- In mongo-driver: 

```go
// Majority returns a WriteConcern that requests acknowledgment that write// operations have been durably committed to the calculated majority of the  
// data-bearing voting members.  
//  
// Write concern "w: majority" typically requires write operations to be written  
// to the on-disk journal before they are acknowledged, unless journaling is  
// disabled on MongoDB or the "writeConcernMajorityJournalDefault" replica set  
// configuration is set to false.  
//  
// For more information about write concern "w: majority", see  
// https://www.mongodb.com/docs/manual/reference/write-concern/#mongodb-writeconcern-writeconcern.-majority-  
func Majority() *WriteConcern {  
    return &WriteConcern{W: majority}  
}
```
- Majority returns a WriteConcern that requests acknowledgment that write operations have been durably committed to the calculated majority of the  [[data-bearing voting members]].  