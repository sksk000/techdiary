---
title: "DockerfileのCOPYとビルドコンテキストを理解する"
last_modified_at: 2025-02-17T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### Dockerfile 内で好きなファイルをイメージに配置する方法

- `COPY <追加元> <追加先>`で好きなファイルをイメージに配置することができる。
  - 今回 hello.txt をコピーする。
  - hello.txt には`Hello World`という文字列が書かれている。
- 試しに一度 COPY コマンドをコメントアウトし、現在のコンテナ状況を確認する。

```
FROM ubuntu:20.04

RUN apt update
RUN apt install -y curl

# COPY ./hello.txt /app/

```

- 実際に`docker build`して確認する。

```
> docker build .

[+] Building 2.1s (8/8) FINISHED                                                                                                                                                     docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => => transferring dockerfile: 125B                                                                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                      0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 2B                                                                                                                                                                      0.0s
 => [1/3] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.7s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.7s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                        0.0s
 => CACHED [2/3] RUN apt update                                                                                                                                                                      0.0s
 => CACHED [3/3] RUN apt install -y curl                                                                                                                                                             0.0s
 => exporting to image                                                                                                                                                                               0.1s
 => => exporting layers                                                                                                                                                                              0.0s
 => => exporting manifest sha256:695bb9121af8d81a3e0f65f661ac892bb9b2ec8b7e72e4f6e37132d72131b017                                                                                                    0.0s
 => => exporting config sha256:5a7e6d5da57622686e8c7f7b16ce17207986b396430db570a8eaf84768eacc76                                                                                                      0.0s
 => => exporting attestation manifest sha256:de08fbc6afc07a6c148155e138528901be9a9cee6b752158572c06428a77bab8                                                                                        0.0s
 => => exporting manifest list sha256:f5041b0ce1140d42366aa1932eff45b76da92d754ece95209f623a69a3c4762b                                                                                               0.0s
 => => naming to moby-dangling@sha256:f5041b0ce1140d42366aa1932eff45b76da92d754ece95209f623a69a3c4762b                                                                                               0.0s
 => => unpacking to moby-dangling@sha256:f5041b0ce1140d42366aa1932eff45b76da92d754ece95209f623a69a3c4762b                                                                                            0.0s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/t3p2jk11l7k8uvlq4vryzf1c2
// buildを実行すると上記のログが出力される。
// 前回buildを行っているため、buildの結果がキャッシュに残っているため早くbuildを完了することが出来た。

> docker container run -it sha256:4809a7390a70c9701d2a8d0268be17e796ba7347480a8bdf9e82dc17da713758
// 作成したコンテナ内に入る。

root@8e6b4a95e258:/# cd /app
bash: cd: /app: No such file or directory
// DockerfileにてCOPYコマンドを実行していないため、/appのディレクトリは作成されていない事がわかる。
```

- 今度はコメントアウトした COPY コマンドを実行するようにする。

```
FROM ubuntu:20.04

RUN apt update
RUN apt install -y curl

COPY ./hello.txt /app/
```

- Dockerfile を作成したため、再度ビルドを行う

```
> docker image build .
[+] Building 2.4s (10/10) FINISHED                                                                                                                                                   docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => => transferring dockerfile: 123B                                                                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                      0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 2B                                                                                                                                                                      0.0s
 => [1/4] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.6s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.6s
 => [internal] load build context                                                                                                                                                                    0.1s
 => => transferring context: 47B                                                                                                                                                                     0.0s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                        0.0s
 => CACHED [2/4] RUN apt update                                                                                                                                                                      0.0s
 => CACHED [3/4] RUN apt install -y curl                                                                                                                                                             0.0s
 => [4/4] COPY ./hello.txt /app/                                                                                                                                                                     0.1s
 => exporting to image                                                                                                                                                                               0.4s
 => => exporting layers                                                                                                                                                                              0.2s
 => => exporting manifest sha256:4ccbbb7e23c7047d5656f280e2a8c6e3e4204fc7e37d2b86a47a300da41967c7                                                                                                    0.0s
 => => exporting config sha256:537bfac54866c2899d3102d20b47aa448f72757aece2d6391da0b81310a3dfb4                                                                                                      0.0s
 => => exporting attestation manifest sha256:d752c2675219439e8426c8dcce1437d6e5850a947f11840ed87814e75e11c896                                                                                        0.1s
 => => exporting manifest list sha256:e255059ae460031dc5198bbd86c9f1f234eb15aeec907141108892c6304c533e                                                                                               0.0s
 => => naming to moby-dangling@sha256:e255059ae460031dc5198bbd86c9f1f234eb15aeec907141108892c6304c533e                                                                                               0.0s
 => => unpacking to moby-dangling@sha256:e255059ae460031dc5198bbd86c9f1f234eb15aeec907141108892c6304c533e                                                                                            0.1s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/nljo8uruy91fxv5lbyrryu8ud
// COPYコマンド以外はキャッシュされているため、キャッシュが使用される。

> docker container run -it sha256:e255059ae460031dc5198bbd86c9f1f234eb15aeec907141108892c6304c533e
// 再度コンテナ内にアクセスし、確認する。

root@33dec4417497:/# cd /app
// cdコマンドを実行することが出来た。

root@33dec4417497:/app# ls
hello.txt
// コピーしたhello.txtが存在する。

root@33dec4417497:/app# cat hello.txt
Hello World
// ファイル内に書かれている文字列も確認出来た。
```

### COPY したファイルを再度編集するとどうなるか

- `docker build`を行った時点で COPY コマンドは実行されてしまう。
- そのため、`docker build`後に COPY したファイルを編集しても、コンテナ側に反映されない。
- 実際に確認してみる。
  - 事前に hello.txt 内の文字列を`Hello World`から`こんにちは世界`に変更した。

