---
title: "RubyOnRailsにてDeviseのメッセージをtoastrで表示したい"
last_modified_at: 2025-02-07T00:00:00+0900
tags:
  - RubyonRails
---

## 開発環境

- Rails 6.1.7.8
- Ruby 3.1.2p20
  - ユーザ認証として Devise を使用

## 問題

- Devise のメッセージをそのまま View に表示しており、視認性が低い。

## やったこと

### 導入手順

- Gemfile にて`gem "toastr-rails"`を追加しコマンドラインにて`bundle install`を行う。
- `app/javascript/packs/application.js`に`import toastr from 'toastr';`を追加。
- `app/javascript/packs/application.js`に`window.toastr = toastr;`を追加。
- `app/javascript/stylesheets/application.scss`に`@import './node_modules/toastr/build/toastr.min';`を追加。

### View 側の設定

- toastr の表示設定を行う。
- `app/views/layouts/application.html.erb`に下記を追加(記述場所はどこでも問題ない。)

```
// Deviseのalertやnotice時にトースター表示を行うように設定
<% if flash.any? %>
  <% flash.each do |key, value| %>
    // Devise側で通知したメッセージがalertやnoticeの場合
    // alert->error notice->successに設定
    <% key = "error" if key == "alert" %>
    <% key = "success" if key == "notice" %>
    <script>
      //先ほど設定したkeyとメッセージをtoastrに設定しトースター表示を行う。
      toastr['<%= key %>']('<%= value %>');
    </script>
  <% end %>
<% end %>
```

### toastr の設定

- toastr のレイアウトを変更したい場合は、オプション設定を`app/javascript/packs/application.js`で行う必要がある。
- レイアウト設定の項目は以下のサイトにデモがあるため確認すると良い。
- https://codeseven.github.io/toastr/demo.html

```
import toastr from 'toastr';
･･･
//オプション設定
toastr.options = {
  "positionClass": "toast-top-full-width",
  "closeButton": true,
  "progressBar": true,
}

//オプション設定完了後、以下の処理を行う。
window.toastr = toastr;
```

### 細かいレイアウト調整方法
- CSS側で直接参照し、以下のようにレイアウトを調整する。
- 他にも調整したい箇所がある場合は、デモサイトにて確認しながら調整すると良い。
- https://codeseven.github.io/toastr/demo.html
```
.toast{
  max-width: 100%;
}

#toast-container > div{
  opacity: 1;
}
```

## 参考 URL
- https://codeseven.github.io/toastr/demo.html
- https://qiita.com/Uuya/items/0766c96143881ab53cd7
