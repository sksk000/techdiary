---
title: "コンテナのデタッチドモードとフォアグラウンドモード"
last_modified_at: 2025-02-14T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### フォアグラウンドモードとは？

- コマンドプロンプト上にコンテナ内の出力結果等が表示される。
- `docker container run -it <イメージ名>`でフォアグラウンドモードでコンテナを起動することができる。

```
>docker container run -it ubuntu

Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
5a7813e071bf: Download complete
Digest: sha256:72297848456d5d37d1262630108ab308d3e9ec7ed1c3286a32fe09856619a782
Status: Downloaded newer image for ubuntu:latest
root@febcd11becc6:/#
```

### デタッチドモードとは？

- バックグラウンドでコンテナが起動する。
- `docker container run -itd <イメージ名>`でバックグランドでコンテナが起動する。

```
> docker container run -itd ubuntu
// docker container run -it -d ubuntuと同じ意味、省略すると上記のコマンドになる。

737f725fe36aed132137a5345ad60437bd8a77c641559722a5a26fe758a87a0b
```

- 先ほどの ubuntu が起動しているか確認

```
> docker container ls

CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
9b5f5f5fe626   ubuntu    "/bin/bash"   2 minutes ago   Up 2 minutes             cranky_yalow
// バックグラウンドでコンテナが起動していることがわかる。
```

### デタッチモードのコンテナへ接続したい場合

- `docker contaner attach <コンテナ名>`でデタッチモードのコンテナへ接続する。

```
> docker container attach 737f725fe36a

root@737f725fe36a:/#
// コンテナ内に入ることが出来た。
```

### デタッチモードやフォアグラウンドモードの使い分け

- コンテナ内で処理をループしているような場合は、デタッチモードでバックグラウンドモードで起動することが良いと思った。
- 処理がすぐ終了するようなコンテナであれば、フォアグラウンドモードでコンテナに接続するのが良いと思った。
