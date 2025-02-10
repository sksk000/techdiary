---
title: "コンテナの操作を理解する"
last_modified_at: 2025-02-10T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### コンテナの起動

```
// コンテナを起動してみる。
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

// hello-worldのイメージは前取得しているため、コンテナを作成し実行だけ行われている。
// 存在しない場合は、DockerHubからイメージを取得後にコンテナ作成し実行される。

// hello-worldのコンテナがUPステータスではないため、何も表示されない
> docker container ls
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

// -aオプションをつけて再度実行
// 上記のコマンドと違い、すべてのコンテナを表示することが出来た。
// ステータスはExitedになっている。
> docker container ls -a
CONTAINER ID   IMAGE          COMMAND                   CREATED        STATUS                    PORTS                    NAMES
7ed1e46c8f8e   hello-world    "/hello"                  2 hours ago    Exited (0) 2 hours ago                             ecstatic_nash
```

### なぜコンテナを起動後、ステータスが Exited になるのか

- `docker container run hello-world`実行後は、以下の通りに遷移する
  - コンテナ起動(ステータス:Created)
  - イメージに登録されているコマンド実行(ステータス:Up)
  - コマンド実行終了(ステータス:Exited)

### コンテナのステータスが Up のままだとどうなるのか

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

// ここで止まっている。
// ステータスがUpの状態のままになっている。

// 別でコマンドプロンプトを開き確認
> docker container ls

//実行すると、先程起動したnginxのコンテナが表示されている。
CONTAINER ID   IMAGE     COMMAND                   CREATED              STATUS              PORTS     NAMES
cde490e8107f   nginx     "/docker-entrypoint.…"   About a minute ago   Up About a minute   80/tcp    trusting_payne
```

### コンテナを停止する

```
// nginxのコンテナを起動する
> docker container run nginx

// 別のコマンドプロンプトで、まずは起動されているか確認
> docker container ls
CONTAINER ID   IMAGE     COMMAND                   CREATED         STATUS         PORTS     NAMES
4d148ff25d96   nginx     "/docker-entrypoint.…"   7 seconds ago   Up 6 seconds   80/tcp    affectionate_hermann

// コンテナを停止させる。
>docker container stop コンテナID(NAMEでもOK)

// 再度ls使って停止されているか確認
>docker container ls
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

// -aをつけてステータスを確認
// ステータスがExitedになっている。
> docker container ls -a
CONTAINER ID   IMAGE          COMMAND                   CREATED          STATUS                      PORTS
       NAMES
4d148ff25d96   nginx          "/docker-entrypoint.…"   7 minutes ago    Exited (0) 4 minutes ago
      affectionate_hermann

```

### コンテナを再起動する

```
> docker container restart コンテナID(NAMEでもOK)
affectionate_hermann

// nginxが再起動され、ステータスがUpに変更されている。
>docker container ls
CONTAINER ID   IMAGE     COMMAND                   CREATED          STATUS              PORTS     NAMES
4d148ff25d96   nginx     "/docker-entrypoint.…"   11 minutes ago   Up About a minute   80/tcp    affectionate_hermann

// nginxは起動してるかログを確認してみる。
> docker container logs affectionate_hermann

