---
title: "マルチステージングビルドを実務で使う例について理解する"
last_modified_at: 2025-02-23T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### 実務でマルチステージングビルドを行う例

- 本番環境と開発環境で環境を分ける場合
  - Dockerfile を本番環境・開発環境 2 つに分けると以下のデメリットがある。
    - ファイルが多くなってしまい管理が大変。
    - 共通処理を同じファイルに書くことになるため、保守が面倒。
  - マルチステージングビルドを使用し、1 つの Dockerfile で管理することができるため、管理が楽になる。

### 実際に試してみる

- 例として、Dockerfile を用意する。
  - Dockerfile 内には、開発・本番環境用のステージと共通処理用のステージを用意する。

```
//--共通処理(base)ステージ
FROM ubuntu:20.04 AS base
RUN apt update
CMD ["sh", "-c", "echo My name is $my_name"]
//----------

//--開発環境用(development)ステージ
FROM base AS devlopment
ENV my_name=TEST
//----------

//--本番環境用(production)ステージ
FROM base AS production
ENV my_name=Bob
//----------
```

- `docker image build`で開発環境用のイメージのビルドを行う

```
PS D:\testdocker_1> docker image build --target development .
// --target <ステージ名>
// どのステージをビルド対象とするか設定できる。

[+] Building 2.1s (7/7) FINISHED                                                                                                                                                     docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => => transferring dockerfile: 216B                                                                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                      0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 2B                                                                                                                                                                      0.0s
 => [base 1/2] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                           1.7s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.7s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                        0.0s
 => CACHED [base 2/2] RUN apt update                                                                                                                                                                 0.0s
 => exporting to image                                                                                                                                                                               0.2s
 => => exporting layers                                                                                                                                                                              0.0s
 => => exporting manifest sha256:a2dc2fd95b304ebdf30fdc066d420ad5d732595ad000aec7bfc9e58c24970c97                                                                                                    0.0s
 => => exporting config sha256:f05e46e2c1febac373e82f13ffd3fa1ac09de1b55cdf8e0a31fe7a823cf4c384                                                                                                      0.0s
 => => exporting attestation manifest sha256:085f61aca3dd352d0a94c5685d81a7fdf19a1571fd501d007518eb24c7d42ce3                                                                                        0.0s
 => => exporting manifest list sha256:60f44e49d178ceb1b2fe277b5ee7d4b5f9fbc3da8639e8a8aff31e7919a74a8c                                                                                               0.0s
 => => naming to moby-dangling@sha256:60f44e49d178ceb1b2fe277b5ee7d4b5f9fbc3da8639e8a8aff31e7919a74a8c                                                                                               0.0s
 => => unpacking to moby-dangling@sha256:60f44e49d178ceb1b2fe277b5ee7d4b5f9fbc3da8639e8a8aff31e7919a74a8c                                                                                            0.0s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/rl84b3h5rv5su4jzcjsbwaqis
```

- `docker container run`でコンテナを起動し、確認する。

```
PS D:\testdocker_1> docker container run --rm sha256:60f44e49d178ceb1b2fe277b5ee7d4b5f9fbc3da8639e8a8aff31e7919a74a8c
My name is TEST
// developmentでビルドされ、TESTが表示されている。
// 開発環境用のステージでビルドされていることがわかった。
```

- `docker image build`で本番環境用のイメージのビルドを行う

```
PS D:\testdocker_1> docker image build --target production .
[+] Building 2.2s (7/7) FINISHED                                                                                                                                                     docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                      0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 2B                                                                                                                                                                      0.0s
 => [base 1/2] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                           1.6s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.6s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                        0.0s
 => CACHED [base 2/2] RUN apt update                                                                                                                                                                 0.0s
 => exporting to image                                                                                                                                                                               0.2s
 => => exporting layers                                                                                                                                                                              0.0s
 => => exporting manifest sha256:a3ce06844609453a501237f5e94d44a750f7fea699fd69d88f316b708ebd0906                                                                                                    0.0s
 => => exporting config sha256:51633154316f4d20e857b8823d219386e4f986cafc926adb135558b6151d9995                                                                                                      0.0s
 => => exporting attestation manifest sha256:30e04328862c3a502effcdbbbdb7ab5500d73a87b6ccb44c3d647e478e1febc7                                                                                        0.0s
 => => exporting manifest list sha256:fc045a19872ee972ab986d15b2a95b470f37ddf0e5f623f19172dc8b69c6739f                                                                                               0.0s
 => => naming to moby-dangling@sha256:fc045a19872ee972ab986d15b2a95b470f37ddf0e5f623f19172dc8b69c6739f                                                                                               0.0s
 => => unpacking to moby-dangling@sha256:fc045a19872ee972ab986d15b2a95b470f37ddf0e5f623f19172dc8b69c6739f                                                                                            0.0s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/whuddrjpcr74xs0i5qu7t4999
```

- `docker container run`でコンテナを起動し、確認する。

```
PS D:\testdocker_1> docker container run --rm sha256:fc045a19872ee972ab986d15b2a95b470f37ddf0e5f623f19172dc8b69c6739f
My name is Bob
// 本番環境用でビルドしたため、Bobと表示されている。
```

### マルチステージングビルドを採用するかの判断基準は？

- イメージの容量が肥大化している。
- Dockerfile 内で開発・本番環境を同じステージで構築している。
- Dockerfile を複数に分けて管理している。

### マルチステージングビルドの利点

- イメージの容量削減できる。
- Dockerfile を 1 つで管理できる。
- ステージを分けることで環境を分けることができる。
- 管理自体が楽になる。
