---
title: ".dockerignoreとDockerfileのCMDを理解する"
last_modified_at: 2025-02-18T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### .dockerignore とは

- ビルドコンテキストから除外するファイルを指定できるファイルを指す。
- 実際に確認する。
  - Dockerfile 内で指定しているコピー元のパスを変更する。

```
FROM ubuntu:20.04

RUN apt update
RUN apt install -y curl

COPY . /app/
// コピー元を現在のディレクトリにし、コピー先をコンテナ内の/appに変更
```

- フォルダ構成は以下で行う。

```
    ディレクトリ: D:\testdocker


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        2025/02/18     15:33                docker
// testdockerの中にdockerフォルダが存在する。

PS D:\testdocker\docker> ls

    ディレクトリ: D:\testdocker\docker


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        2025/02/18     15:46              0 .dockerignore
-a----        2025/02/18     15:46             76 Dockerfile
-a----        2025/02/17     18:02             14 hello.txt
-a----        2025/02/18     15:46              8 hoge.txt

```

- .dockerignore には以下の記述を行う

```
hoge.txt
// hoge.txtはビルドコンテキストに含まれない。
```

- 実際に上記の環境下でビルド実行する。

```
> docker image build .

[+] Building 2.4s (10/10) FINISHED                                                                                                                                                   docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => => transferring dockerfile: 113B                                                                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                      0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 48B                                                                                                                                                                     0.0s
 => [1/4] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.7s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.7s
 => [internal] load build context                                                                                                                                                                    0.0s
 => => transferring context: 215B                                                                                                                                                                    0.0s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                        0.0s
 => CACHED [2/4] RUN apt update                                                                                                                                                                      0.0s
 => CACHED [3/4] RUN apt install -y curl                                                                                                                                                             0.0s
 => [4/4] COPY . /app/                                                                                                                                                                               0.1s
 => exporting to image                                                                                                                                                                               0.4s
 => => exporting layers                                                                                                                                                                              0.2s
 => => exporting manifest sha256:b952e757384e80e00cae8f65b35edc521c3cfa1467dce679a57573213913003b                                                                                                    0.0s
 => => exporting config sha256:02b5b681fa1817e65ebeb532d63feb22795c411ebf0ecf427aa1b9166247589f                                                                                                      0.0s
 => => exporting attestation manifest sha256:ede5cab6555c782788b9df6b5853b7bdaae089eae42b6e3e41f00d57c67f6fdc                                                                                        0.0s
 => => exporting manifest list sha256:9328c6ed308590ac1070f79571a0590cc9da0149cc5f2abefc2cb1921ab049de                                                                                               0.0s
 => => naming to moby-dangling@sha256:9328c6ed308590ac1070f79571a0590cc9da0149cc5f2abefc2cb1921ab049de                                                                                               0.0s
 => => unpacking to moby-dangling@sha256:9328c6ed308590ac1070f79571a0590cc9da0149cc5f2abefc2cb1921ab049de                                                                                            0.0s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/aygqnxriiv30ax8wddwed0vli
// 無事ビルド成功。
```

- コンテナ内のファイル確認する。

```
> docker container run -it --rm sha256:9328c6ed308590ac1070f79571a0590cc9da0149cc5f2abefc2cb1921ab049de
// 今回使用するコンテナは使い捨てなので、--rmを指定しコンテナ内でexitするとコンテナ自体勝手に削除される。

root@4c7504dec3ef:/# ls -la
total 60
drwxr-xr-x   1 root root 4096 Feb 18 07:11 .
drwxr-xr-x   1 root root 4096 Feb 18 07:11 ..
-rwxr-xr-x   1 root root    0 Feb 18 07:11 .dockerenv
drwxr-xr-x   2 root root 4096 Feb 18 07:08 app
･･･
// appフォルダが存在することを確認


root@4c7504dec3ef:/# cd /app
root@4c7504dec3ef:/app# ls -la

total 20
drwxr-xr-x 2 root root 4096 Feb 18 07:08 .
drwxr-xr-x 1 root root 4096 Feb 18 07:11 ..
-rwxr-xr-x 1 root root    8 Feb 18 06:47 .dockerignore
-rwxr-xr-x 1 root root   76 Feb 18 06:46 Dockerfile
-rwxr-xr-x 1 root root   14 Feb 17 09:02 hello.txt
// .dockerignoreで設定されていたhoge.txtは除外し、ビルドが行われていたということがわかる。
```

