---
title: "ブリッジネットワーク内の通信を行う方法について理解する"
last_modified_at: 2025-02-28T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### ブリッジネットワーク内で通信を行う

- 以下の環境で通信を行う

#### ブリッジネットワーク

- bridge(デフォルト)
  - my-nginx-1
  - my-ubuntu-1
- my-net
  - my-nginx-2
  - my-ubuntu-2

#### my-ubuntu

- Dockerfile

```
FROM ubuntu:22.04
RUN apt update && RUN apt install -y iputils-png curl
CMD ["bash"]
```

### 事前準備(my-ubuntu)

- Dockerfile をでビルドする
  - 今回はイメージに名前を付けたいので、-t オプションを付ける。

```
PS D:\testdocker_1> docker image build -t my-ubuntu .
[+] Building 82.7s (5/5) FINISHED                                                                                                                                                    docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => => transferring dockerfile: 124B                                                                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:22.04                                                                                                                                      0.8s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 2B                                                                                                                                                                      0.0s
 => CACHED [1/2] FROM docker.io/library/ubuntu:22.04@sha256:ed1544e454989078f5dec1bfdabd8c5cc9c48e0705d07b678ab6ae3fb61952d2                                                                         0.1s
 => => resolve docker.io/library/ubuntu:22.04@sha256:ed1544e454989078f5dec1bfdabd8c5cc9c48e0705d07b678ab6ae3fb61952d2                                                                                0.1s
 => ERROR [2/2] RUN apt update && RUN apt install -y iputils-ping curl                                                                                                                              81.6s
------
 > [2/2] RUN apt update && RUN apt install -y iputils-ping curl:
0.381
0.381 WARNING: apt does not have a stable CLI interface. Use with caution in scripts.
0.381
0.957 Get:1 http://archive.ubuntu.com/ubuntu jammy InRelease [270 kB]
0.984 Get:2 http://security.ubuntu.com/ubuntu jammy-security InRelease [129 kB]
1.827 Get:3 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [2649 kB]
2.102 Get:4 http://archive.ubuntu.com/ubuntu jammy-updates InRelease [128 kB]
2.498 Get:5 http://archive.ubuntu.com/ubuntu jammy-backports InRelease [127 kB]
2.891 Get:6 http://archive.ubuntu.com/ubuntu jammy/main amd64 Packages [1792 kB]
7.676 Get:7 http://archive.ubuntu.com/ubuntu jammy/universe amd64 Packages [17.5 MB]
9.031 Get:8 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [1235 kB]
12.31 Get:9 http://security.ubuntu.com/ubuntu jammy-security/multiverse amd64 Packages [45.2 kB]
12.45 Get:10 http://security.ubuntu.com/ubuntu jammy-security/restricted amd64 Packages [3688 kB]
55.14 Get:11 http://archive.ubuntu.com/ubuntu jammy/multiverse amd64 Packages [266 kB]
55.90 Get:12 http://archive.ubuntu.com/ubuntu jammy/restricted amd64 Packages [164 kB]
56.26 Get:13 http://archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [1533 kB]
60.37 Get:14 http://archive.ubuntu.com/ubuntu jammy-updates/multiverse amd64 Packages [53.3 kB]
61.12 Get:15 http://archive.ubuntu.com/ubuntu jammy-updates/restricted amd64 Packages [3830 kB]
72.27 Get:16 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [2957 kB]
79.83 Get:18 http://archive.ubuntu.com/ubuntu jammy-backports/main amd64 Packages [81.4 kB]
80.03 Fetched 36.5 MB in 1min 20s (458 kB/s)
80.03 Reading package lists...
81.31 Building dependency tree...
81.53 Reading state information...
81.56 11 packages can be upgraded. Run 'apt list --upgradable' to see them.
81.57 /bin/sh: 1: RUN: not found
------
Dockerfile:2
--------------------
   1 |     FROM ubuntu:22.04
   2 | >>> RUN apt update && RUN apt install -y iputils-ping curl
   3 |     CMD ["bash"]
--------------------
ERROR: failed to solve: process "/bin/sh -c apt update && RUN apt install -y iputils-ping curl" did not complete successfully: exit code: 127

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/46vdh54h7p7axik7b8t738bnm
// なにかエラーが起きているようだ。
// 原因は、RUNコマンドを2つ書かれていたため。
// 一番最初のRUNコマンドだけ残し、後のRUNコマンドは削除
```

