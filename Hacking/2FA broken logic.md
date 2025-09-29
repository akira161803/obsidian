
Date: 2025-09-25
Tags: 
Links:  [[WSA]], [[Authentication Vulnerabilities]]

***

脆弱性
1. ユーザー名とパスワードを入力したら二段階認証画面に行くが、そのクッキー値を書き換えれば他のユーザーになりすませる。
2. クッキー値をユーザー名で管理している。
3. session値を消してもOK。
4. 認証コードが予測可能で書き換えられる。

```http
Cookie: verify=carlos; session=FJ1wVrWqhfq2UiWBHDx1aIfPk3RVOE1U

mfa-code=0602
```

mfa-codeをintruderの**brute forcer**で総当たり。

***
# References