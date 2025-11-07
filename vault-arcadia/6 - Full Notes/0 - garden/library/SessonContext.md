
- file: /home/vessel/go/pkg/mod/go.mongodb.org/mongo-driver@v1.17.4/mongo/session.go
```go
// SessionContext combines the context.Context and mongo.Session interfaces. It should be used as the Context arguments// to operations that should be executed in a session.  
//  
// Implementations of SessionContext are not safe for concurrent use by multiple goroutines.//  
// There are two 
type SessionContext interface {  
    context.Context  
    Session}
```
- [[Read Concern]]
---
**Use case**:  
- file: /media/veracrypt1/src/sigpro-stask-api/database/mongo/queries/transactions.go
- function: ApplyStatusTransitionAndWorkflow
---
- Every write query being executed on a [[Majority]] collection
```go
    return s.getSTaskDB().Collection(new(mongoModels.Project).CollectionName(), options.Collection().SetWriteConcern(writeconcern.Majority()))  

```