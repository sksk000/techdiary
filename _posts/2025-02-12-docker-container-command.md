---
title: "コンテナ内のコマンド操作について理解"
last_modified_at: 2025-02-12T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

- 前提として、新しく ubuntu のコンテナを用意し、コンテナ内で適当なファイルを作成する。

```
>docker container run -it ubuntu:20.04

root@1c0ba068ad88:/# echo "Hello World" > hello.txt
root@1c0ba068ad88:/# cat hello.txt
Hello World
// hello.txtをルートに用意し、Hello Worldを記述する。
```

- コンテナが作成されているか確認し、コンテナをリスタートする。

```
> docker container ls -a
CONTAINER ID   IMAGE          COMMAND                   CREATED         STATUS                      PORTS
                 NAMES
1c0ba068ad88   ubuntu:20.04   "/bin/bash"               5 minutes ago   Exited (0) 13 seconds ago
                 recursing_bartik

> docker container restart 1c0ba068ad88
1c0ba068ad88
// コンテナのステータスをUpに変更

> docker container ls
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS         PORTS     NAMES
1c0ba068ad88   ubuntu:20.04   "/bin/bash"   7 minutes ago   Up 5 seconds             recursing_bartik
// コンテナが起動されていることを確認
```

- `docker container exec <コンテナID> ls`を実行し、先程作成したコンテナ内のディレクトリ情報を表示する。

```
> docker container exec 1c0ba068ad88 ls
bin
boot
dev
etc
hello.txt
home
lib
lib32
lib64
libx32
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
// 先ほど作成したhello.txtが表示されていることがわかる。

> docker container exec -it 1c0ba068ad88 ls
bin   dev  hello.txt  lib    lib64   media  opt   root  sbin  sys  usr
boot  etc  home       lib32  libx32  mnt    proc  run   srv   tmp  var
// 更に、-itを付けることで上記のコマンド実行よりきれいにlsの結果が表示されている事がわかる。

> docker container exec -it 1c0ba068ad88 bash

root@1c0ba068ad88:/#
root@1c0ba068ad88:/# ls
bin   dev  hello.txt  lib    lib64   media  opt   root  sbin  sys  usr
boot  etc  home       lib32  libx32  mnt    proc  run   srv   tmp  var
// bashでコンテナへのアクセスも可能
```

### `docker container run`と`docker container exec`の違い

- `docker container run <イメージ名> <実行したいコマンド>`
  - イメージからコンテナを新規に作成しながらコマンドを実行する。
- `docker container exec <コンテナ名> <指定したコンテナ内で実行したいコマンド>`
  - 既存のコンテナに対してコマンドを実行する

### コンテナに名前を付ける

- 名前を指定しない限り、docker 側がコンテナに名前を付ける。

```
> docker container ls -a
CONTAINER ID   IMAGE          COMMAND                   CREATED          STATUS                      PORTS
                  NAMES
1c0ba068ad88   ubuntu:20.04   "/bin/bash"               54 minutes ago   Up 47 minutes
                  recursing_bartik
72cd1a8e7c12   ubuntu:20.04   "hoge"                    40 hours ago     Created
                  admiring_shirley
// NAMESを見ると、勝手に名前がつけられている事がわかる。
```

- `docker container run --name <コンテナ名>`を実行することで、コンテナに名前を付けることができる。

```
> docker container run --name my_ubnuntu ubuntu:20.04

> docker container ls -a
CONTAINER ID   IMAGE          COMMAND                   CREATED          STATUS                      PORTS
                  NAMES
566bae48796f   ubuntu:20.04   "/bin/bash"               20 seconds ago   Exited (0) 19 seconds ago
                  my_ubnuntu
// 先ほど命名したコンテナ名がNAMESに設定されていることがわかる。
```
