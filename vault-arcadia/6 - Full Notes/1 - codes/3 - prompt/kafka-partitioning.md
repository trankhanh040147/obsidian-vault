```
func (h *messageKafkaHandler) handleMessage() (err error) {  
    var assistantPacket PacketAssistant  
    if err = sonic.Unmarshal(h.data, &assistantPacket); err != nil {  
       logger.Error().Err(err).Str("function", "handleMessage").Str("functionInLine", "sonic.Unmarshal").Msg("gossip-message-controller")  
       return err  
    }  
    msgProtoBytes, err := proto.Marshal(&protobuf.KafkaPacketAssistantSendMessage{  
       Version:    assistantPacket.Version,  
       Type:       assistantPacket.Type,  
       PacketId:   assistantPacket.PacketId,  
       PacketTime: assistantPacket.PacketTime,  
       Data:       assistantPacket.Data,  
       From:       assistantPacket.From,  
       To:         assistantPacket.To,  
       Meta: &protobuf.PacketAssistantMetadata{  
          Cmd:       assistantPacket.Metadata.Cmd,  
          Platform:  assistantPacket.Metadata.Platform,  
          Cid:       assistantPacket.Metadata.ConvId,  
          Mid:       assistantPacket.Metadata.MessageId,  
          NotNotify: assistantPacket.Metadata.NotNotify,  
       },  
    })  
    if err != nil {  
       logger.Error().Err(err).Str("function", "handleMessage").Str("functionInLine", "proto.Marshal").Msg("gossip-message-controller")  
       return err  
    }  
    if err = kafkaTool.GetGlobalWriter().SendMessage(context.Background(), kafka.Message{  
       Topic: configure.GetConfig().KafkaTopicTlaRequest,  
       Key:   []byte(assistantPacket.Metadata.MessageId),  
       Value: msgProtoBytes,  
    }); err != nil {  
       logger.Error().Err(err).Str("function", "handleMessage").Str("functionInLine", "kafkaTool.GetGlobalWriter.SendMessage").Msg("gossip-message-controller")  
       return err  
    }  
    return nil  
  
}
```

I want to directing message into partitions base on message id, so I set Key as MessageId, but it not working as I expected (messages is shared among consumers (parititions))