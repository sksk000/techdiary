---
title: "railsアプリケーションにfaviconを設定する方法"
last_modified_at: 2025-01-12T00:00:00+0900
tags:
  - RubyonRails
---

## 開発環境

- Rails 6.1.7.8

## 問題

- favicon の設定を行いたい

## やったこと

- favicon 用画像を以下サイトで生成。
  - https://ao-system.net/favicon/
- `app/assets/images/`に favicon.ico を格納
- `app/views/layouts/application.html.erb` に以下のコードを追加する

```
<html>
  <head>
  ...
    <%= favicon_link_tag('favicon.ico') %>
  ...
  </head>
```

## 参考 URL

- https://qiita.com/ntkgcj/items/6643a07a43a36029dc9b
