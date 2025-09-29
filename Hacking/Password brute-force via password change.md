
Date: 2025-09-26
Tags: 
Links:  [[WSA]], [[Authentication Vulnerabilities]]

***

脆弱性
1. パスワード変更欄でユーザー名を書き換えれられる。`username=wiener&current-password=123456789&new-password-1=aaa&new-password-2=kokoko`
2. new-password1と2で同じものを入力する、かつ、間違ったパスワードを入力する。
   →2回間違えるとログインページに飛ばされる。
   が、new-password1と2で違うものを入力すると、何回でも試行できる。
   正しいパスワードと間違ったパスワードでエラーメッセージが異なるから、そこをexploitできる。

***
# References

https://portswigger.net/web-security/authentication/other-mechanisms/lab-password-brute-force-via-password-change