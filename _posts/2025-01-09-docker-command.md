---
title: "Dockerで頻繁に使うコマンドの整理"
last_modified_at: 2025-01-09T00:00:00+0900
tags:
  - Docker
---

## 開発環境
- Docker

## コマンド一覧
##### 起動関連
- docker-compose.ymlに登録したサービスをバックグランドで起動する。
```
$ docker compose up -d
$ docker compose up -d --build

// --build : ビルドしてから起動する
// -d : shellのバックグラウンドで起動する
```
##### 停止関連
- コンテナの削除、コンテナの停止
```
$ docker compose down
$ docker compose stop

// down : コンテナを削除する。コンテナ上の永続化されていないデータは消える。
// stop : コンテナを停止する。停止したコンテナは後ほど start で再開できる。
```

##### 操作関連
- コンテナのshellへの接続
  - docker compose 管理のサービス名は、docker-compose.ymlに書かれている

```
$ docker compose exec -it サービス名 コマンド

$ docker compose exec -it web /bin/bash

// コマンドは、コンテナの内部で実行したいコマンドのこと。
```

##### 状況確認
- コンテナの起動状況や概要の確認
```
$ docker compose ps
$ docker compose ps -a

// -a : 起動や停止の状態に関係なく、docker-compose.ymlに記載のあるすべてのサービスについて表示する
// 未指定の場合は、起動しているコンテナのみ表示する。
```

# 検証環境の場合

# dockerhubから取得し実行する
# DockerHubからダウンロードし、バックグラウンドでimageを動作させます。
$ docker run -itd image名 /bin/sh

# コンテナ名またはコンテナIDを使用し、コンテナ内でコマンドを実行する
docker exec -it コンテナ名またはコンテナID コマンド

```
