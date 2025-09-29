
Date: 2025-09-28
Tags: 
Links: [[WSA]], [[Information Disclosure]]

***

### Information disclosure due to insecure configuration

Websites are sometimes vulnerable as a result of improper configuration. This is especially common due to the widespread use of third-party technologies, whose vast array of configuration options are not necessarily well-understood by those implementing them.

In other cases, developers might forget to disable various debugging options in the production environment. For example, the HTTP `TRACE` method is designed for diagnostic purposes. If enabled, the web server will respond to requests that use the `TRACE` method by echoing in the response the exact request that was received. This behavior is often harmless, but occasionally leads to information disclosure, such as the name of internal authentication headers that may be appended to requests by reverse proxies.

## Lab : 

Burp Repeaterで、`GET /admin`にアクセスします。レスポンスから、管理パネルは管理者としてログインしているか、ローカルIPからリクエストされた場合にのみアクセス可能であることが開示されます。  
リクエストを再度送信しますが、今度は`TRACE`メソッドを使用します：

`TRACE /admin`  
レスポンスを調査します。あなたのIPアドレスを含む`X-Custom-IP-Authorization`ヘッダーが、リクエストに自動的に付加されていることに注目してください。これは、リクエストがローカルホストのIPアドレスから来たものかどうかを判断するために使用されます。  
Proxy > Match and replace に移動します。  
HTTP match and replace rules の下にある Add をクリックします。Add match/replace rule ダイアログが開きます。  
Match フィールドは空のままにします。  
Type の下で、Request header が選択されていることを確認します。  
Replace フィールドに、以下を入力します：

`X-Custom-IP-Authorization: 127.0.0.1`  
Test をクリックします。  
Auto-modified request の下で、Burpが変更後のリクエストに`X-Custom-IP-Authorization`ヘッダーを追加したことに注目してください。  
OK をクリックします。これで、Burp Proxyはあなたが送信するすべてのリクエストに`X-Custom-IP-Authorization`ヘッダーを追加するようになります。  
ホームページにアクセスします。管理パネルにアクセスできるようになり、そこでcarlosを削除できることに注目してください。


***
# References

https://portswigger.net/web-security/information-disclosure/exploiting/lab-infoleak-authentication-bypass