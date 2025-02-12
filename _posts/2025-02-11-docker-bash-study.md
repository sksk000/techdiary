---
title: "bashについて理解する"
last_modified_at: 2025-02-11T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### bash とは？

- Unix 系の OS で使用するシェルの一種
  - ユーザが行いたい操作を、シェルを通じて OS に指示を出す事ができる。
  - Windows ではコマンドプロンプト、PowerShell2 種類のシェルが存在している。

### Ubuntu 環境を作成し、bash を使って操作する

- `docker container run -it ubuntu:20.04`を実行する。

```
> docker container run -it ubuntu:20.04
root@0c16f919003b:/#
// -itオプションを付けると、rootでubuntuのコンテナにアクセスできる。
```

- 試しにコマンドをたたいてみる。

```
root@0c16f919003b:/# pwd
/
root@0c16f919003b:/# ls
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

- ubuntu コンテナから抜ける。

```
root@0c16f919003b:/# exit
exit
>
```

### docker container run -it について

- `docker container run -i -t`をつなげた状態
- 試しに-it オプションを設定せず、`docker container run ubuntu:20.04`で実行し、コンテナの状態を確認する。

```
> docker container run ubuntu:20.04

> docker container ls
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
// Upになっていない。

> docker container ls -a
CONTAINER ID   IMAGE          COMMAND                   CREATED          STATUS                      PORTS
NAMES
4770c66ac65f   ubuntu:20.04   "/bin/bash"               46 seconds ago   Exited (0) 45 seconds ago
trusting_moser
// bashを実行したが、bashに対して何も行っていないためbashが終了してステータスもExitedとなっている。
// 標準入力が行えない状態ため、コマンドプロンプトからコンテナに対して標準入力を行える状態にする必要がある。
```

- 試しに-i をオプションにつけて`docker container run -i ubuntu:20.04`を実行する。

```
> docker container run -i ubuntu:20.04
ls
/bin/bash: line 1: $'ls\r': command not found
// ubuntuのコンテナに対してbashのコマンドを実行出来ていそう。
// -iは標準入力を受け付けてくれるコマンドだということがわかった。
```

- 試しに-it をオプションについて`docker container run -it ubuntu:20.04`を実行する。

```
> docker container run -it ubuntu:20.04

root@c0afb3745b60:/# ls
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
// ubuntuのコンテナ内でlsコマンドを実行すると、コンテナ内のディレクトリ一覧が表示されている。
// -tのオプションがつくことで、先ほどの-iよりも出力結果が見やすくなった。
```

### コンテナ起動時に実行されるコマンドを変更

- まずは、`docker image inspect ubuntu:20.04`でイメージに設定されているデフォルトコマンドを調べる。

```
> docker image inspect ubuntu:20.04

[
    {
        ･･･
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
           ･･･
        },
        ･･･
    }
]
// configのCmdにデフォルトのコマンドが書かれている。
// デフォルトは'/bin/bash'が実行される。
```

- `docker container run -it ubuntu:20.04`の後に実行したいコマンドを設定すると、起動後そのコマンドが実行される。

```
> docker container run -it ubuntu:20.04 pwd

/
// ubuntuのコンテナ内でpwdが実行され、カレントディレクトリが表示されている。

> docker container run -it ubuntu:20.04 ls

bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys                       tmp  usr  var
// ubuntuのコンテナ内でlsが実行され、ルートディレクトリ内のディレクトリ一覧が表示されている。
```

- コンテナ側で定義されていないコマンドを実行するとエラーが発生する。

```
> docker container run -it ubuntu:20.04 hoge

docker: Error response from daemon: failed to create task for container: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "hoge": executable file not found in $PATH: unknown.
// hogeというコマンドはないとエラーが発生している。
```
