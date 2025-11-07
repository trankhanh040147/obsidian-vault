base.go: 
```
package request  
  
import (  
    "context"  
    "net/http"    "strconv"  
    "github.com/panjf2000/ants/v2"    "github.com/segmentio/kafka-go"  
    "tla-ai-bridge/common/configure"    "tla-ai-bridge/common/constants"    "tla-ai-bridge/common/local"    "tla-ai-bridge/common/logging"    "tla-ai-bridge/utilities/apm"    "tla-ai-bridge/utilities/protobuf")  
  
var (  
    cfg        = configure.GetConfig()  
    logger     = logging.GetLogger()  
    logService = newLogServiceDefault()  
    mapMessage = make(map[string]context.CancelFunc)  
    queueChat  = make(chan *protobuf.KafkaPacketAssistantSendMessage, cfg.NumConcurrencyProcess)  
    queueStop  = make(chan *protobuf.KafkaPacketAssistantSendMessage, cfg.NumConcurrencyProcess)  
)  
  
type requestHandler struct {  
    ctx  context.Context  
    data []byte  
}  
  
func newRequestHandler(ctx context.Context, data []byte) *requestHandler {  
    return &requestHandler{  
       ctx:  ctx,  
       data: data,  
    }  
}  
  
func HandlerRequest(ctx context.Context, msg kafka.Message) error {  
    maxTask := 1  
    handler := newRequestHandler(ctx, msg.Value)  
    errChan := make(chan error, maxTask)  
    go func() {  
       errChan <- handler.handleRequest()  
    }()  
    for i := 0; i < maxTask; i++ {  
       if err := <-errChan; err != nil {  
          return err  
       }  
    }    return nil  
}  
  
func Init() {  
    if cfg.ElasticAPMEnable {  
       logService = apm.GetGlobal()  
    }  
    go runQueue(queueChat)  
    go runQueue(queueStop)  
}  
  
func Stop() {  
    close(queueChat)  
    close(queueStop)  
}  
  
func runQueue(queue chan *protobuf.KafkaPacketAssistantSendMessage) {  
    p, _ := ants.NewPoolWithFunc(cfg.NumConcurrencyProcess, func(data interface{}) {  
       msg := data.(*protobuf.KafkaPacketAssistantSendMessage)  
       switch msg.Meta.Cmd {  
       case constants.ClientCMDAssistantStop:  
          if err := handleStop(msg); err != nil {  
             logger.Error().Err(err).Str("function", "runQueue").Str("functionInLine", "handleStop").Msg("request-controller")  
          }  
       case constants.ClientCMDAssistantMessage:  
          tx := logService.StartTransaction(cfg.KafkaTopicTlaRequest, "event")  
          defer logService.EndTransaction(tx)  
          ctx := local.InitToContext(context.Background())  
          if err := handleMessage(ctx, msg); err != nil {  
             logService.LogError(tx, err)  
             logService.SetHTTPStatusCode(tx, http.StatusInternalServerError)  
             logger.Error().Err(err).Str("function", "runQueue").Str("functionInLine", "handleMessage").Msg("request-controller")  
          }  
          logService.SetHTTPStatusCode(tx, http.StatusOK)  
          localService, _ := local.GetFromContext(ctx)  
          logService.SetCustom(tx, "response_body", localService.GetResponseBody())  
          logService.SetCustom(tx, "request_body", localService.GetRequestBody())  
          logService.SetCustom(tx, "extra_body", localService.GetExtraBody())  
          logService.SetLabel(tx, "username", localService.GetUsername())  
          logService.SetLabel(tx, "platform", strconv.Itoa(int(msg.Meta.Platform)))  
       }  
    })  
    defer p.Release()  
    for {  
       msg, ok := <-queue  
       if !ok {  
          break  
       }  
       _ = p.Invoke(msg)  
    }  
}
```

