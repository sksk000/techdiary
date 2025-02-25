---
title: "バインドマウントについて理解する"
last_modified_at: 2025-02-25T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### バインドマウントとは？

- ホスト PC の任意のディレクトリをコンテナにマウントする仕組み。
- ボリュームと違い、データはホスト PC に保存される。
  - そのため、ホスト PC からデータにアクセスすることができる。

### バインドマウントを使用し、データの永続化を行う

- 以下のディレクトリ構成で確認する。

```
ディレクトリ: D:\testdocker_1


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        2025/02/25     12:18                my-dir
-a----        2025/02/25     12:17            119 Dockerfile


ディレクトリ: D:\testdocker_1\my-dir


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        2025/02/25     12:17             11 hello.txt
// hello.txtにはHello Worldが書かれている。
```

- `docker container run -it -v D:\testdocker_1\my-dir:/app ubuntu:20.04`でコンテナを起動する。

```
PS D:\testdocker_1\my-dir> docker container run -it -v D:\testdocker_1\my-dir:/app ubuntu:20.04

root@43472eef5e01:/# cd /app
root@43472eef5e01:/app# cat hello.txt
Hello Worldroot@43472eef5e01:/app#
// /appディレクトリに移動し、hello.txtの中身を確認すると
// Hello Worldが書かれている。
```

- データが同期されていることを確認する。
- hello.txt 内の文字列を変更する。

```
Hello Worldroot@43472eef5e01:/app# echo "HELLO" > hello.txt
root@43472eef5e01:/app# cat hello.txt
HELLO
// hello.txt 内の文字列をHELLOに変更する

// 変更後、ホストPCのhello.txtを確認すると、HELLOが書き込まれていることを確認した。
```

- バインド先を絶対パスにて設定する決まりがあるが以下の方法でも実行可能
- Windows だと、`docker container run -it -v ${PWD}/my-dir:/app ubuntu:20.04`
- MAC 環境だと、`docker container run -it -v $(PWD)/my-dir:/app ubuntu:20.04`となる。

### ボリュームとバインドマウントをどう使い分けるか？

#### ボリューム

- 永続化したい場合は使っておけば問題なし(Docker 公式推奨)
- ホスト PC 側からアクセスをしない場合に使用する。

#### バインドマウント

- ホスト PC から頻繁にアクセスしたいデータがある場合に使用する。
