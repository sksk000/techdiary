---
title: "Dockerのレイヤー構造を理解する"
last_modified_at: 2025-02-19T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### レイヤー構造とは？

- 読み取り専用の改装的なデータの単位を表す。

#### イメージレイヤーとは？

- Dockerfile 内の記述事に作成される層のことを表す。
  - レイヤーは読み取り専用
  - レイヤーにはイメージの変更差分が保存される。

#### レイヤーについて実際に確認

- 以下の Dockerfile 用意する。

```
FROM rails:5

RUN touch hoge.txt
CMD ["ls", "-la"]
// ベースイメージはrailsでコンテナ内にhoge.txtを作成する。
// デフォルトコマンドはlsで、ディレクトリ一覧が表示される想定。
```

- `docker image build`でビルド実行する。

```
PS D:\testdocker\docker> docker image build .
[+] Building 34.1s (7/7) FINISHED                                                                                                                                                    docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => => transferring dockerfile: 90B                                                                                                                                                                  0.0s
 => [internal] load metadata for docker.io/library/rails:5                                                                                                                                           2.5s
 => [auth] library/rails:pull token for registry-1.docker.io                                                                                                                                         0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 34B                                                                                                                                                                     0.0s
 => [1/2] FROM docker.io/library/rails:5@sha256:aec52fe81ff0e99d64174001fbdf44c3bc9e1869756dec05c447f52a0fe637e7                                                                                    29.9s
 => => resolve docker.io/library/rails:5@sha256:aec52fe81ff0e99d64174001fbdf44c3bc9e1869756dec05c447f52a0fe637e7                                                                                     0.0s
 => => sha256:4cd427c44fca003d57c5ad391bc709131ed308bc8468d9b1331db3f5571bac74 23.27MB / 23.27MB                                                                                                     4.3s
 => => sha256:6bd3d457e27acefd70f4ff93b1b20794746e0a49eda0630f9e68aed3b7e910c2 13.73MB / 13.73MB                                                                                                     2.9s
 => => sha256:7dd42782d26e2bbf0d49e35860ca528b73ea72e46b787312849502fe37e9b333 2.88MB / 2.88MB                                                                                                       1.3s
 => => sha256:73729083bf6c97a21d7c753cc31a23e9cb3142f61200ba7c33a32333973e1be7 162B / 162B                                                                                                           0.8s
 => => sha256:7025772f9b601093d0f4d16474bab88bca8f180d1e3efcb47899fac48a62c86f 612.88kB / 612.88kB                                                                                                   0.5s
 => => sha256:c2807e33cf8416989005a833f0b1cf36ccb635a9c1df5b00dbc1862dfd01330c 35.11MB / 35.11MB                                                                                                     6.4s
 => => sha256:afb684ad7765258b3876f3ee6f96244f254b12c72f6bcfd97af3bd02f7912935 204B / 204B                                                                                                           0.4s
 => => sha256:871436ab7225503e9e951a7acb7b1689a91a60d033bf8cbabcd40fe5ca4cfc87 129.82MB / 129.82MB                                                                                                  22.5s
 => => sha256:4306be1e8943b446026b96c2ef7b3ab8471c760774fd1cd11334df7084fed57b 42.50MB / 42.50MB                                                                                                     7.5s
 => => sha256:57de64c72267e88e952b064236cb906c7626f7c07a1a2d5900cf6953e72632b3 18.53MB / 18.53MB                                                                                                     3.6s
 => => sha256:75a822cd7888e394c49828b951061402d31745f596b1f502758570f2d0ee79e2 51.36MB / 51.36MB                                                                                                     8.9s
 => => extracting sha256:75a822cd7888e394c49828b951061402d31745f596b1f502758570f2d0ee79e2                                                                                                            1.8s
 => => extracting sha256:57de64c72267e88e952b064236cb906c7626f7c07a1a2d5900cf6953e72632b3                                                                                                            0.4s
 => => extracting sha256:4306be1e8943b446026b96c2ef7b3ab8471c760774fd1cd11334df7084fed57b                                                                                                            1.4s
 => => extracting sha256:871436ab7225503e9e951a7acb7b1689a91a60d033bf8cbabcd40fe5ca4cfc87                                                                                                            2.7s
 => => extracting sha256:afb684ad7765258b3876f3ee6f96244f254b12c72f6bcfd97af3bd02f7912935                                                                                                            0.0s
 => => extracting sha256:c2807e33cf8416989005a833f0b1cf36ccb635a9c1df5b00dbc1862dfd01330c                                                                                                            0.9s
 => => extracting sha256:7025772f9b601093d0f4d16474bab88bca8f180d1e3efcb47899fac48a62c86f                                                                                                            0.1s
 => => extracting sha256:73729083bf6c97a21d7c753cc31a23e9cb3142f61200ba7c33a32333973e1be7                                                                                                            0.0s
 => => extracting sha256:7dd42782d26e2bbf0d49e35860ca528b73ea72e46b787312849502fe37e9b333                                                                                                            0.1s
 => => extracting sha256:6bd3d457e27acefd70f4ff93b1b20794746e0a49eda0630f9e68aed3b7e910c2                                                                                                            0.3s
 => => extracting sha256:4cd427c44fca003d57c5ad391bc709131ed308bc8468d9b1331db3f5571bac74                                                                                                            0.8s
 => [2/2] RUN touch hoge.txt                                                                                                                                                                         1.0s
 => exporting to image                                                                                                                                                                               0.4s
 => => exporting layers                                                                                                                                                                              0.2s
 => => exporting manifest sha256:33976682229c02af31b4b722fce03347fa4ef357d66b6097e6cd8cd8caa08703                                                                                                    0.0s
 => => exporting config sha256:49e3fc93e6032ae453d28d55ddad3683b0dbe0cdfc51106c013258bb4d445121                                                                                                      0.0s
 => => exporting attestation manifest sha256:6c118e0d4508f8a7c7c1bcb1c952dbfd05161ba3978742b29dfd5bda160b3db1                                                                                        0.0s
 => => exporting manifest list sha256:6aa7b968d432cb03a9910a3c9c72045bfe09e03dbf5b4239ca4c4e92e55fcfbc                                                                                               0.0s
 => => naming to moby-dangling@sha256:6aa7b968d432cb03a9910a3c9c72045bfe09e03dbf5b4239ca4c4e92e55fcfbc                                                                                               0.0s
 => => unpacking to moby-dangling@sha256:6aa7b968d432cb03a9910a3c9c72045bfe09e03dbf5b4239ca4c4e92e55fcfbc                                                                                            0.0s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/zgvt8bwuhix6rurgw5gl429kt
```

