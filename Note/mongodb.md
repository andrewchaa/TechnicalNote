#mongo db

## Getting started
### Connect to a mongo daemon
```
mongo
```

### Select Database
```
show dbs
use mydb
db // show the current db

show collections
```



## CRUD


### Remove documents
#### Remove documents that matches a condition
```javascript
db.measures.remove({ unit : null })
```