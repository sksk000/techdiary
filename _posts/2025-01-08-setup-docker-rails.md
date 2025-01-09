---
title: "dockerにてrailsの開発環境時に躓いたこと"
last_modified_at: 2025-01-07T00:00:00+0900
tags:
  - RubyonRails
  - Docker
---

## 開発環境
- Docker

## 問題
- 以下のエラーが出ており、正常にサイトを開くことが出来なかった。エラー文にあるmanifest.jsonをコンテナ内を確認すると、作成されていなかった。
```
Webpacker can't find application.js in /app/public/packs/manifest.json. Possible causes:
1. You want to set webpacker.yml value of compile to true for your environment
   unless you are using the `webpack -w` or the webpack-dev-server.
2. webpack has not yet re-run to reflect updates.
3. You have misconfigured Webpacker's config/webpacker.yml file.
4. Your webpack configuration is not creating a manifest.
Your manifest contains:
{
}
```

## やったこと

- DockerFileにて、webpacker:install、webpacker:compileを行った。

```
FROM ruby:3.1.2

RUN apt update

#git
RUN apt install git

# node.js v16インストール（yarnがサポートしているバージョンに合わせる）
RUN curl -fsSL https://deb.nodesource.com/setup_16.x | bash - \
    && apt-get install -y nodejs

# yarnのインストール
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
    && echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list \
    && apt-get update && apt-get install -y yarn

#gemでrailsをイントール
RUN gem install rails -v 6.1.4

#プロジェクトをgitからClone
RUN git clone プロジェクト名 app

#以下の実行コマンドを/appで行う
WORKDIR /app

#gemや依存ファイルのインストール
RUN bundle install

#これを行うことでmanifest.jsonにJSのファイルが登録される
RUN rails webpacker:install
RUN npx update-browserslist-db@latest
RUN rails webpacker:compile

RUN yarn install

RUN bin/webpack

# DBの登録とテストデータ挿入
RUN rails db:create
RUN rails db:migrate
RUN rails db:seed

EXPOSE 3000
CMD ["bundle", "exec", "rails", "s", "-b", "0.0.0.0"]

```
