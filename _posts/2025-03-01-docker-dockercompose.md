---
title: "ブリッジネットワーク内で名前解決し通信を行う方法、DockerComposeとはを理解する"
last_modified_at: 2025-03-01T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### ブリッジネットワーク内で名前解決し通信を行う

- コンテナ名を指定するだけで、IP アドレスを割り出してくれる機能
  - コンテナが起動されるたびに IP アドレスは変動する、そのため IP アドレスを指定して通信するのは面倒。
  - 自分で作成したブリッジネットワークでしかこの機能を使うことはできない。

#### 実際に確認する

- my-net 内には my-ubuntu-2 と my-nginx-2 のコンテナが属している。
- my-ubuntu-2 から my-nginx-2 と通信を行う際、コンテナ名を指定して通信できるか確認する。
  - `curl http://my-nginx-2`を実行し、通信できるか確認する。

```
> docker container exec -it my-ubuntu-2 bash

root@09cf2edc3788:/# curl http://my-nginx-2
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
// レスポンスした結果が出力されているため、通信できている。
```

- 別ネットワークのコンテナと通信してみる。

```
root@09cf2edc3788:/# curl http://my-nginx-1

curl: (6) Could not resolve host: my-nginx-1
// my-nginx-1はmy-net内に存在しないコンテナのため、通信することができない。
```

#### ネットワークを削除

- `docker network rm <ネットワーク名>`で指定したネットワークを削除することができる。

```
> docker network rm my-net

Error response from daemon: error while removing network: network my-net id f8102650a7624b97eff2a2d8773e5c74d1b0018a9806003848ba0b6cedba14d7 has active endpoints
// my-netに属しているコンテナが起動しているため、削除することができない。

> docker container rm -f my-nginx-2
my-nginx-2
> docker container rm -f my-ubuntu-2
my-ubuntu-2
> docker network rm my-net
my-net
// my-netに属しているコンテナを削除し、再度ネットワークを削除した。
```

### DockerCompose とは？

- 複数の Docker コンテナを一度に操作することができるツールを指す。

#### DockerCompose がない場合

- 複数のコンテナの環境構築を行う際、多くの Docker コマンドを手動実行する必要がある。
- 同じ環境を構築するために、作業手順等を共有する必要がある。

#### DockerCompose がある場合

- docker-compose.yml を用意し、その中でコンテナの環境構築を行う。
  - `docker compose up`を実行すると、docker-compose.yml の設定通りに複数のコンテナの環境構築を自動的に行うことができる。
