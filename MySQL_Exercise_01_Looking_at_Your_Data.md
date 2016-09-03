# MySQL Exercise 1: Welcome to your first notebook!


```python
# Load the SQL library
%load_ext sql
# Connect the database you need to use
%sql mysql://studentuser:studentpw@mysqlserver/dognitiondb
%sql USE dognitiondb
# Get to know your data
%sql SHOW tables
%sql SHOW columns FROM (enter table name here)
%sql SHOW columns FROM (enter table name here) FROM (enter database name here)
%sql SHOW columns FROM databasename.tablename

```
```sql
USE dognitiondb
SHOW tables
SHOW columns FROM (enter table name here)
SHOW columns FROM (enter table name here) FROM (enter database name here)
SHOW columns FROM databasename.tablename
```

























