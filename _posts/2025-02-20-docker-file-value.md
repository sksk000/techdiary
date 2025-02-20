---
title: "レイヤーを実際に利用するときの意識箇所、Dockerfile内にて環境変数・変数の定義方法を理解"
last_modified_at: 2025-02-20T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### 業務で意識するレイヤー構造とは？

- 今回新たに`RUN apt install -y vim`を追加し、再度ビルドを行った。
  - 再度ビルドを行うと、以下の処理が行われる。
    - 新たに追加したコマンドをビルド。
    - 変更されていないコマンドは、キャッシュとして残っているためビルドは実行されない。
  - そのため、ビルド時間はかからない。
- ビルド後のレイヤーは以下の通り。

```
  CMD ["bash"]
  COPY ./foo.txt
  RUN apt install -y vim
  RUN apt install -y curl
  RUN apt update
  ベースイメージのレイヤー
```

- 必ずしも Dockerfile のコマンドを&&でつなげて圧縮する必要はない
  - 開発状況によって最善の方法を考えること。
    - 流動的にコマンドが増える場合は、`&&`で短縮せずそのまま記述しイメージレイヤーを細かく分けたほうが開発時間をかけずに済む。
    - コマンドが変わらなければ、`&&`でコマンドを短縮し一つのイメージレイヤーで行うことで、イメージのサイズを削減することができる。

### Dockerfile に環境変数を設定する方法

- Dockerfile 内にて`ENV {キー}={値}`と定義すると環境変数を定義することができる。
  - 一行で複数の環境変数を設定することも可能。
    - `ENV {キー1}={値1} {キー2}={値2}`

```
FROM ubuntu:20.04

ENV hello="Hello World"

CMD ["bash"]
```

- 環境変数が定義されているか確認する。

```
> docker image build .

[+] Building 2.3s (6/6) FINISHED                                                                                                                                                     docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => => transferring dockerfile: 97B                                                                                                                                                                  0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                      0.1s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 34B                                                                                                                                                                     0.0s
 => CACHED [1/1] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                         1.7s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.7s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                        0.0s
 => exporting to image                                                                                                                                                                               0.2s
 => => exporting layers                                                                                                                                                                              0.0s
 => => exporting manifest sha256:b7cba77c7e9d47131e0fbefa0b1687a759b36f30f889022f987bc10ef6db3a9f                                                                                                    0.0s
 => => exporting config sha256:5a969e2c6dba93da1b7fed066f1e52c2301fca99a30f3bd084ca75b56a44a096                                                                                                      0.0s
 => => exporting attestation manifest sha256:8f713a38d3d82525ecc3a10cb7f4647afc721262fab87436aa959f25f91ef706                                                                                        0.0s
 => => exporting manifest list sha256:34b614c8c5dc67ce19cb9f5c0af3664657284ba1351526e1ce37e257504cae07                                                                                               0.0s
 => => naming to moby-dangling@sha256:34b614c8c5dc67ce19cb9f5c0af3664657284ba1351526e1ce37e257504cae07                                                                                               0.0s
 => => unpacking to moby-dangling@sha256:34b614c8c5dc67ce19cb9f5c0af3664657284ba1351526e1ce37e257504cae07                                                                                            0.0s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/1dlg6i4x1i05bgkmftudyk8fs
// ビルド成功
```

- `docker container run -it`でコンテナ内に入り、環境変数が定義されているか確認する。
  - `echo $hello`で確認する、環境変数を呼び出す場合は$を先頭に付けて呼び出す。

```
PS D:\testdocker\docker> docker container run -it sha256:34b614c8c5dc67ce19cb9f5c0af3664657284ba1351526e1ce37e257504cae07

root@f6c1fa419199:/# echo $hello
Hello World
```

### ARG とは？

- Dockerfile 内で変数を定義できるコマンド。
- イメージ作成時に利用する変数を渡す際に使用する。
  - `ARG {キー1}={デフォルト値}`で記述する。

### 実際に ARG を使用してみる

- 以下の Dockerfile を用意する。

```
FROM ubuntu:20.04

ARG message
RUN echo $message > message.txt
// messageを定義し、messageの中身をmessage.txtへ書き込む

CMD ["cat", "message.txt"]
// デフォルトコマンドとしてcatで、message.txtの中身を表示する。
```

