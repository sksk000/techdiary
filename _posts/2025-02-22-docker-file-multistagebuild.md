---
title: "Dockerfileのマルチステージビルドについて理解する"
last_modified_at: 2024-09-19T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### マルチステージビルドとは？

- 1 つの Dockerfile でステージごとに環境を分離し、必要なイメージだけを作ることができる機能

### 実際にマルチステージビルドを行う

- まずは、シングルステージビルドを行う。
- D ドライブ直下にテスト用の testdocker_1 フォルダを用意する。
- testdocker_1 フォルダに以下の Dockerfile を用意する。

```
FROM gcc:12.2.0

WORKDIR /app
COPY ./hello.c .
RUN gcc hello.c
// cファイルをコンテナにコピーし、gccでコンパイル

CMD ["./a.out"]
// コンパイルしたファイルを実行
```

- testdocker_1 フォルダに以下の hello.c を用意する。

```
#include <stdio.h>

int main(){
	printf("Hello World\n");
	return 0;
}
```

- gcc でビルドを行えなかったため、環境構築を行う。
  - MinGW-w64 の導入。
    - https://winlibs.com/
  - 導入後、下記の URL を参考に MinGW-w64 の環境変数を設定する。
    - https://www.javadrive.jp/cstart/install/index6.html

```
D:\testdocker_1>gcc hello.c -o a.out
// gcc hello.cでビルドすると、hello.exeができた。
// そのため、-oオプションで出力ファイル名を設定。

D:\testdocker_1>a.out
Hello World
// 実行すると、printfで出力しているHello Worldが出力。
```

- `docker build`を実行

```
PS D:\testdocker_1> docker image build -t single-image .
// 今回はイメージに名前を付けて実行

[+] Building 46.9s (10/10) FINISHED                                                                                                                                                  docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.1s
 => => transferring dockerfile: 122B                                                                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/gcc:12.2.0                                                                                                                                        2.6s
 => [auth] library/gcc:pull token for registry-1.docker.io                                                                                                                                           0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.1s
 => => transferring context: 2B                                                                                                                                                                      0.0s
 => [1/4] FROM docker.io/library/gcc:12.2.0@sha256:a3e091325c0af43bc9c1c576ddd155351d5b16438124421188bb4b4fcacc1452                                                                                 41.6s
 => => resolve docker.io/library/gcc:12.2.0@sha256:a3e091325c0af43bc9c1c576ddd155351d5b16438124421188bb4b4fcacc1452                                                                                  0.0s
 => => sha256:3f2f807db31c5b2a06e06a0f30ab3aed3f0101790e1d45d57dc3966ae3411a7c 10.13kB / 10.13kB                                                                                                     0.2s
 => => sha256:e060ccbcfc97c154ad4f21a6c1c56410e4175041857b0ba1fc3ea5c10d7158d4 1.88kB / 1.88kB                                                                                                       0.4s
 => => sha256:7dcff776ce99bf1d9f65a978fa5a673066e40c14c2dafa23c8e448bad8dce4d0 17.15kB / 17.15kB                                                                                                     0.6s
 => => sha256:6d02efafaffd948565c3ebd8623e51d4437f1fb68cd7655aa43735e1a7bbd251 138.30MB / 138.30MB                                                                                                  24.3s
 => => sha256:9cdadd40055fb82fef74a0a38f29fb1ee7b6922e18370ebdc3502ec66f79fa3f 196.85MB / 196.85MB                                                                                                  33.7s
 => => sha256:4eedd9c5abf7e5f63753a5e788cb0872a715fa1141e8ce5ea87638e6cd370a41 54.58MB / 54.58MB                                                                                                     9.4s
 => => sha256:5d79063a01c561833dc6546d4e647fda0121a59e1a9a17874a3e30854555475e 15.76MB / 15.76MB                                                                                                     2.9s
 => => sha256:918547b9432687b1e1d238e82dc1e0ea0b736aafbf3c402eea98c6db81a9cb65 55.05MB / 55.05MB                                                                                                     9.6s
 => => extracting sha256:918547b9432687b1e1d238e82dc1e0ea0b736aafbf3c402eea98c6db81a9cb65                                                                                                            2.3s
 => => extracting sha256:5d79063a01c561833dc6546d4e647fda0121a59e1a9a17874a3e30854555475e                                                                                                            0.6s
 => => extracting sha256:4eedd9c5abf7e5f63753a5e788cb0872a715fa1141e8ce5ea87638e6cd370a41                                                                                                            2.2s
 => => extracting sha256:9cdadd40055fb82fef74a0a38f29fb1ee7b6922e18370ebdc3502ec66f79fa3f                                                                                                            4.5s
 => => extracting sha256:7dcff776ce99bf1d9f65a978fa5a673066e40c14c2dafa23c8e448bad8dce4d0                                                                                                            0.0s
 => => extracting sha256:6d02efafaffd948565c3ebd8623e51d4437f1fb68cd7655aa43735e1a7bbd251                                                                                                            2.7s
 => => extracting sha256:3f2f807db31c5b2a06e06a0f30ab3aed3f0101790e1d45d57dc3966ae3411a7c                                                                                                            0.0s
 => => extracting sha256:e060ccbcfc97c154ad4f21a6c1c56410e4175041857b0ba1fc3ea5c10d7158d4                                                                                                            0.0s
 => [internal] load build context                                                                                                                                                                    0.2s
 => => transferring context: 109B                                                                                                                                                                    0.0s
 => [2/4] WORKDIR /app                                                                                                                                                                               1.1s
 => [3/4] COPY ./hello.c .                                                                                                                                                                           0.1s
 => [4/4] RUN gcc hello.c                                                                                                                                                                            0.4s
 => exporting to image                                                                                                                                                                               0.7s
 => => exporting layers                                                                                                                                                                              0.4s
 => => exporting manifest sha256:34076b28d966c0403b8de4e014e32836505358ac364c1c2edfebca94f3b952cd                                                                                                    0.0s
 => => exporting config sha256:3d20d6d6b9623f3d2f62845be53b6da96a1a26b8312d7abc02ecb5de8b5c6667                                                                                                      0.0s
 => => exporting attestation manifest sha256:fcef89db5037c04abc570dc0c7faf50d3d728a84f8dde7e1cbe1d88dcefc5e69                                                                                        0.0s
 => => exporting manifest list sha256:3f816c0d2e279b9c8d2b89d6ebea234f80ba06cd2feeed431380abcbe52e9d24                                                                                               0.0s
 => => naming to docker.io/library/single-image:latest                                                                                                                                               0.0s
 => => unpacking to docker.io/library/single-image:latest                                                                                                                                            0.1s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/1xob0b2wz1qizexvotf8504qr
```

