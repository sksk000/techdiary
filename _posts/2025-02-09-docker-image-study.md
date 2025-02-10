---
title: "Dockerのイメージを理解"
last_modified_at: 2025-02-09T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### Docker イメージとは

- コンテナ ≠ イメージ
- コンテナに必要なソフトウェアや環境変数などの設定の情報が入っているもの
- イメージを元にコンテナを作成する

### Docker のイメージの利点

- 配布やコンテナ環境の再現を行うことができる。
  - イメージ自体は変更不可能。

### Docker イメージの取得方法

- DockerHub か DockerFile で入手可能

### DockerHub について

- Docker イメージを登録・配布を行えるサイト。
- 基本的には DockerHub からイメージを取得する。
  - あまりにもマイナー(ダウンロード数やお気に入り数等が少ない)なイメージは注意したほうが良い。

### コンテナのライフサイクル

- コンテナはステータス情報を持っている。
  - Created
    - 作成後はこのステータスになる、コンテナの起動は行われていない。
  - Up
    - コマンドを実行することができる。
  - Exited
    - 実行したいコマンドは終了する。
    - コンテナは削除されず存在する。この状態で Up に遷移することができる。

### イメージのダウンロード・破棄

- `docker image pull ubuntu`を実行し、ubuntu の image をインストールする。

```
>docker image pull ubuntu
// tagを設定しなければ、最新のタグのものをインストールする。

Using default tag: latest
latest: Pulling from library/ubuntu
5a7813e071bf: Download complete
Digest: sha256:72297848456d5d37d1262630108ab308d3e9ec7ed1c3286a32fe09856619a782
Status: Downloaded newer image for ubuntu:latest
docker.io/library/ubuntu:latest

>docker image ls

REPOSITORY      TAG       IMAGE ID       CREATED       SIZE
ubuntu          latest    72297848456d   12 days ago   117MB
hello-world     latest    d715f14f9eca   2 weeks ago   20.4kB
･･･
// ubuntuをインストールされていることを確認出来た。
// repository: イメージ名
// tag: イメージのバージョン

```

- 今度は、`docker image pull ubuntu:20.04`を実行しインストールを行う。

```
>docker image pull ubuntu:20.04
// タグ指定している。

20.04: Pulling from library/ubuntu
d9802f032d67: Download complete
Digest: sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b
Status: Downloaded newer image for ubuntu:20.04
docker.io/library/ubuntu:20.04
// 実行すると、今度はubuntu:20.04がダウンロードされる。

>docker image ls

REPOSITORY      TAG       IMAGE ID       CREATED        SIZE
ubuntu          latest    72297848456d   12 days ago    117MB
ubuntu          20.04     8e5c4f0285ec   4 months ago   109MB
// tagが異なっていることが確認できた。

```

- `docker image rm`でイメージの削除を行う。

```
>docker image rm ubuntu

Untagged: ubuntu:latest
Deleted: sha256:72297848456d5d37d1262630108ab308d3e9ec7ed1c3286a32fe09856619a782

// tagを指定していないため、ubuntu:latestが削除された。

>docker image ls

REPOSITORY      TAG       IMAGE ID       CREATED        SIZE
ubuntu          20.04     8e5c4f0285ec   4 months ago   109MB

// ubuntu:20.04のみになっている事がわかる。
```

- `docker image rm <イメージID>`でイメージ ID を指定し削除を行う。

```
>docker image rm 8e5c4f0285ec

Untagged: ubuntu:20.04
Deleted: sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b

>docker image ls
REPOSITORY      TAG       IMAGE ID       CREATED       SIZE
// イメージが削除されたことを確認。
```
