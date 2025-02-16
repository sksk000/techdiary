---
title: "Dockerfile内で任意のコマンドを実行したい場合にどうするか"
last_modified_at: 2025-02-16T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### Dockerfile に RUN を追加する

- RUN とは、シェルで実行したいコマンドを実行するコマンド。
- apt はパッケージ管理ツールで、`apt update`を行うとアップデートに必要なパッケージリストを更新することができる。

```
FROM ubuntu:20.04

RUN apt update
// ubuntu:20.04がインストールされ、aptをアップデートされた状態のコンテナが用意される

RUN apt install -y curl
```

```
> docker image build .

[+] Building 100.7s (8/8) FINISHED                                                                                                                                                   docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => => transferring dockerfile: 97B                                                                                                                                                                  0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                                                                                      0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 2B                                                                                                                                                                      0.0s
 => CACHED [1/3] FROM docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                         1.7s
 => => resolve docker.io/library/ubuntu:20.04@sha256:8e5c4f0285ecbb4ead070431d29b576a530d3166df73ec44affc1cd27555141b                                                                                1.7s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                        0.0s
 => [2/3] RUN apt update                                                                                                                                                                            69.8s
 => [3/3] RUN apt install -y curl                                                                                                                                                                   24.9s
 => exporting to image                                                                                                                                                                               3.9s
 => => exporting layers                                                                                                                                                                              3.1s
 => => exporting manifest sha256:695bb9121af8d81a3e0f65f661ac892bb9b2ec8b7e72e4f6e37132d72131b017                                                                                                    0.0s
 => => exporting config sha256:5a7e6d5da57622686e8c7f7b16ce17207986b396430db570a8eaf84768eacc76                                                                                                      0.0s
 => => exporting attestation manifest sha256:1151a3b937ad4b904d0e0487ae7b0bb84c6d021206f9af2d0ecd42b10dc38e3d                                                                                        0.0s
 => => exporting manifest list sha256:4809a7390a70c9701d2a8d0268be17e796ba7347480a8bdf9e82dc17da713758                                                                                               0.0s
 => => naming to moby-dangling@sha256:4809a7390a70c9701d2a8d0268be17e796ba7347480a8bdf9e82dc17da713758                                                                                               0.0s
 => => unpacking to moby-dangling@sha256:4809a7390a70c9701d2a8d0268be17e796ba7347480a8bdf9e82dc17da713758                                                                                            0.6s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/xvvj7y826i0o65nhauauelxd8
// 先ほどのDockerfileをビルドし、成功した。
```

- 試しに DockerHub から ubuntu:20.04 のイメージをインストールし curl があるか確認する。

```
> docker container run -it ubuntu:20.04
root@14c74602f329:/# curl
bash: curl: command not found
// curlコマンドが無いと言われているため、インストールされていない。
```

- 今度は先程作成したコンテナで curl があるか確認する

```
> docker container run -it sha256:4809a7390a70c9701d2a8d0268be17e796ba7347480a8bdf9e82dc17da713758
root@5ccd3b66a890:/#curl
curl: try 'curl --help' or 'curl --manual' for more information
// 先ほどのエラーと違い、curlはインストールされていそう。
```

- `apt install -y curl`の-y はどのようなオプションか確認する

```
root@5ccd3b66a890:/# apt install vim
// -yを使用せずvimをインストールする

Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  alsa-topology-conf alsa-ucm-conf file libasound2 libasound2-data libcanberra0 libexpat1 libgpm2 libltdl7 libmagic-mgc libmagic1 libmpdec2 libogg0 libpython3.8 libpython3.8-minimal
  libpython3.8-stdlib libreadline8 libtdb1 libvorbis0a libvorbisfile3 mime-support readline-common sound-theme-freedesktop vim-common vim-runtime xxd xz-utils
Suggested packages:
  libasound2-plugins alsa-utils libcanberra-gtk0 libcanberra-pulse gpm readline-doc ctags vim-doc vim-scripts
The following NEW packages will be installed:
  alsa-topology-conf alsa-ucm-conf file libasound2 libasound2-data libcanberra0 libexpat1 libgpm2 libltdl7 libmagic-mgc libmagic1 libmpdec2 libogg0 libpython3.8 libpython3.8-minimal
  libpython3.8-stdlib libreadline8 libtdb1 libvorbis0a libvorbisfile3 mime-support readline-common sound-theme-freedesktop vim vim-common vim-runtime xxd xz-utils
0 upgraded, 28 newly installed, 0 to remove and 2 not upgraded.
Need to get 13.1 MB of archives.
After this operation, 65.2 MB of additional disk space will be used.
Do you want to continue? [Y/n]
// 先ほど表示されなかった、y/nの入力が表示された。
// -yを付けてインストールすると、y/nを入力する必要がなく自動的にyにしインストールが行われる。
```

- `apt install -y vim`でインストールを行う。

