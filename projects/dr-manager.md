
**Feature**
[17/05] - [02/06]
- [x] API: CompareByCollections
- [x] API: CompareByDatabase
- [x] API: SyncByCollections

[02/06] - [08/06]
- [x] API: List Collections By Database Id
- [x] API: Delete database
- [x] FEAT: handle index _id
	- [x] not allow field = _id when create, update
	- [x] skip sync _id index

[23/06] - [29/06]
- [ ] Implement job queue
- [ ] Frontend
	- [ ] update page Datbase
	- [ ] update page Collections
	- [ ] update page Indexes
	- [ ] update page CompareIndex
- [ ] FEAT: hỗ trợ tính năng index TEXT 
   - [ ] 1. Cho phép người dùng thêm index dạng TEXT
   - [ ] 2. Cho phép người dùng đồng bộ các index đã có
   - [ ] 3. Đồng bộ các index dạng TEXT
- [ ] FEAT: thêm nút reload ở màn hình compare
- [ ] Import Index
- [ ] Export Index
- [ ] loading sử dụng component **LoaderComponent**
- [ ] **UPGRADE**
	- [ ] List Database: query & sorting
**Page /Databases**
- [ ] Thêm nút Copy: Database URI 
- [ ] Rút gọn cột Database URI
- [ ] code: add index dr_manager's db
- [ ] [FE] choose-database: click select


---
**Bug**
- [ ] BUG: tính năng import existing indexes
- [ ] BUG: export indexes 
- [ ] BUG: log out khi nhấn logo
- [ ] sync: auto reload sync history
- [ ] sync: button reload màn hình sync database
- [ ] create-db: URI cho option vd /authSource=admin
- [ ] BUG: tính năng import existing indexes  
- [ ] BUG: export indexes 
- [ ] BUG: log out khi nhấn logo
---
- [ ] Compare Indexes
	- [ ] check connection after select database
	- [ ] sort collections
- [ ] Collections dashboard
	- [ ] check unique indexes
- [ ] add index type: text

---
**22/05/2025**
- [x] Fix: check unique key.fields when create, update
- [x] API: Delete Index

---
### DRAFT

COMPARE_BY_COLLECTIONS
**manager**
```
{
"name": 1,
"age": 1,
unique
}
```
->**REDUNDANT** 
--> name_1_age_1_unique
```
{
"number":1 
}
```

**client**
```
{
"name": 1,
"age": 1
}
```
-> **MISSING**  
--> name_1_age_1_unique
```
{
"number":1 
}
```
