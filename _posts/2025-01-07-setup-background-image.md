---
title: "railsにて背景画像を適用する方法"
last_modified_at: 2025-01-07T00:00:00+0900
tags:
  - RubyonRails
---

## 開発環境

- Rails 6.1.7.8

## 問題

- 以下のパスにて、main タグに background-image を設定後、背景画像が設定されなかった。

```
# 該当のパス
プロジェクト名/app/javascript/stylesheets/application.scss

# CSS
main {
  flex: 1;
  margin: 0;
  padding: 0;
  background-image: url("<%= asset_path('hogehoge.png') %>")
}
```

## やったこと

- application.html.erb の main タグに直接記述した。
- 他方法があれば、記事にする。

```
<main class="pb-5"
 style=
 "background-image: url('<%= asset_path('hogehoge.png') %>');
  background-color:rgba(255,255,255,0.5);
  background-blend-mode:lighten;">
  <%= yield %>
</main>
```

## 参考 URL

- https://qiita.com/knxrvb/items/c78c08a0a3c9d4095f3f
