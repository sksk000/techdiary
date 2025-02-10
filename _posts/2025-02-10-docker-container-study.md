---
title: "コンテナの操作を理解する"
last_modified_at: 2025-02-10T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### コンテナの起動

- `docker container run hello-world`で hello-world のコンテナを起動。

```
// コンテナを起動
> docker container run hello-world

//--------------イメージ内のコマンドを実行---------------------------------
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

```

- hello-world のコンテナのステータスを`docker container ls`で確認

```
> docker container ls
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
// hello-worldのコンテナがUPステータスではないため、何も表示されない
```

- hello-world のコンテナのステータスを`docker container ls -a`で確認

```
> docker container ls -a
CONTAINER ID   IMAGE          COMMAND                   CREATED        STATUS                    PORTS                    NAMES
7ed1e46c8f8e   hello-world    "/hello"                  2 hours ago    Exited (0) 2 hours ago                             ecstatic_nash
// 上記のコマンドと違い、すべてのコンテナを表示することが出来た。
// ステータスはExitedになっている。
```

### なぜコンテナを起動後、ステータスが Exited になるのか

- `docker container run hello-world`実行後は、以下の通りに遷移する
  - コンテナ起動(ステータス:Created)
  - イメージに登録されているコマンド実行(ステータス:Up)
  - コマンド実行終了(ステータス:Exited)

### コンテナのステータスが Up のままだとどうなるのか

- `docker container run nginx`でコンテナを起動する。

```
> docker container run nginx

atest: Pulling from library/nginx
84cade77a831: Download complete
24ff42a0d907: Download complete
976e8f6b25dd: Download complete
6c78b0ba1a32: Download complete
c558df217949: Download complete
c29f5b76f736: Download complete
e19db8451adb: Download complete
Digest: sha256:91734281c0ebfc6f1aea979cffeed5079cfe786228a71cc6f1f46a228cde6e34
Status: Downloaded newer image for nginx:latest
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2025/02/10 10:51:28 [notice] 1#1: using the "epoll" event method
2025/02/10 10:51:28 [notice] 1#1: nginx/1.27.4
2025/02/10 10:51:28 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14)
2025/02/10 10:51:28 [notice] 1#1: OS: Linux 5.15.167.4-microsoft-standard-WSL2
2025/02/10 10:51:28 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2025/02/10 10:51:28 [notice] 1#1: start worker processes
2025/02/10 10:51:28 [notice] 1#1: start worker process 28
2025/02/10 10:51:28 [notice] 1#1: start worker process 29
2025/02/10 10:51:28 [notice] 1#1: start worker process 30
2025/02/10 10:51:28 [notice] 1#1: start worker process 31
2025/02/10 10:51:28 [notice] 1#1: start worker process 32
2025/02/10 10:51:28 [notice] 1#1: start worker process 33
2025/02/10 10:51:28 [notice] 1#1: start worker process 34
2025/02/10 10:51:28 [notice] 1#1: start worker process 35

// コマンドを受け付けない状態になっている。
```

- 別のコマンドプロンプトにてコンテナの状態を確認する。

```
> docker container ls

CONTAINER ID   IMAGE     COMMAND                   CREATED              STATUS              PORTS     NAMES
cde490e8107f   nginx     "/docker-entrypoint.…"   About a minute ago   Up About a minute   80/tcp    trusting_payne
//実行すると、先程起動したnginxのコンテナが表示されている。
```

### コンテナを停止する

- 検証のため、最初に `docker container run` コマンドで nginx のコンテナを起動する
- その後、別のコマンドプロンプトで、`docker container ls` で起動されているか確認

```
> docker container run nginx

> docker container ls
CONTAINER ID   IMAGE     COMMAND                   CREATED         STATUS         PORTS     NAMES
4d148ff25d96   nginx     "/docker-entrypoint.…"   7 seconds ago   Up 6 seconds   80/tcp    affectionate_hermann
```

- `docker container stop` でコンテナを停止させる。
- `docker contaier ls -a` でコンテナの状態確認