- `docker image hidtory`でイメージレイヤー構造を確認する。

```
PS D:\testdocker\docker> docker image history sha256:6aa7b968d432cb03a9910a3c9c72045bfe09e03dbf5b4239ca4c4e92e55fcfbc

IMAGE          CREATED         CREATED BY                                       SIZE      COMMENT
6aa7b968d432   2 minutes ago   CMD ["ls" "-la"]                                 0B        buildkit.dockerfile.v0
<missing>      2 minutes ago   RUN /bin/sh -c touch hoge.txt # buildkit         4.1kB     buildkit.dockerfile.v0
// 上記は先程作成したDockerfileで記述した箇所

// 下記はベースイメージのレイヤー
<missing>      8 years ago     /bin/sh -c gem install rails --version "$RAI…   65.3MB
<missing>      8 years ago     /bin/sh -c #(nop)  ENV RAILS_VERSION=5.0.1       0B
<missing>      8 years ago     /bin/sh -c apt-get update && apt-get install…   47.1MB
<missing>      8 years ago     /bin/sh -c apt-get update && apt-get install…   8.9MB
<missing>      8 years ago     /bin/sh -c #(nop)  CMD ["irb"]                   0B
// 今回はCMDを上書きしているため、この処理は呼ばれない。
// 上書きされている場合は、この処理がデフォルトコマンドで実行されている。

<missing>      8 years ago     /bin/sh -c mkdir -p "$GEM_HOME" "$BUNDLE_BIN…   20.5kB
<missing>      8 years ago     /bin/sh -c #(nop)  ENV PATH=/usr/local/bundl…   0B
<missing>      8 years ago     /bin/sh -c #(nop)  ENV BUNDLE_PATH=/usr/loca…   0B
<missing>      8 years ago     /bin/sh -c #(nop)  ENV GEM_HOME=/usr/local/b…   0B
<missing>      8 years ago     /bin/sh -c gem install bundler --version "$B…   2.36MB
<missing>      8 years ago     /bin/sh -c #(nop)  ENV BUNDLER_VERSION=1.13.7    0B
<missing>      8 years ago     /bin/sh -c set -ex   && buildDeps='   bison …   125MB
<missing>      8 years ago     /bin/sh -c #(nop)  ENV RUBYGEMS_VERSION=2.6.8    0B
<missing>      8 years ago     /bin/sh -c #(nop)  ENV RUBY_DOWNLOAD_SHA256=…   0B
<missing>      8 years ago     /bin/sh -c #(nop)  ENV RUBY_VERSION=2.3.3        0B
<missing>      8 years ago     /bin/sh -c #(nop)  ENV RUBY_MAJOR=2.3            0B
<missing>      8 years ago     /bin/sh -c mkdir -p /usr/local/etc  && {   e…   20.5kB
<missing>      8 years ago     /bin/sh -c apt-get update && apt-get install…   349MB
<missing>      8 years ago     /bin/sh -c apt-get update && apt-get install…   136MB
<missing>      8 years ago     /bin/sh -c apt-get update && apt-get install…   47.3MB
<missing>      8 years ago     /bin/sh -c #(nop)  CMD ["/bin/bash"]             0B
<missing>      8 years ago     /bin/sh -c #(nop) ADD file:1d214d2782eaccc74…   141MB
```