// 確認すると、以下の様にログを確認することが出来た。
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
2025/02/10 11:11:06 [notice] 1#1: start worker process 29
2025/02/10 11:11:06 [notice] 1#1: start worker process 30
2025/02/10 11:11:06 [notice] 1#1: start worker process 31
2025/02/10 11:11:06 [notice] 1#1: start worker process 32
2025/02/10 11:11:06 [notice] 1#1: start worker process 33
2025/02/10 11:11:06 [notice] 1#1: start worker process 34
2025/02/10 11:11:06 [notice] 1#1: start worker process 35
2025/02/10 11:11:06 [notice] 1#1: start worker process 36
2025/02/10 11:13:31 [notice] 1#1: signal 3 (SIGQUIT) received, shutting down
2025/02/10 11:13:31 [notice] 29#29: gracefully shutting down
2025/02/10 11:13:31 [notice] 30#30: gracefully shutting down
2025/02/10 11:13:31 [notice] 31#31: gracefully shutting down
2025/02/10 11:13:31 [notice] 30#30: exiting
2025/02/10 11:13:31 [notice] 32#32: gracefully shutting down
2025/02/10 11:13:31 [notice] 29#29: exiting
2025/02/10 11:13:31 [notice] 32#32: exiting
2025/02/10 11:13:31 [notice] 33#33: gracefully shutting down
2025/02/10 11:13:31 [notice] 31#31: exiting
2025/02/10 11:13:31 [notice] 36#36: gracefully shutting down
2025/02/10 11:13:31 [notice] 29#29: exit
2025/02/10 11:13:31 [notice] 30#30: exit
2025/02/10 11:13:31 [notice] 33#33: exiting
2025/02/10 11:13:31 [notice] 32#32: exit
2025/02/10 11:13:31 [notice] 36#36: exiting
2025/02/10 11:13:31 [notice] 31#31: exit
2025/02/10 11:13:31 [notice] 33#33: exit
2025/02/10 11:13:31 [notice] 36#36: exit
2025/02/10 11:13:31 [notice] 34#34: gracefully shutting down
2025/02/10 11:13:31 [notice] 35#35: gracefully shutting down
2025/02/10 11:13:31 [notice] 35#35: exiting
2025/02/10 11:13:31 [notice] 34#34: exiting
2025/02/10 11:13:31 [notice] 34#34: exit
2025/02/10 11:13:31 [notice] 35#35: exit
2025/02/10 11:13:31 [notice] 1#1: signal 17 (SIGCHLD) received from 35
2025/02/10 11:13:31 [notice] 1#1: worker process 35 exited with code 0
2025/02/10 11:13:31 [notice] 1#1: signal 29 (SIGIO) received
2025/02/10 11:13:31 [notice] 1#1: signal 17 (SIGCHLD) received from 29
2025/02/10 11:13:31 [notice] 1#1: worker process 29 exited with code 0
2025/02/10 11:13:31 [notice] 1#1: worker process 36 exited with code 0
2025/02/10 11:13:31 [notice] 1#1: signal 29 (SIGIO) received
2025/02/10 11:13:31 [notice] 1#1: signal 17 (SIGCHLD) received from 31
2025/02/10 11:13:31 [notice] 1#1: worker process 31 exited with code 0
2025/02/10 11:13:31 [notice] 1#1: signal 29 (SIGIO) received
2025/02/10 11:13:31 [notice] 1#1: signal 17 (SIGCHLD) received from 34
2025/02/10 11:13:31 [notice] 1#1: worker process 33 exited with code 0
2025/02/10 11:13:31 [notice] 1#1: worker process 34 exited with code 0
2025/02/10 11:13:31 [notice] 1#1: signal 29 (SIGIO) received
2025/02/10 11:13:31 [notice] 1#1: signal 17 (SIGCHLD) received from 30
2025/02/10 11:13:31 [notice] 1#1: worker process 30 exited with code 0
2025/02/10 11:13:31 [notice] 1#1: signal 29 (SIGIO) received
2025/02/10 11:13:31 [notice] 1#1: signal 17 (SIGCHLD) received from 32
2025/02/10 11:13:31 [notice] 1#1: worker process 32 exited with code 0
2025/02/10 11:13:31 [notice] 1#1: exit
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: IPv6 listen already enabled
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2025/02/10 11:21:31 [notice] 1#1: using the "epoll" event method
2025/02/10 11:21:31 [notice] 1#1: nginx/1.27.4
2025/02/10 11:21:31 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14)
2025/02/10 11:21:31 [notice] 1#1: OS: Linux 5.15.167.4-microsoft-standard-WSL2
2025/02/10 11:21:31 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2025/02/10 11:21:31 [notice] 1#1: start worker processes
2025/02/10 11:21:31 [notice] 1#1: start worker process 22
2025/02/10 11:21:31 [notice] 1#1: start worker process 23
2025/02/10 11:21:31 [notice] 1#1: start worker process 24
2025/02/10 11:21:31 [notice] 1#1: start worker process 25
2025/02/10 11:21:31 [notice] 1#1: start worker process 26
2025/02/10 11:21:31 [notice] 1#1: start worker process 27
2025/02/10 11:21:31 [notice] 1#1: start worker process 28
2025/02/10 11:21:31 [notice] 1#1: start worker process 29

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

// 削除されていることを確認
> docker container ls -a
CONTAINER ID   IMAGE          COMMAND                   CREATED          STATUS                    PORTS                    NAMES
4d148ff25d96   nginx          "/docker-entrypoint.…"   52 minutes ago   Up 41 minutes             80/tcp                   affectionate_hermann


// 試しに、ステータスがUpのコンテナを削除してみる。
// Errorになり削除出来ない。
>docker container rm 4d148ff25d96
Error response from daemon: cannot remove container "/affectionate_hermann": container is running: stop the container before removing or force remove

// stopしコンテナを削除してみる。
// 削除成功
> docker container stop 4d148ff25d96
4d148ff25d96
>docker container rm 4d148ff25d96
4d148ff25d96

// 削除したため、一覧に表示されない。
> docker container ls -a
CONTAINER ID   IMAGE          COMMAND                   CREATED        STATUS                    PORTS                    NAMES


```