- 再度ビルド

```
PS D:\testdocker_1> docker image build -t my-ubuntu .
[+] Building 91.5s (7/7) FINISHED                                                                                                                                                    docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                 0.0s
 => => transferring dockerfile: 120B                                                                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:22.04                                                                                                                                      1.5s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                        0.0s
 => [internal] load .dockerignore                                                                                                                                                                    0.0s
 => => transferring context: 2B                                                                                                                                                                      0.0s
 => CACHED [1/2] FROM docker.io/library/ubuntu:22.04@sha256:ed1544e454989078f5dec1bfdabd8c5cc9c48e0705d07b678ab6ae3fb61952d2                                                                         0.0s
 => => resolve docker.io/library/ubuntu:22.04@sha256:ed1544e454989078f5dec1bfdabd8c5cc9c48e0705d07b678ab6ae3fb61952d2                                                                                0.0s
 => [2/2] RUN apt update && apt install -y iputils-ping curl                                                                                                                                        85.7s
 => exporting to image                                                                                                                                                                               4.0s
 => => exporting layers                                                                                                                                                                              3.1s
 => => exporting manifest sha256:a149097a523ef94384954ccc676d23c1ded9e156e015cb38e3a2302ebf8a05af                                                                                                    0.0s
 => => exporting config sha256:1d0c2a94cbff6752e120f7c9b4a579f76751e7100dd108043dd93a9a351559e9                                                                                                      0.0s
 => => exporting attestation manifest sha256:bb5ab12834e9fd01cb0f077a9ac05f7179112a489dd40ef8d24661529256860d                                                                                        0.1s
 => => exporting manifest list sha256:a79233daa144f58910e63595d4a83715ca94f32bba085be709f4fd6d1cb220f4                                                                                               0.0s
 => => naming to docker.io/library/my-ubuntu:latest                                                                                                                                                  0.0s
 => => unpacking to docker.io/library/my-ubuntu:latest                                                                                                                                               0.6s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/io784lrq6l98j4owcl9gxh5kg
```

- ビルド完了したため、コンテナを起動する。
  - 今回は、デタッチドモードで起動する。
  - 先ほどビルドしたイメージを利用し、コンテナを起動する。

```
PS D:\testdocker_1> docker container run -itd --name my-ubuntu-1 my-ubuntu

fb67ad3f86a5def90a116fa33f939ad7d93d7825930ff1b849e730f3b73c93ed
PS D:\testdocker_1> docker container ls
CONTAINER ID   IMAGE       COMMAND   CREATED         STATUS         PORTS     NAMES
fb67ad3f86a5   my-ubuntu   "bash"    7 seconds ago   Up 6 seconds             my-ubuntu-1
```

- 今度は my-net ブリッジネットワークに所属する my-ubuntu-2 コンテナを起動する。

```
S D:\testdocker_1> docker container run -itd --name my-ubuntu-2 --network my-net my-ubuntu

09cf2edc3788b708692e236d3c18f96814a054c3853eeda6fbc1a9d0ac7a69b8

```

- どちらのコンテナも起動したため、確認として`docker container ls`で確認

```

PS D:\testdocker_1> docker container ls

CONTAINER ID   IMAGE       COMMAND   CREATED         STATUS         PORTS     NAMES
09cf2edc3788   my-ubuntu   "bash"    7 seconds ago   Up 7 seconds             my-ubuntu-2
fb67ad3f86a5   my-ubuntu   "bash"    6 minutes ago   Up 6 minutes             my-ubuntu-1
// どちらも正常に起動していることがわかった。
```

### 事前準備(my-nginx)

- nginx-1、nginx-2 コンテナを起動する
  - nginx-2 コンテナは my-net ブリッジネットワークに所属する。

```
PS D:\testdocker_1> docker container run -d --name my-nginx-1 --network my-net nginx
df41ec66d73b6969f1e080c1fbc66310ae2d26a23c0613d9be147b440f953d3b

PS D:\testdocker_1> docker container run -d --name my-nginx-2 --network my-net nginx
77ce694c10d98783cfa289b9681d65ee646e822aef6b7530c8fc52faf265e6d3

```

