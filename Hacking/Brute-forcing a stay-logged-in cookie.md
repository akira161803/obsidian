
Date: 2025-09-26
Tags: 
Links:  [[WSA]], [[Authentication Vulnerabilities]]

***
## Lab: 

脆弱性
1. ログインしたときcookie: session=を消してもOK。
2. Cookie: stay-logged-inが推測可能。

writeup
1. **Inspector** でstay-logged-inクッキーをデコード。base64で`wiener:51dc30ddc473d43a6011e9ebba6ca770`
2. 後半はmd5とわかる。
3. intruderの**Payload processing**で
	- Hash: `MD5`
	- Add prefix: `carlos:`
	- Encode: `Base64-encode`
	を追加。


***
# References

https://portswigger.net/web-security/authentication/other-mechanisms/lab-brute-forcing-a-stay-logged-in-cookie