---
title: "certbotセットアップ"
last_modified_at: 2025-01-06T00:00:00+0900
tags:
  - Linux
---

## 開発環境

- Amazon Linux2

## やったこと

- EC2 インスタンスが属しているセキュリティグループのインバウンドルールに、HTTPS を追加する。(0.0.0.0/0 で設定)

- vi で nginx の config ファイルを変更。location/の一番後ろに「 proxy_set_header X-FORWARDED_PROTO https;」を追加。完了後、certbot 起動

```
$ vi /etc/nginx/conf.d/xxxx.conf
$ sudo certbot --nginx
```

起動後質問事項に答える

- 通知用メールアドレス
  - アドレス入力
- 利用規約
  - Y 入力
- Certbot の運営団体にメールを共有するか
  - N 入力
- HTTPS 化するドメイン名を選択
  - 記載されているドメイン名の前に数値があるため、その数値を入力
