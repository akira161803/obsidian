
Date: 2025-09-24
Tags: 
Links:  [[WSA]], [[File Upload Vulnerabilities]]

**ディレクトリの設定ファイルをアップロードしてバイパス**

***

1. phpファイルはアップロードできない。
2. response header でサーバーはapacheとわかる。
3. **ディレクトリの設定ファイルはアップロードできるかもしれない。**
4. 設定ファイルをアップロードする。`.htaccess`: 
```
AddType application/x-httpd-php .php .foo
```
これで.fooファイルを.phpファイルとして扱えるようになる。
5. webshell.fooをアップロードしてアクセス。

***
# References

https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-extension-blacklist-bypass