
Date: 2025-09-27
Tags: 
Links:  [[WSA]], [[OS Command Injection]]

***

 There is a writable folder at:

`/var/www/images/`

The application serves the images for the product catalog from this location. You can redirect the output from the injected command to a file in this folder, and then use the image loading URL to retrieve the contents of the file.

1. http history見ると、`/image?filename=aaaa.png`のように画像ファイルを取得している。
2. `email=||whoami>/var/www/images/output.txt||`送る。
3. **`/image?filename=output.txt`でアクセス。**

**`/var/www/images/`はサーバー側の話であって、URLでアクセスするパスは違う。**



***
# References

https://portswigger.net/web-security/os-command-injection/lab-blind-output-redirection