```
root@5ccd3b66a890:/# apt install -y vim

Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  alsa-topology-conf alsa-ucm-conf file libasound2 libasound2-data libcanberra0 libexpat1 libgpm2 libltdl7 libmagic-mgc libmagic1 libmpdec2 libogg0 libpython3.8 libpython3.8-minimal
  libpython3.8-stdlib libreadline8 libtdb1 libvorbis0a libvorbisfile3 mime-support readline-common sound-theme-freedesktop vim-common vim-runtime xxd xz-utils
Suggested packages:
  libasound2-plugins alsa-utils libcanberra-gtk0 libcanberra-pulse gpm readline-doc ctags vim-doc vim-scripts
The following NEW packages will be installed:
  alsa-topology-conf alsa-ucm-conf file libasound2 libasound2-data libcanberra0 libexpat1 libgpm2 libltdl7 libmagic-mgc libmagic1 libmpdec2 libogg0 libpython3.8 libpython3.8-minimal
  libpython3.8-stdlib libreadline8 libtdb1 libvorbis0a libvorbisfile3 mime-support readline-common sound-theme-freedesktop vim vim-common vim-runtime xxd xz-utils
0 upgraded, 28 newly installed, 0 to remove and 2 not upgraded.
Need to get 13.1 MB of archives.
After this operation, 65.2 MB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu focal/main amd64 libmagic-mgc amd64 1:5.38-4 [218 kB]
Get:2 http://archive.ubuntu.com/ubuntu focal/main amd64 libmagic1 amd64 1:5.38-4 [75.9 kB]
Get:3 http://archive.ubuntu.com/ubuntu focal/main amd64 file amd64 1:5.38-4 [23.3 kB]
Get:4 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libexpat1 amd64 2.2.9-1ubuntu0.8 [75.6 kB]
Get:5 http://archive.ubuntu.com/ubuntu focal/main amd64 libmpdec2 amd64 2.4.2-3 [81.1 kB]
Get:6 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libpython3.8-minimal amd64 3.8.10-0ubuntu1~20.04.14 [720 kB]
Get:7 http://archive.ubuntu.com/ubuntu focal/main amd64 mime-support all 3.64ubuntu1 [30.6 kB]
Get:8 http://archive.ubuntu.com/ubuntu focal/main amd64 readline-common all 8.0-4 [53.5 kB]
Get:9 http://archive.ubuntu.com/ubuntu focal/main amd64 libreadline8 amd64 8.0-4 [131 kB]
Get:10 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libpython3.8-stdlib amd64 3.8.10-0ubuntu1~20.04.14 [1675 kB]
Get:11 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 xxd amd64 2:8.1.2269-1ubuntu5.31 [50.2 kB]
Get:12 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 vim-common all 2:8.1.2269-1ubuntu5.31 [85.2 kB]
Get:13 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 xz-utils amd64 5.2.4-1ubuntu1.1 [82.6 kB]
update-alternatives: warning: skip creation of /usr/share/man/fr/man1/ex.1.gz because associated file /usr/share/man/fr/man1/vim.1.gz (of link group ex) doesn't exist
update-alternatives: warning: skip creation of /usr/share/man/it/man1/ex.1.gz because associated file /usr/share/man/it/man1/vim.1.gz (of link group ex) doesn't exist
update-alternatives: warning: skip creation of /usr/share/man/ja/man1/ex.1.gz because associated file /usr/share/man/ja/man1/vim.1.gz (of link group ex) doesn't exist
update-alternatives: warning: skip creation of /usr/share/man/pl/man1/ex.1.gz because associated file /usr/share/man/pl/man1/vim.1.gz (of link group ex) doesn't exist
update-alternatives: warning: skip creation of /usr/share/man/ru/man1/ex.1.gz because associated file /usr/share/man/ru/man1/vim.1.gz (of link group ex) doesn't exist
update-alternatives: warning: skip creation of /usr/share/man/man1/ex.1.gz because associated file /usr/share/man/man1/vim.1.gz (of link group ex) doesn't exist
update-alternatives: using /usr/bin/vim.basic to provide /usr/bin/editor (editor) in auto mode
update-alternatives: warning: skip creation of /usr/share/man/da/man1/editor.1.gz because associated file /usr/share/man/da/man1/vim.1.gz (of link group editor) doesn't exist
update-alternatives: warning: skip creation of /usr/share/man/de/man1/editor.1.gz because associated file /usr/share/man/de/man1/vim.1.gz (of link group editor) doesn't exist
update-alternatives: warning: skip creation of /usr/share/man/fr/man1/editor.1.gz because associated file /usr/share/man/fr/man1/vim.1.gz (of link group editor) doesn't exist
update-alternatives: warning: skip creation of /usr/share/man/it/man1/editor.1.gz because associated file /usr/share/man/it/man1/vim.1.gz (of link group editor) doesn't exist
update-alternatives: warning: skip creation of /usr/share/man/ja/man1/editor.1.gz because associated file /usr/share/man/ja/man1/vim.1.gz (of link group editor) doesn't exist
update-alternatives: warning: skip creation of /usr/share/man/pl/man1/editor.1.gz because associated file /usr/share/man/pl/man1/vim.1.gz (of link group editor) doesn't exist
update-alternatives: warning: skip creation of /usr/share/man/ru/man1/editor.1.gz because associated file /usr/share/man/ru/man1/vim.1.gz (of link group editor) doesn't exist
update-alternatives: warning: skip creation of /usr/share/man/man1/editor.1.gz because associated file /usr/share/man/man1/vim.1.gz (of link group editor) doesn't exist
Processing triggers for libc-bin (2.31-0ubuntu9.16) ...
// 何も選択せずともインストール完了した。
// Dockerfileでインストールを行う際は、-yを付けるようにしたほうが良さそうとわかった。
```
