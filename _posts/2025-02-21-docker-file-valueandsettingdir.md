---
title: "DockerfileのARG・ENVの使い分け、Dockerfile内で作業ディレクトリを変更する方法"
last_modified_at: 2025-02-21T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### ARG と ENV の違い

#### ARG

- Dockerfile 内でしか使えない変数
  - そのため、コンテナ実行時にコンテナ側へ影響を及ぼさない。
  - 同じ変数がコンテナ側の環境変数として定義されていたとしても、問題ない。

#### ENV

- Dockerfile 内 やコンテナ内で使用できる変数

### ARG と ENV の違いを確認する

- 以下の Dockerfile を用意

```
FROM ubuntu:20.04

ARG message_arg="Message Arg"
ENV message_env="Message Env"

RUN echo $message_arg > message_arg.txt
RUN echo $message_env > message_env.txt
// 各変数に格納されている文字列を対応するテキストファイルに出力する。

CMD ["bash"]
```

- `docker image build`実行後コンテナに入り、`message_arg.txt`と`message_env.txt`を確認する。

```
// ビルドを省略
PS D:\testdocker\docker> docker container run -it sha256:940c068c88df10cb97dce6a69cf7ebd5f1b6a0ee6265861b1e330a32c33eb53b

root@edb94bc7f788:/# cat message_arg.txt
Message Arg
root@edb94bc7f788:/# cat message_env.txt
Message Env
// message_arg.txtとmessage_env.txtの中身を確認する。
// Dockerfileで設定した文字列がテキストファイルに出力されていることがわかる。
```

- ARG は Dockerfile 内で有効な変数、ENV は Dockerfile 内とコンテナ内で有効な変数
  - この違いについて実際にコンテナ内で変数を呼び出して確認する。

```
root@edb94bc7f788:/# echo $message_env
Message Env
root@edb94bc7f788:/# echo $message_arg

root@edb94bc7f788:/#
// ARGとENVの違いがはっきりした。
// ARGはDockerfile内で定義されているが、コンテナ内では定義されていないため表示されなかった。
// ENVはDockerfile内・コンテナ内で定義されているため表示されている。
```

### ARG と ENV の使い分け方法

#### ENV

- 実行した内容に応じて処理を変更したい場合。

#### ARG

- RUN で何かしらの環境変数にアクセスしその中身を使用し、RUN の中身を変えたい場合。
- Dockerfile 内で完結する変数を定義したい場合。

### Dockerfile 内で作業ディレクトリを変更する方法

- `WORKDIR {ディレクトリパス}`で設定を行う。
  - RUN や CMD などを行う場合の作業ディレクトリを指定する。
  - 未設定の場合は、作業ディレクトリはルートディレクトリになる。

### 実際に作業ディレクトリを設定してみる

- 以下の順番で動作を行う Dockerfile を用意する。
  - ルートディレクトリに 1.txt 作成
  - 作業用ディレクトリを/app/my_dir に変更
  - /app/my_dir に 2.txt を作成
  - 作業用ディレクトリを/app に変更
  - /app に 3.txt を作成

```
FROM ubuntu:20.04

RUN touch 1.txt
WORKFDIR /app/my_dir
RUN touch 2.txt
WORKDIR ..
RUN touch 3.txt

CMD ["bash"]
```

- `docker image build`でビルドする。

```
PS D:\testdocker\docker> docker image build .
[+] Building 4.2s (11/11) FINISHED                                                                                                                                                   docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => => transferring dockerfile: 156B                                                                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                      0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 34B                                                                                                                                                                     0.0s
 => CACHED [1/6] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                         1.6s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.6s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                        0.0s
 => [2/6] RUN touch 1.txt                                                                                                                                                                            0.3s
 => [3/6] WORKDIR /app/my_dir                                                                                                                                                                        0.1s
 => [4/6] RUN touch 2.txt                                                                                                                                                                            0.4s
 => [5/6] WORKDIR ..                                                                                                                                                                                 0.1s
 => [6/6] RUN touch 3.txt                                                                                                                                                                            0.5s
 => exporting to image                                                                                                                                                                               0.9s
 => => exporting layers                                                                                                                                                                              0.6s
 => => exporting manifest sha256:9b09ef1e0bc27fced85b790fe1b060fefc006b6d7ac8338ccb16cb82549f487d                                                                                                    0.0s
 => => exporting config sha256:c60eb02ca05fe9b027cc0c90493b6f0b697064aee36b7615734a24df1ce52fa5                                                                                                      0.0s
 => => exporting attestation manifest sha256:6d26d062a525d2e35ee4473183518dae44cc0b6cea23446b060b8bfc9e7bc9a7                                                                                        0.0s
 => => exporting manifest list sha256:22c2c15c3876cc9dcd6572f919ff0f1a3d9b7596883112b80c0f60370688c89f                                                                                               0.0s
 => => naming to moby-dangling@sha256:22c2c15c3876cc9dcd6572f919ff0f1a3d9b7596883112b80c0f60370688c89f                                                                                               0.0s
 => => unpacking to moby-dangling@sha256:22c2c15c3876cc9dcd6572f919ff0f1a3d9b7596883112b80c0f60370688c89f                                                                                            0.2s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/mr3nbbtrngtj53yzn5ye00j64
// ビルド成功
```

- `docker container run -it`でコンテナ内に入り、Dockerfile で設定した内容を確認する。

```
PS D:\testdocker\docker> docker container run -it sha256:22c2c15c3876cc9dcd6572f919ff0f1a3d9b7596883112b80c0f60370688c89f

root@bc3c3f408c8c:/app# pwd
/app
// カレントディレクトリは/appになっているのは、一番最後作業ディレクトリを/appに設定しているため。

root@bc3c3f408c8c:/app# ls /

1.txt  app  bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
// ルートディレクトリに1.txtが作成されていることを確認。

root@bc3c3f408c8c:/app# ls /app/my_dir

2.txt
// /app/my_dir内に2.txtが作成されていることを確認。

root@bc3c3f408c8c:/app# ls

3.txt  my_dir
// /app内に3.txtとmy_dirディレクトリが作成されていることを確認。

// 上記以外の利点として、作業ディレクトリを変更することでコンテナ起動後、コンテナに入った後のディレクトリを意図的に変更することができる。
```