#### コンテナレイヤーとは？

- コンテナ上で変更した内容を保存するレイヤーを表す。
  - イメージレイヤーとは違い、書き込み可能。

### イメージレイヤーの容量を小さくする方法

- 前提として、レイヤーの数が少ない方が容量は小さくなる。
  - なので、実行されるコマンドの数が少ないほうが良い。

#### 比べてみる

- 以下の Dockerfile を用意する。

```
FROM ubuntu:20.04

RUN apt update
RUN apt install -y curl
RUN apt install -y vim

CMD ["bash"]
```

- `docker image build`でビルドする。

```
PS D:\testdocker\docker> docker image build .
[+] Building 69.3s (9/9) FINISHED                                                                                                                                                    docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => => transferring dockerfile: 137B                                                                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                      0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 34B                                                                                                                                                                     0.0s
 => [1/4] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.6s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.6s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                        0.0s
 => CACHED [2/4] RUN apt update                                                                                                                                                                      0.0s
 => CACHED [3/4] RUN apt install -y curl                                                                                                                                                             0.0s
 => [4/4] RUN apt install -y vim                                                                                                                                                                    63.6s
 => exporting to image                                                                                                                                                                               3.7s
 => => exporting layers                                                                                                                                                                              2.6s
 => => exporting manifest sha256:bed008d14af8382617a39eb6b120c397db70d48596bce223632d8b9af8d0a7a8                                                                                                    0.0s
 => => exporting config sha256:5ee972cd55e8ebdbf2344762f2ba686633c2067f3a67145974bd115ec2725876                                                                                                      0.0s
 => => exporting attestation manifest sha256:1adcd5d15197ca7fb4341a9a7979513d504893c5d89d3d7bda34a3d64ea5ec56                                                                                        0.0s
 => => exporting manifest list sha256:43b36206b4d1cd928af3da74e21778f9853df0ef4a6e631a0388d45559216954                                                                                               0.0s
 => => naming to moby-dangling@sha256:43b36206b4d1cd928af3da74e21778f9853df0ef4a6e631a0388d45559216954                                                                                               0.0s
 => => unpacking to moby-dangling@sha256:43b36206b4d1cd928af3da74e21778f9853df0ef4a6e631a0388d45559216954                                                                                            0.9s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/sfbx2vq719vlq76c6le6t3gsl
```

- 次は以下の Dockerfile を用意する。

```
FROM ubuntu:20.04

RUN apt update && apt install -y curl vim

CMD ["bash"]
```

- `docker image build`でビルドする。

```
PS D:\testdocker\docker> docker image build .
[+] Building 158.2s (7/7) FINISHED                                                                                                                                                   docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => => transferring dockerfile: 115B                                                                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                      0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 34B                                                                                                                                                                     0.0s
 => CACHED [1/2] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                         1.6s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.6s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                        0.0s
 => [2/2] RUN apt update && apt install -y curl vim                                                                                                                                                149.7s
 => exporting to image                                                                                                                                                                               6.6s
 => => exporting layers                                                                                                                                                                              5.2s
 => => exporting manifest sha256:68e7d58e37296ad896513952bfd0bfeefac4295e649f0872039268e0888bb3b3                                                                                                    0.0s
 => => exporting config sha256:d8f08e471afd0d93af1778d402d51c1579aabd3d6041de0114d9378ba1830b3d                                                                                                      0.0s
 => => exporting attestation manifest sha256:ff7820f18245b543d18f589b2f2d0d08b1459b4fb9907059d9a3b9933717d62f                                                                                        0.0s
 => => exporting manifest list sha256:d1886c5cd1c6a69c64f512a3d53d4623ccc94a6c72e34ce6dcc9f98c16bf439d                                                                                               0.0s
 => => naming to moby-dangling@sha256:d1886c5cd1c6a69c64f512a3d53d4623ccc94a6c72e34ce6dcc9f98c16bf439d                                                                                               0.0s
 => => unpacking to moby-dangling@sha256:d1886c5cd1c6a69c64f512a3d53d4623ccc94a6c72e34ce6dcc9f98c16bf439d                                                                                            1.2s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/tzyp8gy20fhobx6saidbnr10c
```

- 両方ともビルド出来たため、`docker image ls`で容量を見比べる。

```
PS D:\testdocker\docker> docker image ls

REPOSITORY      TAG       IMAGE ID       CREATED          SIZE
<none>          <none>    d1886c5cd1c6   2 minutes ago    318MB
// 最後にビルドしたイメージ

<none>          <none>    43b36206b4d1   19 minutes ago   319MB
// 最初にビルドしたイメージ

// 結果最後にビルドしたイメージが一番容量が小さい事がわかった。
// 実行結果は同じだが、レイヤー構造を意識することでイメージのサイズを小さくすることができる事がわかった。
```
