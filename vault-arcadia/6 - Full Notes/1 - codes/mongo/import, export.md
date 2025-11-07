
**List URI as env**
```bash
URI_LOCAL
URI_DEV_1
URI_DEV_2
URI_STG_1
URI_STG_2
```


**navigate**
```bash
cd /media/veracrypt1/db
```
---
**import**

- local 
```bash
mongoimport --uri="mongodb://localhost:27017" --db db_api --collection contacts --file db_api/contacts.json
```

```bash
mongoimport --uri="mongodb://mongo-admin:AwlfhFQMtH9a6u0T@10.1.164.3:30000,10.1.164.4:30000,10.1.164.5:30000" --db db_presence --collection contacts --file db_presence/contacts.json
```

```bash
mongoimport --uri="mongodb://mongo-admin:AwlfhFQMtH9a6u0T@10.1.164.3:30000,10.1.164.4:30000,10.1.164.5:30000/db_presence?connectTimeoutMS=10000&authMechanism=SCRAM-SHA-1&replicaSet=rs0&authSource=admin" --db db_presence --collection contacts --file db_presence/contacts.json
```
---
**export**
```bash
mongoexport --uri=$URI_DEV_1  -d=db_api -c=conversations  -o=dev/conversations.json
```
---