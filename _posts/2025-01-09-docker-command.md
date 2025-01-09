---
title: "Dockerで頻繁に使うコマンド"
last_modified_at: 2024-09-19T00:00:00+0900
tags:
  - Docker
---

## 開発環境
- Docker

## コマンド一覧
```
# バックグランドでdocker-compose.ymlに登録したサービスを起動する。
$ docker compose up -d

# バックグランドでdocker-compose.ymlに登録したサービスをビルドし、起動する。
# ビルドしてから起動したい場合はこっちを使う。
$ docker compose up -d --build

# コンテナを停止し、イメージを削除する
$ docker compose down

# コンテナを停止するだけ
$ docker compose stop

# コンテナに入る
# サービス名はdocker-compose.ymlに書かれている
# コマンドはコンテナ内で実行するコマンド
$ docker compose exec -it サービス名 コマンド

# コンテナ状況一覧
# docker-compose.ymlで書かれているすべてのサービスを表示する。
$ docker compose ps -a

# docker-compose.ymlで書かれている起動中のサービスを表示する。
$ docker compose ps


# 検証環境の場合

# dockerhubから取得し実行する
# DockerHubからダウンロードし、バックグラウンドでimageを動作させます。
$ docker run -itd image名 /bin/sh

# コンテナ名またはコンテナIDを使用し、コンテナ内でコマンドを実行する
docker exec -it コンテナ名またはコンテナID コマンド

```
