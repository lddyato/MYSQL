# Looking at Your Data

## step 1: load the SQL library, connect to the database
```python
%load_ext sql
%sql mysql://studentuser:studentpw@mysqlserver/dognitiondb
%sql USE dognitiondb
```

## step 2: get to know your data
```sql
SHOW tables
```
| Tables_in_dognitiondb|
|--------------|
| complete_tests| 
| dogs|
| exam_answers| 
| reviews| 
| site_activities| 
| users| 

## step 3: show how many columns in table
```sql
SHOW columns FROM tablename
SHOW columns FROM tablename FROM databasename
SHOW columns FROM databasename.tablename  
#Whenever you have multiple databases loaded, you will need to specify which database a table comes from using one of the syntax options described above.
SHOW columns FROM dogs
DESCRIBE reviews
DESCRIBE complete_tests
DESCRIBE exam_answers
DESCRIBE site_activities
DESCRIBE users
```
* **Field**: indicates the column name.
* **Type**: indicates the column data type.
* **Collation**: indicates the collation for nonbinary string columns, or NULL for other columns. This value is displayed only if you use the **FULL** keyword.
* **Null**: contains YES if NULL values can be stored in the column, NO if not.
* **Key**: indicates whether the column is indexed:
 + If Key is **empty**, the column either is not indexed or is indexed only as a secondary column in a multiple-column, nonunique index.
 + If Key is **PRI**, the column is a PRIMARY KEY or is one of the columns in a multiple-column PRIMARY KEY.
 + If Key is **UNI**, the column is the first column of a UNIQUE index. (A UNIQUE index permits multiple NULL values, but you can tell whether the column permits NULL by checking the Null field.)
 + If Key is **MUL**, the column is the first column of a nonunique index in which multiple occurrences of a given value are permitted within the column.
 + If more than one of the Key values applies to a given column of a table, Key displays the one with the highest priority, in the order **PRI > UNI > MUL**.
* **UNIQUE**: may be displayed as PRI if it cannot contain NULL values and there is no PRIMARY KEY in the table. A UNIQUE index may display as MUL if several columns form a composite UNIQUE index; although the combination of the columns is unique, each column can still hold multiple occurrences of a given value.
* **Default**: indicates the default value that is assigned to the column. This is NULL if the column has an explicit default of NULL, or if the column definition has no DEFAULT clause.
* **Extra**: contains any additional information that is available about a given column. The value is nonempty in these cases:
 + auto_increment for columns that have the AUTO_INCREMENT attribute
 + on update CURRENT_TIMESTAMP for TIMESTAMP or DATETIME columns that have the ON UPDATE CURRENT_TIMESTAMP attribute
 + VIRTUAL GENERATED or VIRTUAL STORED for generated column
* **Privileges**: indicates the privileges you have for the column. This value is displayed only if you use the FULL keyword.
* **Comment**: indicates any comment the column has. This value is displayed only if you use the FULL keyword.

**Table 1: dogs**
<table>
    <tr>
        <th>Field</th>
        <th>Type</th>
        <th>Null</th>
        <th>Key</th>
        <th>Default</th>
        <th>Extra</th>
    </tr>
    <tr>
        <td>gender</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>birthday</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>breed</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>weight</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>dog_fixed</td>
        <td>tinyint(1)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>dna_tested</td>
        <td>tinyint(1)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>created_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>updated_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>dimension</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>exclude</td>
        <td>tinyint(1)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>breed_type</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>breed_group</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>dog_guid</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>user_guid</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>total_tests_completed</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>mean_iti_days</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>mean_iti_minutes</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>median_iti_days</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>median_iti_minutes</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>time_diff_between_first_and_last_game_days</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>time_diff_between_first_and_last_game_minutes</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
</table>

**Table 2: reviews**

<table>
    <tr>
        <th>Field</th>
        <th>Type</th>
        <th>Null</th>
        <th>Key</th>
        <th>Default</th>
        <th>Extra</th>
    </tr>
    <tr>
        <td>rating</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>created_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>updated_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>user_guid</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>dog_guid</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>subcategory_name</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>test_name</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
</table>

**Table 3: complete_tests**
<table>
    <tr>
        <th>Field</th>
        <th>Type</th>
        <th>Null</th>
        <th>Key</th>
        <th>Default</th>
        <th>Extra</th>
    </tr>
    <tr>
        <td>created_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>updated_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>user_guid</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>dog_guid</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>test_name</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>subcategory_name</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
</table>
**Table 4: site_activities**
<table>
    <tr>
        <th>Field</th>
        <th>Type</th>
        <th>Null</th>
        <th>Key</th>
        <th>Default</th>
        <th>Extra</th>
    </tr>
    <tr>
        <td>activity_type</td>
        <td>varchar(150)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>description</td>
        <td>text</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>membership_id</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>category_id</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>script_id</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>created_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>updated_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>user_guid</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>script_detail_id</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>test_name</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>dog_guid</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
