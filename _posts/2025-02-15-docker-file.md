---
title: "Dockerfileについて理解する"
last_modified_at: 2025-02-15T00:00:00+0900
tags:
  - Docker
---

## 学んだこと
### DockerFileとは
- カスタマイズしたイメージを作るための設計書のようなものを指す。

### なぜDockerFileが必要なのか
- DockerHubにアップされているイメージの大半は最低限の機能しか入っていない。そのためDockerFileにてカスタマイズを行う必要がある。

### Dockerfileからイメージを作成する
- 以下の様なDockerfileを用意する。
```
FROM ubuntu:20.04
```
- `docker image build <Dockerfileのパス>`でDockerfileをビルドする。
```
> docker image build .
[+] Building 2.3s (6/6) FINISHED                                                                                                                                                                      docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                                  0.0s
 => => transferring dockerfile: 54B                                                                                                                                                                                   0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                                       0.0s
 => [internal] load .dockerignore                                                                                                                                                                                     0.1s
 => => transferring context: 2B                                                                                                                                                                                       0.0s
 => [1/1] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                                 1.8s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                                 1.7s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                                         0.0s
 => exporting to image                                                                                                                                                                                                0.2s
 => => exporting layers                                                                                                                                                                                               0.0s
 => => exporting manifest sha256:278aacb75efc99a3b17defcf418c36dc7eb2c67a70ac52ffbee67a85bb27bb09                                                                                                                     0.0s
 => => exporting config sha256:87af5eee5007c28014f37917c1939cd89bbafbd87a253829582941b9b60456f1                                                                                                                       0.0s
 => => exporting attestation manifest sha256:378fb02e5c0c59ad227b81628df9fe5860a0dd23252cd98f2f5cd444012f5987                                                                                                         0.1s
 => => exporting manifest list sha256:19eef52202bf816e7e56c34474a08c845265862521e737c67b769562d631cd92                                                                                                                0.0s
 => => naming to moby-dangling@sha256:19eef52202bf816e7e56c34474a08c845265862521e737c67b769562d631cd92                                                                                                                0.0s
 => => unpacking to moby-dangling@sha256:19eef52202bf816e7e56c34474a08c845265862521e737c67b769562d631cd92                                                                                                             0.0s
```
- `docker image ls`でイメージが作成されているか確認する。
```

//イメージIDが19eef52202bf816e7e56c34474a08c845265862521e737c67b769562d631cd92のデータがあるか確認

PS D:\testdocker> docker image ls
REPOSITORY      TAG       IMAGE ID       CREATED        SIZE
<none>          <none>    19eef52202bf   4 months ago   109MB
// 同じイメージIDがあることを確認した。
```
- 上記の状態だと確認しずらいので、`docker image build -t <名前> <Dockerfileのパス>`でイメージに名前を付ける。
```
> docker image build -t my-image:v1 .
// 名前の後ろに:vとあるが、これはバージョンを示す。バージョンもこのコマンド実行時に任意で設定できる。

[+] Building 2.1s (6/6) FINISHED                                                                                                                                                                      docker:desktop-linux
 => => transferring dockerfile: 54B                                                                                                                                                                                   0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                                       0.0s
 => [internal] load .dockerignore                                                                                                                                                                                     0.0s
 => => transferring context: 2B                                                                                                                                                                                       0.0s
 => CACHED [1/1] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                          1.6s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                                 1.6s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                                         0.0s
 => exporting to image                                                                                                                                                                                                0.1s
 => => exporting layers                                                                                                                                                                                               0.0s
 => => exporting manifest sha256:278aacb75efc99a3b17defcf418c36dc7eb2c67a70ac52ffbee67a85bb27bb09                                                                                                                     0.0s
 => => exporting config sha256:87af5eee5007c28014f37917c1939cd89bbafbd87a253829582941b9b60456f1                                                                                                                       0.0s
 => => exporting attestation manifest sha256:1261d18f69733ebfe4fa6d88390f69407b7dac50a9e878ebb1a6bf2347e57a67                                                                                                         0.0s
 => => exporting manifest list sha256:4d80da5b0b1b561592ad09d80c6b0debef8239b307685b45b8b4289f1178bcbf                                                                                                                0.0s
 => => naming to docker.io/library/my-image:v1                                                                                                                                                                        0.0s
 => => unpacking to docker.io/library/my-image:v1                                                                                                                                                                     0.0s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/ztjh9ax8gdcs2tbs0xdtbmou6
> docker image ls
REPOSITORY      TAG       IMAGE ID       CREATED        SIZE
nginx           latest    91734281c0eb   9 days ago     279MB
ubuntu          latest    72297848456d   2 weeks ago    117MB
hello-world     latest    d715f14f9eca   3 weeks ago    20.4kB
travelog-web    latest    94b4d35f2d51   5 weeks ago    2.87GB
<none>          <none>    988b84f1e2bb   5 weeks ago    2.73GB
<none>          <none>    f79879d5befc   5 weeks ago    2.73GB
travelog-1910   latest    2a5ffa083a7b   5 weeks ago    2.73GB
<none>          <none>    c7b7af1dee85   5 weeks ago    2.79GB
<none>          <none>    239f2e08ca04   5 weeks ago    2.79GB
<none>          <none>    83853a2e6cf5   5 weeks ago    2.87GB
<none>          <none>    ab724cfc350e   5 weeks ago    2.78GB
<none>          <none>    afb4cf737a6a   5 weeks ago    2.78GB
<none>          <none>    4e892dbc5cd8   5 weeks ago    2.8GB
<none>          <none>    7f6e768c6f41   5 weeks ago    2.31GB
my-image        v1        4d80da5b0b1b   4 months ago   109MB
<none>          <none>    19eef52202bf   4 months ago   109MB
ubuntu          20.04     8e5c4f0285ec   4 months ago   109MB
ruby            3.1.2     7681a3d37560   2 years ago    1.32GB
```
