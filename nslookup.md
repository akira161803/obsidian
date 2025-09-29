
Date: 2025-09-29
Tags: 
Links: [[Commands]]

***

**nslookup (Name Server Lookup)** は、DNS サーバに問い合わせを行って **ホスト名 ↔ IP アドレスの対応を調べるためのコマンド** です。

「ドメイン名から IP アドレスを引く」「IP アドレスから逆引きでドメイン名を調べる」といった用途に使われます。

---

## **主な用途**

1. ドメイン名から IP アドレスを調べる

```
nslookup example.com
```

1. → example.com の IP アドレスが返ってくる
2. **IP アドレスからホスト名を調べる（逆引き）**

```
nslookup 8.8.8.8
```

2. → dns.google などのホスト名が返ってくる
3. **特定の DNS サーバに問い合わせる**

```
nslookup example.com 8.8.8.8
```

3. → Google Public DNS (8.8.8.8) に直接問い合わせる

---

## **出力の例**

```
$ nslookup example.com
Server:   8.8.8.8
Address:  8.8.8.8#53

Non-authoritative answer:
Name:    example.com
Address: 93.184.216.34
```

- Server … 問い合わせをした DNS サーバ
- Address … そのサーバの IP
- Non-authoritative answer … キャッシュなど権威以外の回答
- Name と Address … 対応するホスト名と IP

---
## **注意点**

- nslookup は昔からあるツールですが、今では **dig** や **host** コマンドの方が詳細な情報を取得できるので、ネットワーク調査ではそちらを使うことも多いです。
- ただし、Windows には標準で nslookup が入っているため、クロスプラットフォームでよく使われます。

---