</table>

**Table 5: exam_answers**
<table>
    <tr>
        <th>Field</th>
        <th>Type</th>
        <th>Null</th>
        <th>Key</th>
        <th>Default</th>
        <th>Extra</th>
    </tr>
    <tr>
        <td>script_detail_id</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>subcategory_name</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>test_name</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>step_type</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>start_time</td>
        <td>datetime</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>end_time</td>
        <td>datetime</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>loop_number</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>dog_guid</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
</table>

**Table 6: users**
<table>
    <tr>
        <th>Field</th>
        <th>Type</th>
        <th>Null</th>
        <th>Key</th>
        <th>Default</th>
        <th>Extra</th>
    </tr>
    <tr>
        <td>sign_in_count</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>0</td>
        <td></td>
    </tr>
    <tr>
        <td>created_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>updated_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>max_dogs</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>0</td>
        <td></td>
    </tr>
    <tr>
        <td>membership_id</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>subscribed</td>
        <td>tinyint(1)</td>
        <td>YES</td>
        <td></td>
        <td>0</td>
        <td></td>
    </tr>
    <tr>
        <td>exclude</td>
        <td>tinyint(1)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>free_start_user</td>
        <td>tinyint(1)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>last_active_at</td>
        <td>datetime</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>membership_type</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>user_guid</td>
        <td>text</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>city</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>state</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>zip</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>country</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>utc_correction</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
</table>

## Note
+ SQL syntax and keywords are **case insensitive**.  
+ Table or column names with spaces in them need to be surrounded by quotation marks in SQL.  MySQL accepts both double and single quotation marks, but some database systems only accept single quotation marks.  In all database systems, if a table or column name contains an SQL keyword, the name must be enclosed in backticks instead of quotation marks.
 + ` 'the marks that surrounds this phrase are single quotation marks' `     
 + ` "the marks that surrounds this phrase are double quotation marks" `     
 + `` `the marks that surround this phrase are backticks` ``
+ The semi-colon at the end of a query is only required when you have multiple separate queries saved in the same text file or editor. 

## step 4 Using SELECT to look at your raw data
Let’s take look into the syntax of the SELECT statement:
```sql
SELECT 
    column_1, column_2, ...
FROM
    table_1
[INNER | LEFT |RIGHT] JOIN table_2 ON conditions
WHERE
    conditions
GROUP BY column_1
HAVING group_conditions
ORDER BY column_1
LIMIT offset, length;
```
The SELECT statement consists of several clauses as explained in the following list:

* **SELECT** followed by a list of comma-separated columns or an asterisk (*) to indicate that you want to return all columns.
* **FROM** specifies the table or view where you want to query the data.
* **JOIN** gets data from other tables based on certain join conditions.
* **WHERE** filters rows in the result set.
* **GROUP BY** groups a set of rows into groups and applies aggregate functions on each group.
* **HAVING** filters group based on groups defined by GROUP BY clause.
* **ORDER BY** specifies a list of columns for sorting.
* **LIMIT** constrains the number of returned rows.

```sql
SELECT breed FROM dogs;
```
## step 5 Using LIMIT to restrict the number of rows
```sql
# you will only see the first 5 rows of data you select
SELECT breed FROM dogs LIMIT 5;
# 10 rows of data will be returned, starting at Row 6.
SELECT breed FROM dogs LIMIT 10 OFFSET 5;
SELECT breed FROM dogs LIMIT 5, 10;
```

## step 6 Using SELECT to query multiple columns

```sql
# query with different orders of the column names to observe the differences in output 
SELECT breed,breed_type,breed_group FROM dogs LIMIT 5,10;

# using the wild card to query the reviews table
SELECT * FROM reviews LIMIT 5,10;

# SELECT statements can also be used to make new derivations of individual columns using "+" for addition, "-" for subtraction, "*" for multiplication, or "/" for division.   
SELECT median_iti_minutes / 60 FROM dogs LIMIT 5, 10;

# retrieve the first 15 rows of data from the dog_guid, subcategory_name, and test_name fields of the Reviews table
SELECT dog_guid, subcategory_name, test_name FROM reviews LIMIT 15;

# retrieve 10 rows of data from the activity_type, created_at, and updated_at fields of the site_activities table, starting at row 50
SELECT activity_type, created_at, updated_at FROM site_activities LIMIT 49,10;

# retrieve 20 rows of data from all the columns in the users table, starting from row 2000
SELECT *
FROM users 
LIMIT 1999,20;
FROM site_activities LIMIT 49,10;
```