```
>docker container stop <コンテナID(NAMEでもOK)>

>docker container ls
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

// docker container ls だけでは表示されない
```

```
> docker container ls -a
CONTAINER ID   IMAGE          COMMAND                   CREATED          STATUS                      PORTS
       NAMES
4d148ff25d96   nginx          "/docker-entrypoint.…"   7 minutes ago    Exited (0) 4 minutes ago
      affectionate_hermann

// -aをつけて、コンテナのステータス問わず状態確認
// ステータスがExitedになっている。
```

### コンテナを再起動する

- `docker container restart`でコンテナを再起動する。

```
> docker container restart <コンテナID(NAMEでもOK)>
affectionate_hermann

// nginxが再起動され、ステータスがUpに変更されている。
>docker container ls
CONTAINER ID   IMAGE     COMMAND                   CREATED          STATUS              PORTS     NAMES
4d148ff25d96   nginx     "/docker-entrypoint.…"   11 minutes ago   Up About a minute   80/tcp    affectionate_hermann

```

- 再起動した nginx コンテナ内のログを確認する。

```
> docker container logs affectionate_hermann。
//-------------------初回起動時のログ------------------------------------
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2025/02/10 11:11:06 [notice] 1#1: using the "epoll" event method
2025/02/10 11:11:06 [notice] 1#1: nginx/1.27.4
2025/02/10 11:11:06 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14)
2025/02/10 11:11:06 [notice] 1#1: OS: Linux 5.15.167.4-microsoft-standard-WSL2
2025/02/10 11:11:06 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2025/02/10 11:11:06 [notice] 1#1: start worker processes
･･･

//-------------------stopコマンド実行時のログ-----------
2025/02/10 11:13:31 [notice] 1#1: signal 3 (SIGQUIT) received, shutting down
･･･
2025/02/10 11:13:31 [notice] 1#1: exit

//-------------------restartコマンド実行時のログ-----------
･･･
2025/02/10 11:21:31 [notice] 1#1: using the "epoll" event method
2025/02/10 11:21:31 [notice] 1#1: nginx/1.27.4
2025/02/10 11:21:31 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14)
2025/02/10 11:21:31 [notice] 1#1: OS: Linux 5.15.167.4-microsoft-standard-WSL2
2025/02/10 11:21:31 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2025/02/10 11:21:31 [notice] 1#1: start worker processes
･･･
```

### コンテナを破棄する

- `docker container rm`でコンテナ削除を行う。
  - ステータスが Exited のコンテナのみ削除を行うことができる。

```
// lsで削除するコンテナを確認
今回は、コンテナIDcde490e8107fのコンテナを削除する
> docker container ls -a
CONTAINER ID   IMAGE          COMMAND                   CREATED             STATUS                         PORTS
    NAMES
4d148ff25d96   nginx          "/docker-entrypoint.…"   49 minutes ago      Up 38 minutes                  80/tcp
   affectionate_hermann
cde490e8107f   nginx          "/docker-entrypoint.…"   About an hour ago   Exited (0) About an hour ago
   trusting_payne

// 対象のコンテナを削除する
>docker container rm cde490e8107f
cde490e8107f
```

```
// 削除されていることを確認
> docker container ls -a
CONTAINER ID   IMAGE          COMMAND                   CREATED          STATUS                    PORTS                    NAMES
4d148ff25d96   nginx          "/docker-entrypoint.…"   52 minutes ago   Up 41 minutes             80/tcp                   affectionate_hermann
```

- 試しに、ステータスが Up のコンテナを削除する。

```
>docker container rm 4d148ff25d96
Error response from daemon: cannot remove container "/affectionate_hermann": container is running: stop the container before removing or force remove
// Errorになり削除出来ない。
```

- コンテナを stop してから削除する。

```
> docker container stop 4d148ff25d96
4d148ff25d96

>docker container rm 4d148ff25d96
4d148ff25d96
```

```
> docker container ls -a
CONTAINER ID   IMAGE          COMMAND                   CREATED        STATUS                    PORTS                    NAMES
// 削除したため、一覧に表示されない。
```
