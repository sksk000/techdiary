---
title: "RubyonRailsでパンくずリストを作成する方法"
last_modified_at: 2025-02-06T00:00:00+0900
tags:
  - RubyonRails
---

## 開発環境

- Rails 6.1.7.8
- Ruby 3.1.2p20

## 問題

- パンくずリストの実装方法を知りたい

## やったこと

### パンくずリストとは

- ユーザがサイト利用時に、今どのページにいるのかわかりやすくするための機能。

### 導入方法

- Gemfile に`gem 'gretel'`を追加し、コマンドラインで`bundle install`を行う。
- コマンドラインで`rails generate gretel:install`を実行
- 上記完了後、`config/breadcrumb.rb`が作成される。この設定ファイルにパンくずリストの設定を行う。

### View の設定

- 以下のコードを`app/views/aplication.html.erb`に配置する。
- 配置箇所にパンくずリストが表示される。

```
<%= breadcrumbs separator: " &rsaquo; " %>
```

### パンくずリストの設定

- `config/breadcrumb.rb`を編集し、パンくずリストとして表示される各パンくずを設定する。
- 例として以下の通りにパンくずを作成する。

```
# ルート
crumb :root do
  link "Home", root_path
end

# ユーザ一覧
crumb :tag do
  link 'ユーザ一覧', users_path
  # 親設定、ルートが親になる
  parent :root
end

# マイページ
crumb :user_show do
  link "ユーザ名", mypage_path(user.id)
  # 親設定、ユーザ一覧が親となる
  parent :tag
end

```
- 編集後、マイページへ遷移すると以下のパンくずリストが表示される
  - Home->ユーザ一覧->マイページ

## 参考 URL

- https://qiita.com/mmaumtjgj/items/95a3b25f9b08da004997
