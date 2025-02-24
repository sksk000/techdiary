---
title: "Dockerのボリュームについて理解する"
last_modified_at: 2025-02-24T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### コンテナ内のデータを永続化させる機能

- ボリューム
- バインドマウント

### ボリュームとは？

- コンテナ・ホスト PC から分離されている領域のことを指す。
  - コンテナを削除しても、ボリュームは削除されない。
  - 複数のコンテナからボリュームにアクセス可能
  - ホスト PC から分離しているため、ホスト PC からボリュームにアクセスすることはできない。

### バインドマウントとは？

- ホスト PC の任意のディレクトリをコンテナにマウントする仕組み。
- ボリュームと違い、データはホスト PC に保存される。
  - そのため、ホスト PC からデータにアクセスすることができる。

### ボリュームの基本的なコマンドを試す

- `docker volume create <ボリューム名>`でボリュームを作成する。
- `docker volume ls`でボリューム一覧を確認し、ボリュームが作成されたかどうか確認する。

```
PS D:\testdocker_1> docker volume create my-volume

my-volume

PS D:\testdocker_1> docker volume ls
// ボリュームが作成されたかどうか確認する。

DRIVER    VOLUME NAME
local     my-volume
```

- `docker volume inspect <ボリューム名>`でボリュームの詳細情報を表示できる。
- 以下の情報を確認することができる。
  - 作成日時
  - ボリュームのドライバ名(ローカルやクラウドストレージも存在する。)
  - ボリューム領域のパス
  - ボリューム名
  - オプション(ボリューム作成時のオプションが表示される。)
  - ボリュームのスコープ(ローカル環境だと local、クラウド環境だと global が表示される。)

```
PS D:\testdocker_1> docker volume inspect my-volume

[
    {
        "CreatedAt": "2025-02-24T06:00:32Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/my-volume/_data",
        "Name": "my-volume",
        "Options": null,
        "Scope": "local"
    }
]
```

- ボリューム領域のパス(/var/lib/docker/volumes/my-volume/\_data)に`cd`コマンドで移動してみる。

```
PS D:\testdocker_1> cd /var/lib/docker/volumes/my-volume/_data

cd : パス 'D:\var\lib\docker\volumes\my-volume\_data' が存在しないため検出できません。
発生場所 行:1 文字:1
+ cd /var/lib/docker/volumes/my-volume/_data
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ObjectNotFound: (D:\var\lib\dock...my-volume\_data:String) [Set-Location], ItemNotFoundException
    + FullyQualifiedErrorId : PathNotFound,Microsoft.PowerShell.Commands.SetLocationCommand
// フォルダが見つからないため移動することができない。
// 理由としては、ホストPC上でDockerのボリューム領域にアクセスすることができない仕様のため。

```

- `docker volume rm <ボリューム名>`でボリュームの削除を行う。
- `docker volume ls`でボリューム削除されているか確認。

```
PS D:\testdocker_1> docker volume rm my-volume

my-volume

PS D:\testdocker_1> docker volume ls

DRIVER    VOLUME NAME
// 先ほど削除したボリュームは表示されなくなった。
```

### 試しにボリュームを使い、データを永続化する。

- 以下の環境で試す。
  - コンテナ
    - contaner-1
      - マウント先:/app1
    - container-2
      - マウント先:/app2
  - ボリューム
    - my-volume
      - ルートディレクトリに hello.txt が存在する。

#### 上記の環境を構築する

- `docker volume <ボリューム名>`でボリュームを作成する。

```
PS D:\testdocker_1> docker volume create my-volume
my-volume
PS D:\testdocker_1> docker volume ls
DRIVER    VOLUME NAME
local     my-volume
```

- `docker container run -it -v <ボリューム名:マウント先パス> --name <コンテナ名> <イメージ名>`でコンテナを起動する。
- container-1 のコンテナを作成

```
PS D:\testdocker_1> docker container run -it -v my-volume:/app1 --name container-1 ubuntu:20.04
// マウント先を設定する際は絶対パスで指定を行う。

root@13d6480d9c9a:/# cd /app1
root@13d6480d9c9a:/app1# touch hello.txt
root@13d6480d9c9a:/app1# ls
hello.txt
// /app1にhello.txtを作成する。
```

- container-2 のコンテナを作成

```
PS D:\testdocker_1> docker container run -it -v my-volume:/app2 --name container-2 ubuntu:20.04

root@32c31336177b:/# cd /app2
root@32c31336177b:/app2# ls
hello.txt
// 先ほどcontainer-1内で作成したhello.txtが存在している。
```

#### ボリューム内のファイルを編集する

- container-2 にある hello.txt を編集し、container-1 で確認する。

```
// container-2
root@32c31336177b:/app2# echo "Hello World from container-2" > hello.txt

// container-1
root@13d6480d9c9a:/app1# cat hello.txt
Hello World from container-2
// container-2で編集した内容が表示されている。
```
