---
title: "railsアプリケーションにてUTCをJSTに変更する方法"
last_modified_at: 2025-01-13T00:00:00+0900
tags:
  - RubyonRails
---

## 開発環境

- Rails 6.1.7.8

## 問題

- 投稿日時を表示すると、UTC で表示されていた。

```
# View処理
<%= @post.created_at %>

# サイト
2024-12-20 14:19:13 UTC
```

## やったこと

- 以下の様に created_at の前に l を付ける

```
<%= l @post.created_at %>
```

- `アプリ名/config/application.rb` に以下を追加する。

```
module アプリ名
  class Application < Rails::Application
    # ↓の2行を追加する
    config.i18n.default_locale = :ja
    config.time_zone = 'Tokyo'
  end
end

```

- `アプリ名/config/locales/`以下に ja.yml を作成し、以下を追加する。
- 今回は 2025 年 01 月 13 日 10:00 のように日時を表示する。

```
ja:
  time:
    formats:
      default: "%Y/%m/%d %H:%M"
```

## 参考 URL

- https://qiita.com/jnchito/items/831654253fb8a958ec25