### .dockerignore はどのようなときに使用するべきか

- 機密情報やファイルサイズの大きいデータは.dockerignore に設定するべき。
  - 特にファイルサイズの大きいデータは、ビルド実行に時間を要してしまう。
- 実際にファイルサイズの大きいデータをビルドして確認してみる。
  - 事前準備として、500MB のダミーファイルを`fsutil file createnew`コマンドを使用し作成する。

```
PS D:\testdocker\docker> fsutil file createnew largefile 524288000
ファイル D:\testdocker\docker\largefile が作成されました

PS D:\testdocker\docker> docker image build .
// ビルド実行

[+] Building 24.3s (10/10) FINISHED                                                                                                                                                  docker:desktop-linux
// 前回は2秒で今回は24秒ビルドに時間がかかった。

 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => => transferring dockerfile: 113B                                                                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                      0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 48B                                                                                                                                                                     0.0s
 => [1/4] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.8s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.8s
 => [internal] load build context                                                                                                                                                                   16.4s
 => => transferring context: 524.42MB                                                                                                                                                               16.4s
// ここではクライアントからdockerデーモンにコンテキストが送られている、16秒かかった。

 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                        0.0s
 => CACHED [2/4] RUN apt update                                                                                                                                                                      0.0s
 => CACHED [3/4] RUN apt install -y curl                                                                                                                                                             0.0s
 => [4/4] COPY . /app/                                                                                                                                                                               0.6s
 => exporting to image                                                                                                                                                                               7.0s
 => => exporting layers                                                                                                                                                                              4.4s
 => => exporting manifest sha256:60e59310e52dcc6bd3c44b1f76890175f5a608eddc16a3787e2671ccc5912d50                                                                                                    0.0s
 => => exporting config sha256:da3bcc396346a346ebb954ed5ccab17287cb3ba2a03d63a4b7f56738ed41c2eb                                                                                                      0.0s
 => => exporting attestation manifest sha256:165bcf9f22eeb6749932c085cba424e1e5bd0c5fe0d1db444a772eb4968256f2                                                                                        0.1s
 => => exporting manifest list sha256:a0134c03cfb4e6c6a7d5be4545028e06c1edb7da8a9020f7fbb37dc20982d7e1                                                                                               0.0s
 => => naming to moby-dangling@sha256:a0134c03cfb4e6c6a7d5be4545028e06c1edb7da8a9020f7fbb37dc20982d7e1                                                                                               0.0s
 => => unpacking to moby-dangling@sha256:a0134c03cfb4e6c6a7d5be4545028e06c1edb7da8a9020f7fbb37dc20982d7e1                                                                                            2.3s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/0oymopi9r3rgmcfitvia66sr7
// 結果前回のビルドより多く時間を要している事がわかった。
// 必要のないデータ特に容量の大きいデータは.dockerignoreで除外することが大切。
```

### Dockerfile 内に定義する CMD とは？

- CMD はコマンドを意味する。
  - `CMD["実行ファイル", "パラメータ1", "パラメータ2"]`で記述を行う。
  - Dockerfile 内にて、デフォルトコマンドを定義する際に使用する。
  - 一度しか使うことが出来ない。
  - ※Dockerfile 内で複数 CMD が定義されている場合は、最後に定義されている CMD を実行する。
- 実際に Dockerfile に記述し動作を確認する。

```
FROM ubuntu:20.04

RUN apt update
RUN apt install -y curl

COPY . /app/

CMD ["ls", "-la"]
// CMDが記述されていない場合は、ubuntu:20.04で指定しているbashを実行する。
// ここに記述することで、bashではなくlsが実行される。
```

- `docker image build`を実行する。