- `docker image build`でビルドする。

```
PS D:\testdocker\docker> docker image build .
[+] Building 2.6s (7/7) FINISHED                                                                                                                                                     docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => => transferring dockerfile: 132B                                                                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                      0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 34B                                                                                                                                                                     0.0s
 => CACHED [1/2] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                         1.6s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.6s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                        0.0s
 => [2/2] RUN echo $message > message.txt                                                                                                                                                            0.3s
 => exporting to image                                                                                                                                                                               0.4s
 => => exporting layers                                                                                                                                                                              0.2s
 => => exporting manifest sha256:b1009bd26f4c7ec3039866e3dbd4f669f334eab8ee977e20d31d0c053ca054c9                                                                                                    0.0s
 => => exporting config sha256:e7bee5f31baedf05f8e44a83eaaffe385520b06ce5a33594ea5bdd2cece64836                                                                                                      0.0s
 => => exporting attestation manifest sha256:f2e6fa5f44fcaa2cf5bbb7ab1b7b89460884d0f13cf89290c622e20a9307ca9d                                                                                        0.1s
 => => exporting manifest list sha256:938c82fda4f01dd2b912e1365cb5d5270fddf4d415de6330e677d627c4f6d33b                                                                                               0.0s
 => => naming to moby-dangling@sha256:938c82fda4f01dd2b912e1365cb5d5270fddf4d415de6330e677d627c4f6d33b                                                                                               0.0s
 => => unpacking to moby-dangling@sha256:938c82fda4f01dd2b912e1365cb5d5270fddf4d415de6330e677d627c4f6d33b                                                                                            0.0s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/moo5jheswewxd85x9yvpzu9lz
// ビルド成功した。
```

- `docker container run -it`でコンテナを実行する。

```
PS D:\testdocker\docker> docker container run -it sha256:938c82fda4f01dd2b912e1365cb5d5270fddf4d415de6330e677d627c4f6d33b

// 何も表示されなかった。
// 理由としては、message変数に何も渡していないため、空の状態で表示された。
PS D:\testdocker\docker>
```

- 今度は、message 変数に`Hello Message`を代入してみる。
  - `docker image build --build-arg <変数名>=<値> <ビルドコンテキストのパス>`で定義した変数に代入し、ビルドを実行することができる。

```
PS D:\testdocker\docker> docker image build --build-arg message="Hello Message" .
[+] Building 1.1s (6/6) FINISHED                                                                                                                                                     docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => => transferring dockerfile: 132B                                                                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                      0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 34B                                                                                                                                                                     0.0s
 => CACHED [1/2] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                         0.0s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                0.0s
 => [2/2] RUN echo Hello Message > message.txt                                                                                                                                                       0.3s
 => exporting to image                                                                                                                                                                               0.4s
 => => exporting layers                                                                                                                                                                              0.1s
 => => exporting manifest sha256:e7652ed7ac65401d2b1ac9908cbc12e3b986c8ac98db5c2760d34938c25b3a39                                                                                                    0.0s
 => => exporting config sha256:8497a04d08ed2f9e4147f2e7fa92e31c7054b91b798e05c37ad978092230331d                                                                                                      0.0s
 => => exporting attestation manifest sha256:512f2bf5af7c982e16f00f9947911932571a45763db267a170dc8d4859b56469                                                                                        0.0s
 => => exporting manifest list sha256:a3a8c70aec9de4268715c33df5ddc263c9a439b8d1076a153b92256397c4c3e0                                                                                               0.0s
 => => naming to moby-dangling@sha256:a3a8c70aec9de4268715c33df5ddc263c9a439b8d1076a153b92256397c4c3e0                                                                                               0.0s
 => => unpacking to moby-dangling@sha256:a3a8c70aec9de4268715c33df5ddc263c9a439b8d1076a153b92256397c4c3e0                                                                                            0.0s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/yx8sztezksfzrzre59zpd0tzc
```

- コンテナを起動し、`Hello Message`が表示されるか確認する。

```
PS D:\testdocker\docker> docker container run -it sha256:a3a8c70aec9de4268715c33df5ddc263c9a439b8d1076a153b92256397c4c3e0

Hello Message
// 先ほどビルド実行時に定義したHello Messageを表示出来た。

PS D:\testdocker\docker>
```