```
root@251a001b9116:/app# cat hello.txt

Hello Worldroot@251a001b9116:/app#
// catコマンドでファイル内を確認すると変更前のHello Worldになっている事がわかる。
```

### ビルドコンテキストとは？

- ビルドに必要なファイル達(テキストファイルやアーカイブファイルなど)のことを示す。
- `docker image build <Dockerfileのパス>`の`<Dockerfileのパス>`がビルドコンテキストのパスを表す
- 上記のコマンドは正確には Dockerfile とビルドコンテキストからイメージを作成するコマンドになる。
  - Dockerfile≠ ビルドコンテキストパス

### Docker の仕組み

- Docker には dockerCLI・RESTAPI・docker デーモンが存在する。
  - 例として、`docker image build`が実行された場合、渡したディレクトリのパスにある Dockerfile やコンテキストを固める。
  - dockerCLI から REST API を経由して固めた Docker ファイルとコンテキストを渡し、docker デーモンがビルドを実行する。
  - docker デーモンが独立しても動かせるような仕組みになっている。
    - 例えば dockerCLI が別の PC に存在しており、docker デーモンがクラウドに存在するとなったとしても docker を動かすことが可能。
    - docker デスクトップをインストールした場合は dockerCLI・RESTAPI・docker デーモン一式がインストールされる。

### docker デーモンに送らないデータを Dockerfile で指定するとどうなるか確認

- 前提として以下のフォルダ構成で試す。

```

    ディレクトリ: D:\testdocker

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        2025/02/17     20:40                docker
-a----        2025/02/17     18:02             14 hello.txt

// testdocker内にはdockerフォルダと、hello.txtファイルが存在する。

    ディレクトリ: D:\testdocker\docker


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        2025/02/17     17:42             86 Dockerfile
// dockerフォルダにはDockerfileが格納されている。
```

- `docker image build`を docker フォルダで行う。

```
D:\testdocker\docker> docker image build .
[+] Building 0.4s (8/8) FINISHED                                                                                                                                                     docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.1s
 => => transferring dockerfile: 123B                                                                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                      0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.1s
 => => transferring context: 2B                                                                                                                                                                      0.0s
 => CANCELED [1/4] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                       0.1s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                0.1s
 => [internal] load build context                                                                                                                                                                    0.1s
 => => transferring context: 2B                                                                                                                                                                      0.0s
 => CACHED [2/4] RUN apt update                                                                                                                                                                      0.0s
 => CACHED [3/4] RUN apt install -y curl                                                                                                                                                             0.0s
 => ERROR [4/4] COPY ./hello.txt /app/                                                                                                                                                               0.0s
------
 > [4/4] COPY ./hello.txt /app/:
------
Dockerfile:6
--------------------
   4 |     RUN apt install -y curl
   5 |
   6 | >>> COPY ./hello.txt /app/
--------------------
ERROR: failed to solve: failed to compute cache key: failed to calculate checksum of ref p9wioi4f7vmh5p3xj6peim4by::rd28codvanxlzd3l6vw8vdrfg: "/hello.txt": not found
// hello.txtというファイルが存在しないとエラーが出ている。

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/g155ukfz9dnye9vyya17orekw
// DockerfileとDockerfileのフォルダ内にあるデータしか渡されていない事がわかった。
// 今回渡しているものはDockerfileしかないため、ビルド失敗になっている。
```

- 成功するには以下を行うと成功する。
  - カレンドディレクトリを D:\testdocker に変更
  - ビルド時に Dockerfile のパスとコンテキストのパスを別々で設定する。
    - `docker image build -f docker/Dockerfile .`で実行すると、Dockerfile のパスとコンテキストのパスを別々で設定することができる。

```
PS D:\testdocker> docker image build -f docker/Dockerfile .
[+] Building 2.3s (10/10) FINISHED                                                                                                                                                   docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => => transferring dockerfile: 123B                                                                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                      0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 2B                                                                                                                                                                      0.0s
 => [1/4] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.6s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.6s
 => [internal] load build context                                                                                                                                                                    0.0s
 => => transferring context: 50B                                                                                                                                                                     0.0s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                        0.0s
 => CACHED [2/4] RUN apt update                                                                                                                                                                      0.0s
 => CACHED [3/4] RUN apt install -y curl                                                                                                                                                             0.0s
 => [4/4] COPY ./hello.txt /app/                                                                                                                                                                     0.1s
 => exporting to image                                                                                                                                                                               0.3s
 => => exporting layers                                                                                                                                                                              0.1s
 => => exporting manifest sha256:e0a39019ea0c0dab4d0e751673cde5e5273b66bcf49279505c45b85b13e756df                                                                                                    0.0s
 => => exporting config sha256:0f287f36a887f94d59544d858086190a6cbb625dca7ff7e499e9d3c385a49132                                                                                                      0.0s
 => => exporting attestation manifest sha256:6d961ed83f69edabbe487750b3cdc8d4ff1123424962297b30d16075c8d56bac                                                                                        0.0s
 => => exporting manifest list sha256:da7b5a1ea261fef0b48e6e5a41de99f9311c6ea3d66b47883de7e216357bc6ff                                                                                               0.0s
 => => naming to moby-dangling@sha256:da7b5a1ea261fef0b48e6e5a41de99f9311c6ea3d66b47883de7e216357bc6ff                                                                                               0.0s
 => => unpacking to moby-dangling@sha256:da7b5a1ea261fef0b48e6e5a41de99f9311c6ea3d66b47883de7e216357bc6ff                                                                                            0.0s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/8kjr86a8a97c0g7raa6gud47w
// 成功することが出来た。
```
