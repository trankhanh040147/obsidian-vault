**uri**

mongodb://localhost:27017

mongodb://mongo-admin:AwlfhFQMtH9a6u0T@10.1.164.3:30000,10.1.164.4:30000,10.1.164.5:30000/db_api?connectTimeoutMS=10000&authMechanism=SCRAM-SHA-1&replicaSet=rs0&authSource=admin

---
**dump**
https://stackoverflow.com/questions/4880874/how-do-i-create-a-mongodb-dump-of-my-database

mongodump --uri="mongodb://mongodb0.example.com:27017" [additional options]
mongodump --uri="_"  --collection=coll_name --out=dump/coll_name  

mongodump --uri="mongodb://mongo-admin:AwlfhFQMtH9a6u0T@10.1.164.3:30000,10.1.164.4:30000,10.1.164.5:30000/?connectTimeoutMS=10000&authMechanism=SCRAM-SHA-1&replicaSet=rs0&authSource=admin" --db=db_api --collection=users --out=users

---
**restore**

mongorestore -h host.com:port -d dbname_test -u username -p password dump/dbname/

mongorestore --uri="mongodb://localhost:27017" dump


mongoimport --uri="mongodb://localhost:27017" --db db_api --collection contacts --file db_api/contacts.json

mongoimport --uri="mongodb://localhost:27017" --db db_presence --collection contacts --file db_presence/contacts.json

mongoimport --uri="mongodb://localhost:27017" --db db_api --collection otp --file otp.json

---
**export**

mongoexport --uri=mongodb://mongo-admin:AwlfhFQMtH9a6u0T@mongo-stg-key0-1:30000,mongo-stg-key0-2:30000,mongo-stg-key0-3:30000/db_api&authMechanism=SCRAM-SHA-1&replicaSet=rs0&authSource=admin --collection=contacts  --out=db_api/contacts.json
jetbrains-toolbox-2.3.2.31487.tar
---