```
PS D:\testdocker_1> docker network inspect bridge
[
    {
      ...
        "Containers": {
            "df41ec66d73b6969f1e080c1fbc66310ae2d26a23c0613d9be147b440f953d3b": {
                "Name": "my-nginx-1",
                "EndpointID": "0b782e565ed372b6707eb40d996ac249d7335f1298202e33d21a8a92357582b4",
                "MacAddress": "02:42:ac:11:00:03",
                "IPv4Address": "172.17.0.3/16",
                "IPv6Address": ""
            },
            "fb67ad3f86a5def90a116fa33f939ad7d93d7825930ff1b849e730f3b73c93ed": {
                "Name": "my-ubuntu-1",
                "EndpointID": "0d7e213c21fc68ac90eedbb729171c2c0a8be54874fcde8d4182d0edbaa205d0",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
        ...
]
// bridgeネットワークにはmy-nginx-1,my-ubuntu-1が所属している。
```

```
PS D:\testdocker_1> docker network inspect my-net
[
    {
      ...
        "Containers": {
            "09cf2edc3788b708692e236d3c18f96814a054c3853eeda6fbc1a9d0ac7a69b8": {
                "Name": "my-ubuntu-2",
                "EndpointID": "d2752f669cd37919509185c869889d40d9d9efd6f102c1d4b8a5b7ad98678bca",
                "MacAddress": "02:42:ac:13:00:02",
                "IPv4Address": "172.19.0.2/16",
                "IPv6Address": ""
            },
            "77ce694c10d98783cfa289b9681d65ee646e822aef6b7530c8fc52faf265e6d3": {
                "Name": "my-nginx-2",
                "EndpointID": "9cc2d43a8d55d56e2ca15612fd0200618c04b30d294a2fdfbe530a5bf6395203",
                "MacAddress": "02:42:ac:13:00:03",
                "IPv4Address": "172.19.0.3/16",
                "IPv6Address": ""
            }
        },
        ...
]
// my-netブリッジネットワークには、my-ubuntu-2,my-nginx-2が所属している。
```

### ブリッジネットワーク内で通信する

- コンテナの IP アドレス一覧
  - bridge
    - my-ubuntu-1
      - 172.17.0.2
    - my-nginx-1
      - 172.17.0.3
  - my-net
    - my-ubuntu-2
      - 172.19.0.2
    - my-nginx-2
      - 172.19.0.3

#### my-ubuntu-1 から my-nginx-1 にアクセスする

- `docker container exec -it my-ubuntu-1 bash`で my-ubuntu-1 に入り、`curl <IPアドレス>`で my-nginx-1 にアクセスする。

```
PS D:\testdocker_1> docker container exec -it my-ubuntu-1 bash

root@fb67ad3f86a5:/# curl http://172.17.0.3

<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
// nginxのコンテナはGETのリクエストが送られた場合、上記のHTMLを返す仕組み。
// そのため、my-ubuntu-1コンテナとmy-nginx-1コンテナは通信できていることが確認できた。
```

#### my-ubuntu-1 から my-nginx-2 にアクセスする

- `curl`コマンドで通信を行う。

```
root@fb67ad3f86a5:/# curl http://172.19.0.3
// この状態で固まってしまった。
// 結果、my-nginx-2は同じネットワークに属していないため、通信することはできない。
```

#### my-ubuntu-2 から my-nginx-2 にアクセスする

- `curl <IPアドレス>`で通信を行う。

```
PS D:\testdocker_1> docker container exec -it my-ubuntu-2 bash

root@09cf2edc3788:/# curl http://172.19.0.3

<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

### 結果

- 以下の通りに動作することがわかった。
  - my-ubuntu-1(bridge) ⇔ my-nginx-1(bridge)
    - 同じネットワークのため、通信することができた。
  - my-ubuntu-1(bridge) ⇔ my-nginx-2(my-net)
    - 所属しているネットワークが異なるため、通信できなかった。
  - my-ubuntu-2(my-net) ⇔ my-ubuntu-2(my-net)
    - 同じネットワークのため、通信することができた。