```
PS D:\testdocker\docker> docker image build .
[+] Building 7.5s (10/10) FINISHED                                                                                                                                                                                              docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                                                            0.0s
 => => transferring dockerfile: 133B                                                                                                                                                                                                            0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                                                                 0.0s
 => [internal] load .dockerignore                                                                                                                                                                                                               0.0s
 => => transferring context: 34B                                                                                                                                                                                                                0.0s
 => [1/4] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                                                           6.6s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                                                           6.6s
 => [internal] load build context                                                                                                                                                                                                               0.0s
 => => transferring context: 197B                                                                                                                                                                                                               0.0s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                                                                   0.0s
 => CACHED [2/4] RUN apt update                                                                                                                                                                                                                 0.0s
 => CACHED [3/4] RUN apt install -y curl                                                                                                                                                                                                        0.0s
 => [4/4] COPY . /app/                                                                                                                                                                                                                          0.1s
 => exporting to image                                                                                                                                                                                                                          0.4s
 => => exporting layers                                                                                                                                                                                                                         0.2s
 => => exporting manifest sha256:3518464c31d25f472133f6cb8b3c8eaa745de5471bfd9e2edd328f37cec72eb4                                                                                                                                               0.0s
 => => exporting config sha256:79756ac4046b29bdcf91d927d81f48ebdc9240167b03287fabe7c9153c315682                                                                                                                                                 0.0s
 => => exporting attestation manifest sha256:3892f8426809fc05eba848aca41dd0c24c8b2cc719912fc898e925798a2d63c1                                                                                                                                   0.0s
 => => exporting manifest list sha256:27187bacb954d7874fe406a55874497dbc8b1653f762c7c8aedce05c8a84271e                                                                                                                                          0.0s
 => => naming to moby-dangling@sha256:27187bacb954d7874fe406a55874497dbc8b1653f762c7c8aedce05c8a84271e                                                                                                                                          0.0s
 => => unpacking to moby-dangling@sha256:27187bacb954d7874fe406a55874497dbc8b1653f762c7c8aedce05c8a84271e                                                                                                                                       0.0s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/muhop8n3ky4x8hfam7cxo6o7o
// ビルド成功
```

- `docker container run -it`でコンテナ内に入る

```
PS D:\testdocker\docker> docker container run -it sha256:27187bacb954d7874fe406a55874497dbc8b1653f762c7c8aedce05c8a84271e

total 60
drwxr-xr-x   1 root root 4096 Feb 18 09:04 .
drwxr-xr-x   1 root root 4096 Feb 18 09:04 ..
-rwxr-xr-x   1 root root    0 Feb 18 09:04 .dockerenv
drwxr-xr-x   2 root root 4096 Feb 18 09:02 app
lrwxrwxrwx   1 root root    7 Oct 11 02:03 bin -> usr/bin
drwxr-xr-x   2 root root 4096 Apr 15  2020 boot
drwxr-xr-x   5 root root  360 Feb 18 09:04 dev
drwxr-xr-x   1 root root 4096 Feb 18 09:04 etc
drwxr-xr-x   2 root root 4096 Apr 15  2020 home
lrwxrwxrwx   1 root root    7 Oct 11 02:03 lib -> usr/lib
lrwxrwxrwx   1 root root    9 Oct 11 02:03 lib32 -> usr/lib32
lrwxrwxrwx   1 root root    9 Oct 11 02:03 lib64 -> usr/lib64
lrwxrwxrwx   1 root root   10 Oct 11 02:03 libx32 -> usr/libx32
drwxr-xr-x   2 root root 4096 Oct 11 02:03 media
drwxr-xr-x   2 root root 4096 Oct 11 02:03 mnt
drwxr-xr-x   2 root root 4096 Oct 11 02:03 opt
dr-xr-xr-x 204 root root    0 Feb 18 09:04 proc
drwx------   2 root root 4096 Oct 11 02:09 root
drwxr-xr-x   5 root root 4096 Oct 11 02:09 run
lrwxrwxrwx   1 root root    8 Oct 11 02:03 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 Oct 11 02:03 srv
dr-xr-xr-x  11 root root    0 Feb 18 09:04 sys
drwxrwxrwt   2 root root 4096 Oct 11 02:09 tmp
drwxr-xr-x   1 root root 4096 Oct 11 02:03 usr
drwxr-xr-x   1 root root 4096 Oct 11 02:09 var
// デフォルトコマンドであるls -laが実行され、ルートディレクトリのディレクトリ情報一覧が表示されている。
```