- service.go:
```
package request  
  
import (  
    "context"  
    "encoding/base64"    "errors"    "fmt"    "net/http"    "time"  
    "github.com/bytedance/sonic"    "github.com/google/uuid"    "github.com/segmentio/kafka-go"    "google.golang.org/protobuf/proto"  
    "tla-ai-bridge/common/local"  
    "tla-ai-bridge/common/constants"    "tla-ai-bridge/database/mongo/models"    "tla-ai-bridge/database/mongo/queries"    kafkaTool "tla-ai-bridge/utilities/kafka"  
    "tla-ai-bridge/utilities/packing"    "tla-ai-bridge/utilities/protobuf"    "tla-ai-bridge/utilities/tla_client")  
  
func (h *requestHandler) handleRequest() error {  
    packet := new(protobuf.KafkaPacketAssistantSendMessage)  
    if err := proto.Unmarshal(h.data, packet); err != nil {  
       logger.Error().Err(err).Str("function", "handleRequest").Str("functionInLine", "proto.Unmarshal").Msg("request-controller")  
       return err  
    }  
    if packet.Type != constants.PacketTypeAssistant {  
       logger.Warn().Err(errors.New("invalid packet type")).Str("function", "handleRequest").Int32("packet", packet.Type).Msg("request-controller")  
       return nil  
    }  
    logger.Debug().Interface("packet", packet).Msg("handleRequest")  
    switch packet.Meta.Cmd {  
    case constants.ClientCMDAssistantMessage:  
       queueChat <- packet  
    case constants.ClientCMDAssistantStop:  
       queueStop <- packet  
    default:  
       err := fmt.Errorf("invalid cmd %v", packet.Meta.Cmd)  
       logger.Error().Err(err).Str("function", "handleRequest").Msg("requestMessage")  
       return err  
    }  
    return nil  
}  
  
func packingAndSendPacket(data PacketAssistantDataResponse, packet *protobuf.KafkaPacketAssistantSendMessage, notNotify, noAck bool) error {  
    packet.Meta.NotNotify = notNotify  
    packet.Nack = noAck  
    packet.PacketTime = time.Now().UnixMilli()  
    messageId := uuid.New()  
    assistantResponseData, err := sonic.Marshal(data)  
    if err != nil {  
       logger.Error().Err(err).Str("function", "packingAndSendPacket").Str("functionInline", "sonic.Marshal").Msg("request-controller")  
       return err  
    }  
    lowLevelPackingDataByte, err := packing.New().LowLevelPacking(constants.ClientCMDAssistantMessage, int(messageId.ID()), assistantResponseData)  
    if err != nil {  
       logger.Error().Err(err).Str("function", "packingAndSendPacket").Str("functionInline", "packingService.LowLevelPacking").Msg("request-controller")  
       return err  
    }  
    packet.Data = base64.StdEncoding.EncodeToString(lowLevelPackingDataByte)  
    kafkaDataByte, err := proto.Marshal(packet)  
    if err != nil {  
       logger.Error().Err(err).Str("function", "packingAndSendPacket").Str("functionInline", "proto.Marshal").Msg("request-controller")  
       return err  
    }  
    if err = kafkaTool.GetGlobalWriter().SendMessage(context.Background(), kafka.Message{  
       Topic: cfg.KafkaTopicTlaResponse, Value: kafkaDataByte,  
    }); err != nil {  
       logger.Error().Err(err).Str("function", "packingAndSendPacket").Str("functionInline", "kafkaTool.GetGlobalWriter().SendMessage").Msg("request-controller")  
       return err  
    }  
    return nil  
}  
  
//nolint:gocyclo  
func handleMessage(ctx context.Context, msg *protobuf.KafkaPacketAssistantSendMessage) error {  
    var (  
       conversationAlias                   string  
       conversationId                      string  
       responseMessage                     string  
       responseAiMessageId                 string  
       textGenerationContent               string  
       textGenerationCounter               int  
       sequence                            int  
       isRunning                           = true  
       packetTime                          = time.UnixMilli(msg.PacketTime)  
       ticker                              = time.NewTicker(cfg.TLAResponseMessageTimeout)  
       messageResponseAttachments          = make([]models.MessageResponseDataAttachedFile, 0)  
       procedureOptions                    = make([]models.MessageResponseProcedureOption, 0)  
       referenceUrls                       = make([]models.MessageResponseDataReference, 0)  
       packetAssistantDataAttachedFiles    = make([]PacketAssistantDataAttachedFile, 0)  
       packetAssistantDataProcedureOptions = make([]PacketAssistantDataProcedureOption, 0)  
       packetAssistantDataReferences       = make([]PacketAssistantDataReference, 0)  
       messageQuery                        = queries.NewMessage(ctx)  
       conversationQuery                   = queries.NewConversation(ctx)  
       queryOption                         = queries.NewOptions()  
       localService, _                     = local.GetFromContext(ctx)  
    )    if msg.Meta.Cid != "" {  
       queryOption.SetOnlyFields("conv_id", "title")  
       conversation, err := conversationQuery.GetByConvId(msg.Meta.Cid, queryOption)  
       if err != nil {  
          logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "conversationQuery.GetByConvId").Msg("request-controller")  
          return err  
       }  
       conversationAlias = conversation.Title  
       conversationId = conversation.ConversationId  
    }  
    var request PacketAssistantDataRequestMessage  
    //packingService := packing.New()  
    //unpackedData, err := packingService.LowLevelUnpacking(msg.Data)    //if err != nil {    // localService.SetRequestBody(msg.Data)    // logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "packingService.LowLevelUnpacking").Msg("request-controller")    // return err    //}    //localService.SetRequestBody(string(unpackedData))    //if err = sonic.Unmarshal(unpackedData, &request); err != nil {    // logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "sonic.Unmarshal").Msg("request-controller")    // return err    //}    queryOption.SetOnlyFields("_id", "sip_id")  
    user, err := queries.NewUser(ctx).GetByUsername(msg.From, queryOption)  
    if err != nil {  
       logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "queries.NewUser.GetByUsername").Msg("request-controller")  
       return err  
    }  
    localService.SetUsername(user.Username)  
    msg.To = user.Username  
    msg.From = cfg.AIAssistantServiceIdentity  
    eventChan := make(chan tla_client.ChatResponseData, cfg.PaginationMaxItem)  
    newCtx, cancelCtx := context.WithCancel(ctx)  
    messageKey := fmt.Sprintf("%s_%s", msg.To, msg.Meta.Mid)  
    defer func() {  
       cancelCtx()  
       close(eventChan)  
    }()  
    if _, exists := mapMessage[messageKey]; exists {  
       return nil  
    }  
    mapMessage[messageKey] = cancelCtx  
    defer delete(mapMessage, messageKey)  
    go tla_client.GetGlobal().MakePostRequestWithSSE(newCtx, tla_client.ChatRequestBody{  
       Text:           request.Message,  
       ConversationId: msg.Meta.Cid,  
    }, eventChan)  
  
    defer ticker.Stop()  
    for isRunning {  
       select {  
       case <-newCtx.Done():  
          newMessage := models.Message{  
             MessageId:      msg.Meta.Mid,  
             ConversationId: conversationId,  
             UserId:         user.Id,  
             Request: &models.MessageRequestData{  
                CreatedAt: packetTime,  
                Data: models.MessageRequestDataData{  
                   Message: request.Message,  
                },  
             },  
             Response: &models.MessageResponseData{  
                CreatedAt: time.Now(),  
                Data: models.MessageResponseDataData{  
                   Answer:           responseMessage,  
                   AttachedFiles:    messageResponseAttachments,  
                   ReferenceUrls:    referenceUrls,  
                   ProcedureOptions: procedureOptions,  
                   AiMessageId:      responseAiMessageId,  
                },  
             },  
          }  
          responseData := PacketAssistantDataResponse{  
             EventType:         constants.TLAEventTypeStreamEnd,  
             MessageAnswer:     responseMessage,  
             MessageText:       "",  
             MessageId:         msg.Meta.Mid,  
             ConversationId:    conversationId,  
             ConversationAlias: conversationAlias,  
             AttachedFiles:     packetAssistantDataAttachedFiles,  
             ProcedureOptions:  packetAssistantDataProcedureOptions,  
             ReferenceUrls:     packetAssistantDataReferences,  
             Code:              constants.ReturnCodeClientCMDAssistantSuccess,  
             Cmd:               msg.Meta.Cmd,  
             Sequence:          sequence,  
          }  
          if err = packingAndSendPacket(responseData, msg, false, false); err != nil {  
             logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "h.packingAndSendPacket").Msg("request-controller")  
          }  
          localService.SetResponseBody(responseData)  
          if conversationId != "" {  
             if err = messageQuery.CreateOne(newMessage); err != nil {  
                logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "messageQuery.CreateOne").Msg("request-controller")  
                return err  
             }  
             if err = conversationQuery.UpdateUpdatedAtByConvId(conversationId, time.Now()); err != nil {  
                logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "conversationQuery.UpdateUpdatedAtByConvId").Msg("request-controller")  
                return err  
             }  
          }          return nil  
       case <-ticker.C:  
          newMessage := models.Message{  
             MessageId:      msg.Meta.Mid,  
             ConversationId: conversationId,  
             UserId:         user.Id,  
             Request: &models.MessageRequestData{  
                CreatedAt: packetTime,  
                Data: models.MessageRequestDataData{  
                   Message: request.Message,  
                },  
             },  
             Response: &models.MessageResponseData{  
                CreatedAt: time.Now(),  
                Data: models.MessageResponseDataData{  
                   Answer:           responseMessage,  
                   AttachedFiles:    messageResponseAttachments,  
                   ReferenceUrls:    referenceUrls,  
                   ProcedureOptions: procedureOptions,  
                   AiMessageId:      responseAiMessageId,  
                },  
             },  
          }  
          responseData := PacketAssistantDataResponse{  
             EventType:         constants.TLAEventTypeStreamEnd,  
             MessageAnswer:     responseMessage,  
             MessageText:       "",  
             MessageId:         msg.Meta.Mid,  
             ConversationId:    conversationId,  
             ConversationAlias: conversationAlias,  
             AttachedFiles:     packetAssistantDataAttachedFiles,  
             ProcedureOptions:  packetAssistantDataProcedureOptions,  
             ReferenceUrls:     packetAssistantDataReferences,  
             Code:              constants.ReturnCodeClientCMDAssistantSuccess,  
             Cmd:               msg.Meta.Cmd,  
             Sequence:          sequence,  
          }  
          if sequence <= 1 {  
             responseData.Code = constants.ReturnCodeClientCMDAssistantTimeout  
             if err = packingAndSendPacket(responseData, msg, true, false); err != nil {  
                logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "packingAndSendPacket").Msg("request-controller")  
                return err  
             }  
             localService.SetResponseBody(responseData)  
             return nil  
          }  
          if err = packingAndSendPacket(responseData, msg, false, false); err != nil {  
             logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "packingAndSendPacket").Msg("request-controller")  
          }  
          localService.SetResponseBody(responseData)  
          if conversationId != "" {  
             if err = messageQuery.CreateOne(newMessage); err != nil {  
                logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "messageQuery.CreateOne").Msg("request-controller")  
                return err  
             }  
             if err = conversationQuery.UpdateUpdatedAtByConvId(conversationId, time.Now()); err != nil {  
                logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "conversationQuery.UpdateUpdatedAtByConvId").Msg("request-controller")  
                return err  
             }  
          }          return nil  
       case event, ok := <-eventChan:  
          if !ok {  
             return nil  
          }  
          if event.HttpCode != http.StatusOK {  
             if errors.Is(event.Err, context.DeadlineExceeded) && sequence > 0 {  
                ticker.Reset(time.Millisecond)  
                break  
             }  
             responseData := PacketAssistantDataResponse{  
                EventType:         constants.TLAEventTypeStreamEnd,  
                MessageAnswer:     "",  
                MessageText:       "",  
                MessageId:         msg.Meta.Mid,  
                ConversationId:    conversationId,  
                ConversationAlias: conversationAlias,  
                AttachedFiles:     packetAssistantDataAttachedFiles,  
                ProcedureOptions:  packetAssistantDataProcedureOptions,  
                ReferenceUrls:     packetAssistantDataReferences,  
                Code:              constants.ReturnCodeClientCMDAssistantInternalError,  
                Cmd:               msg.Meta.Cmd,  
                Sequence:          sequence,  
             }  
             if err = packingAndSendPacket(responseData, msg, true, false); err != nil {  
                logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "h.packingAndSendPacket").Msg("request-controller")  
                return err  
             }  
             localService.SetResponseBody(responseData)  
             return event.Err  
          }  
          responsePacket := PacketAssistantDataResponse{  
             EventType:         constants.TLAEventTypeStreamEnd,  
             MessageAnswer:     "",  
             MessageText:       "",  
             MessageId:         msg.Meta.Mid,  
             ConversationId:    conversationId,  
             ConversationAlias: conversationAlias,  
             AttachedFiles:     packetAssistantDataAttachedFiles,  
             ProcedureOptions:  packetAssistantDataProcedureOptions,  
             ReferenceUrls:     packetAssistantDataReferences,  
             Code:              constants.ReturnCodeClientCMDAssistantSuccess,  
             Cmd:               msg.Meta.Cmd,  
          }  
          switch event.EventType {  
          case constants.TLAEventTypeStreamStart:  
             sequence++  
             responsePacket.Sequence = sequence  
             responsePacket.EventType = constants.TLAEventTypeStreamStart  
             if conversationId == "" {  
                conversationId = event.ConversationId  
                conversationAlias, err = tla_client.GetGlobal().GenerateTitle(ctx, tla_client.GenerateTitleRequestBody{  
                   Text:           request.Message,  
                   ConversationId: conversationId,  
                })  
                if err != nil {  
                   logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "tla_client.GetGlobal().GenerateTitle").Msg("request-controller")  
                   conversationAlias = cfg.TLADefaultConversationAlias  
                }  
                if _, err = conversationQuery.Create(models.Conversation{  
                   Title:          conversationAlias,  
                   ConversationId: conversationId,  
                   UserId:         user.Id,  
                }); err != nil {  
                   logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "conversationQuery.Create").Msg("request-controller")  
                   return err  
                }  
                responsePacket.ConversationId = event.ConversationId  
                responsePacket.ConversationAlias = conversationAlias  
             }  
             if err = packingAndSendPacket(responsePacket, msg, true, true); err != nil {  
                logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "packingAndSendPacket").Msg("request-controller")  
                return err  
             }  
             localService.SetResponseBody(responsePacket)  
          case constants.TLAEventTypeTextGeneration:  
             responsePacket.EventType = constants.TLAEventTypeTextGeneration  
             textGenerationCounter++  
             textGenerationContent += event.Text  
             responseMessage += event.Text  
             if textGenerationCounter >= cfg.ResponseTextGenerationBatchSize {  
                sequence++  
                responsePacket.Sequence = sequence  
                responsePacket.MessageText = textGenerationContent  
                textGenerationContent = ""  
                if err = packingAndSendPacket(responsePacket, msg, true, true); err != nil {  
                   logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "packingAndSendPacket").Msg("request-controller")  
                   return err  
                }  
                localService.SetResponseBody(responsePacket)  
             }  
             textGenerationCounter %= cfg.ResponseTextGenerationBatchSize  
          case constants.TLAEventTypeStreamEnd:  
             isRunning = false  
             sequence++  
             responsePacket.Sequence = sequence  
             responsePacket.EventType = constants.TLAEventTypeStreamEnd  
             responsePacket.MessageText = event.Text  
             responsePacket.MessageAnswer = event.Answer  
             responseAiMessageId = event.AiMessageId  
             for _, url := range event.ReferenceUrls {  
                referenceUrls = append(referenceUrls, models.MessageResponseDataReference{  
                   Title: url.Title,  
                   Url:   url.Url,  
                })  
                packetAssistantDataReferences = append(packetAssistantDataReferences, PacketAssistantDataReference{  
                   Title: url.Title,  
                   Url:   url.Url,  
                })  
             }  
             for _, procedureOption := range event.ProcedureOptions {  
                procedureOptions = append(procedureOptions, models.MessageResponseProcedureOption{  
                   Key:   procedureOption.Key,  
                   Value: procedureOption.Value,  
                })  
                packetAssistantDataProcedureOptions = append(packetAssistantDataProcedureOptions, PacketAssistantDataProcedureOption{  
                   Key:   procedureOption.Key,  
                   Value: procedureOption.Value,  
                })  
             }  
             for _, attachedFile := range event.AttachedFiles {  
                messageResponseAttachments = append(messageResponseAttachments, models.MessageResponseDataAttachedFile{  
                   FileName: attachedFile.FileName,  
                   FilePath: attachedFile.FilePath,  
                })  
                packetAssistantDataAttachedFiles = append(packetAssistantDataAttachedFiles, PacketAssistantDataAttachedFile{  
                   FileName: attachedFile.FileName,  
                   FilePath: attachedFile.FilePath,  
                })  
             }  
             createdAt := time.Now()  
             if conversationId != "" {  
                if err = messageQuery.CreateOne(models.Message{  
                   MessageId:      msg.Meta.Mid,  
                   ConversationId: conversationId,  
                   UserId:         user.Id,  
                   Request: &models.MessageRequestData{  
                      CreatedAt: createdAt,  
                      Data:      models.MessageRequestDataData{Message: request.Message},  
                   },  
                   Response: &models.MessageResponseData{  
                      CreatedAt: createdAt,  
                      Data: models.MessageResponseDataData{  
                         ProcedureOptions: procedureOptions,  
                         Answer:           event.Answer,  
                         AttachedFiles:    messageResponseAttachments,  
                         ReferenceUrls:    referenceUrls,  
                         AiMessageId:      responseAiMessageId,  
                      },  
                   },  
                }); err != nil {  
                   logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "messageQuery.CreateOne").Msg("request-controller")  
                   return err  
                }  
                if err = conversationQuery.UpdateUpdatedAtByConvId(conversationId, time.Now()); err != nil {  
                   logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "conversationQuery.UpdateUpdatedAtByConvId").Msg("request-controller")  
                   return err  
                }  
             } else {  
                responsePacket.Code = constants.ReturnCodeClientCMDAssistantInternalError  
             }  
             responsePacket.ProcedureOptions = packetAssistantDataProcedureOptions  
             responsePacket.AttachedFiles = packetAssistantDataAttachedFiles  
             responsePacket.ReferenceUrls = packetAssistantDataReferences  
             if err = packingAndSendPacket(responsePacket, msg, false, false); err != nil {  
                logger.Error().Err(err).Str("function", "handleMessage").Str("functionInline", "packingAndSendPacket").Msg("request-controller")  
                return err  
             }  
             localService.SetResponseBody(responsePacket)  
          }  
       }    }    return nil  
}  
  
func handleStop(msg *protobuf.KafkaPacketAssistantSendMessage) error {  
    messageKey := fmt.Sprintf("%s_%s", msg.From, msg.Meta.Mid)  
    if cancelContext, exists := mapMessage[messageKey]; exists {  
       if cancelContext != nil {  
          delete(mapMessage, messageKey)  
          cancelContext()  
       }  
       return nil  
    }  
    mapMessage[messageKey] = nil  
    return nil  
}
```

I want to prevent race condiction for map messages