- 作成したイメージを実行

```
PS D:\testdocker_1> docker container run -it single-image
Hello World
// 実行結果が出力されている。
```

- 次に、マルチステージビルドで行う。
  - 以下の Dockerfile を用意する。

```
//---１つ目のステージ
FROM gcc:12.2.0 AS compler
// FROM <ベースイメージ名> AS <ステージ名>でステージに名前を付けることができる
WORKDIR /app
COPY ./hello.c .
RUN gcc hello.c
//---

//---2つ目のステージ
FROM ubuntu:20.04
WORKDIR /app
COPY --from=compler /app/a.out .
CMD ["./a.out"]
// complerステージで作成した/app/a.outを
// ubuntuのコンテナのルートディレクトリにコピーする。
//---
```

- ビルド後、コンテナを起動する。

```
PS D:\testdocker_1> docker image build -t multi-image .
[+] Building 3.8s (15/15) FINISHED                                                                                                                                                   docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => => transferring dockerfile: 200B                                                                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                      0.1s
 => [internal] load metadata for docker.io/library/gcc:12.2.0                                                                                                                                        1.6s
 => [auth] library/gcc:pull token for registry-1.docker.io                                                                                                                                           0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 2B                                                                                                                                                                      0.0s
 => [compler 1/4] FROM docker.io/library/gcc:12.2.0@sha256:a3e091325c0af43bc9c1c576ddd155351d5b16438124421188bb4b4fcacc1452                                                                          0.1s
 => => resolve docker.io/library/gcc:12.2.0@sha256:a3e091325c0af43bc9c1c576ddd155351d5b16438124421188bb4b4fcacc1452                                                                                  0.0s
 => [internal] load build context                                                                                                                                                                    0.0s
 => => transferring context: 28B                                                                                                                                                                     0.0s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.1s
 => CACHED [compler 2/4] WORKDIR /app                                                                                                                                                                0.0s
 => CACHED [compler 3/4] COPY ./hello.c .                                                                                                                                                            0.0s
 => CACHED [compler 4/4] RUN gcc hello.c                                                                                                                                                             0.0s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                        0.0s
 => [stage-1 2/3] WORKDIR /app                                                                                                                                                                       0.1s
 => [stage-1 3/3] COPY --from=compler /app/a.out .                                                                                                                                                   0.1s
 => exporting to image                                                                                                                                                                               0.5s
 => => exporting layers                                                                                                                                                                              0.3s
 => => exporting manifest sha256:ae3ee98da7f369704c3e80638b8e3aa5f9ef675fb3030d0699e3f05e16784ecd                                                                                                    0.0s
 => => exporting config sha256:ac3e18f7cef3b597eb94f00a99aeb53b61002a8de8bef0e19e0cd4db573f8aad                                                                                                      0.0s
 => => exporting attestation manifest sha256:65193b4c1a7120e4bbbac9ef9b3490de62d68ce64c4cc47f92ffd6fb700be714                                                                                        0.0s
 => => exporting manifest list sha256:bf76e2d0a353c99367d176abe125a425363aa8b8cf5f5cff4fa03253dc114040                                                                                               0.0s
 => => naming to docker.io/library/multi-image:latest                                                                                                                                                0.0s
 => => unpacking to docker.io/library/multi-image:latest                                                                                                                                             0.1s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/9zeq7i01en61yillzd48aga4y

PS D:\testdocker_1> docker container run -it multi-image

Hello World
// シングルステージビルドと同じ結果が出力された。
```

- イメージのサイズ比較する。

```
PS D:\testdocker_1> docker image ls

REPOSITORY      TAG       IMAGE ID       CREATED             SIZE
multi-image     latest    bf76e2d0a353   2 minutes ago       109MB
single-image    latest    3f816c0d2e27   About an hour ago   1.8GB
// 比較するとマルチステージビルドのほうが容量が小さい。
// シングルイメージビルドだと、gccのコンパイル環境があるためイメージの容量が肥大化してしまう。
// マルチステージビルドは、シンプルなubuntuの環境のみイメージに保存されるため、サイズが小さい。
// コンパクトなイメージづくりをする上で、マルチステージビルドは重要になるということがわかった。
```
