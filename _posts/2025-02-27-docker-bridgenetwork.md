---
title: "ブリッジネットワークに接続、新しくブリッジネットワークを作成する方法を理解"
last_modified_at: 2025-02-27T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### ブリッジネットワークに接続する

- `docker network inspect <ネットワーク名>`で引数に設定したネットワーク名の詳細情報を確認する。
  - Containers に接続されているコンテナ一覧が表示される。

```
> docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "d3de5bed93c95cf936348ee42f6d68a078267393f858011503ed4ff0fad71a41",
        "Created": "2025-02-27T08:31:50.5666169Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
```

- 適当なコンテナを起動し、`docker network inspect <イメージ名>`で確認する。

```
> docker container run -itd --rm --name my-nginx-1 nginx
f9bca6a3a8980078bc126b99cbd5226f47f2345796dfc64c253ee7e11ab1017a

> docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "d3de5bed93c95cf936348ee42f6d68a078267393f858011503ed4ff0fad71a41",
        "Created": "2025-02-27T08:31:50.5666169Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "f9bca6a3a8980078bc126b99cbd5226f47f2345796dfc64c253ee7e11ab1017a": {
                "Name": "my-nginx-1",
                "EndpointID": "c3be2d570e1f025a423cdd110e866f69babbd8a320dfc8a0244fd279bc07eabf",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
// 先ほどとは違い、起動したコンテナ情報がContainersにかかれている。
// 起動中且つbridgeに所属しているコンテナのみ記載されることがわかった。
```

- `docker network create <ネットワーク名>`で新しくブリッジネットワークを作成する。

```
> docker network create my-net
f8102650a7624b97eff2a2d8773e5c74d1b0018a9806003848ba0b6cedba14d7

> docker network ls
NETWORK ID     NAME               DRIVER    SCOPE
d3de5bed93c9   bridge             bridge    local
53713c5bc16a   host               host      local
f8102650a762   my-net             bridge    local
5e56067c99c1   none               null      local
e6147610759a   travelog_default   bridge    local
// 先ほど作成したmy-netが表示されていることがわかる。
```

- 作成した my-net にコンテナを所属する。
  - `docker container run --network <所属するネットワーク名> <イメージ名>`で、コンテナ起動時に所属するネットワーク名を設定することができる。

```
> docker container run -itd --rm --name my-nginx-2 --network my-net nginx
8031dd43cc82c110e61dce10bd597ec599483f18020f301ceebcbf625a2a54b8

> docker container ls
CONTAINER ID   IMAGE     COMMAND                   CREATED         STATUS         PORTS     NAMES
8031dd43cc82   nginx     "/docker-entrypoint.…"   7 seconds ago   Up 6 seconds   80/tcp    my-nginx-2
f9bca6a3a898   nginx     "/docker-entrypoint.…"   2 hours ago     Up 2 hours     80/tcp    my-nginx-1
// 現在2つのコンテナが起動している。

> docker network inspect my-net
[
    {
        "Name": "my-net",
        "Id": "f8102650a7624b97eff2a2d8773e5c74d1b0018a9806003848ba0b6cedba14d7",
        "Created": "2025-02-27T10:44:04.135515113Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.19.0.0/16",
                    "Gateway": "172.19.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "8031dd43cc82c110e61dce10bd597ec599483f18020f301ceebcbf625a2a54b8": {
                "Name": "my-nginx-2",
                "EndpointID": "25921aefd238b710c6da44611fce10766e514c80971477f572a939bf4b025bdc",
                "MacAddress": "02:42:ac:13:00:02",
                "IPv4Address": "172.19.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
// 先ほど起動したmy-nginx-2コンテナがContainersに追加されている。

> docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "d3de5bed93c95cf936348ee42f6d68a078267393f858011503ed4ff0fad71a41",
        "Created": "2025-02-27T08:31:50.5666169Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "f9bca6a3a8980078bc126b99cbd5226f47f2345796dfc64c253ee7e11ab1017a": {
                "Name": "my-nginx-1",
                "EndpointID": "c3be2d570e1f025a423cdd110e866f69babbd8a320dfc8a0244fd279bc07eabf",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
// my-nginx-1がContainersに追加されている、my-nginx-2は追加されていない。
```
