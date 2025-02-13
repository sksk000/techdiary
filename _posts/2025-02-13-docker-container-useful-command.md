---
title: "docker内のコンテナを整理したい"
last_modified_at: 2024-09-19T00:00:00+0900
tags:
  - Docker
---

## 学んだこと
### なぜコンテナを整理するのか
- 重要なコンテナかどうか判断しにくくなる。
### コンテナ内を整理する
- どのようなコンテナが存在しているか確認する。
```
> docker container ls -a
CONTAINER ID   IMAGE          COMMAND                   CREATED             STATUS                      PORTS                    NAMES
566bae48796f   ubuntu:20.04   "/bin/bash"               17 minutes ago      Exited (0) 17 minutes ago
                     my_ubnuntu
1c0ba068ad88   ubuntu:20.04   "/bin/bash"               About an hour ago   Up About an hour
                     recursing_bartik
72cd1a8e7c12   ubuntu:20.04   "hoge"                    40 hours ago        Created
                     admiring_shirley
1d5588ef5074   ubuntu:20.04   "ls"                      40 hours ago        Exited (0) 40 hours ago
                     magical_hypatia
709ed89bf7db   ubuntu:20.04   "pwd"                     40 hours ago        Exited (0) 40 hours ago
                     elastic_roentgen
c0afb3745b60   ubuntu:20.04   "/bin/bash"               40 hours ago        Exited (130) 40 hours ago
                     modest_wright
fc075adb8414   ubuntu:20.04   "/bin/bash"               40 hours ago        Exited (127) 40 hours ago
                     festive_blackwell
dc0d397e87f9   ubuntu:20.04   "/bin/bash"               40 hours ago        Exited (127) 40 hours ago
                     inspiring_dirac
cf85c0483189   ubuntu:20.04   "/bin/bash"               40 hours ago        Exited (127) 40 hours ago
                     gifted_kilby
8adcacbcf09c   ubuntu:20.04   "/bin/bash"               40 hours ago        Exited (127) 40 hours ago
                     loving_ishizaka
1fc67baccf57   ubuntu:20.04   "/bin/bash"               40 hours ago        Exited (127) 40 hours ago
                     mystifying_darwin
4770c66ac65f   ubuntu:20.04   "/bin/bash"               41 hours ago        Exited (0) 41 hours ago
                     trusting_moser
0c16f919003b   ubuntu:20.04   "/bin/bash"               42 hours ago        Exited (0) 41 hours ago
                     great_goldwasser
b4570bb66d77   ubuntu:20.04   "/bin/bash"               42 hours ago        Exited (0) 42 hours ago
                     cranky_bohr
7ed1e46c8f8e   hello-world    "/hello"                  46 hours ago        Exited (0) 46 hours ago
                     ecstatic_nash
f027023a6049   hello-world    "/hello"                  3 days ago          Exited (0) 3 days ago
                     crazy_johnson
// このようなコンテナがある。
```
- `docker container prune`を実行することで、停止済みのコンテナを削除する。
```
> docker container prune
WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N] y
Deleted Containers:
566bae48796f1c6002f3020e297cbd23d7fbb5522c215855a495889953e0cfd1
72cd1a8e7c123954240b3549eac7ec911db5aad087d6aa28520de19df0b5b4bf
1d5588ef5074ed70991615c542262dba3d239fda3ee8486b9d72245ee689d2cd
709ed89bf7db29c64e684a1916ae5e60f871d56f8a0f054bf8bdeb82d9b45891
c0afb3745b60c4f9749d1fb87e37e280b1bb4e3fcd80d3111efc755c77afae2c
fc075adb84147ff4989a903db9cbf314c2a997784fdc1fbe65b6a7a802ecaf60
dc0d397e87f9f68a4c2ad05a7bbc4a870b6f60a0be21d42eafdb63bd9bbdc643
cf85c0483189c6dc7822c6754a031ae8afac9362abab0c0afee214bf6151f42b
8adcacbcf09c97162cb3745a71184ba03dbb2927d733dfa94d176aa45bf053c6
1fc67baccf57225d495ae439ba1887a422d2ad00b517553bb933baffbb134df7
4770c66ac65f1bb4e3a0dfb382523e6f76f160894e9bf9b1e47933d371fac8e0
0c16f919003b04216f68cc58101ed681ffdee67ffa6c5869b0e4f62b436a3c8a
b4570bb66d774bc33f6c59a4dce55b206eb02a18089926c4147e27553d5e751a
7ed1e46c8f8e41307a297e5235e726a38607cc165bcb320e89afbb16958bacca
f027023a6049c07209de84e8b6ea952cd6b632949039d2e62a02c3be9ec0cdb9
7ae119f631e086e5668e399cc062cdcd141192af871e58b5ee83d2353ad37107
19c3f1a76d51c3f3f78146c97f89bc05b98203242de28932449242976b3acd2f
630674ea59e2387a2129714d6f0865a6fcf8e0a07609df6a4576acdad1166b23
5b86e306871d8fcf8cf11e09d8e990e0f3c4eec7ec1123ceef9cc0b3c07e8fd4
993722d3ca0cfa685feea51d5617d93f08a039194f099276edc182a7802357ff
40c415c79783bb20c7f36d7a6c35af77c677e176d465f41884e6569a39756997
d77e48bdd758e52f6749dc494cfd15471323d4d775d59639771430b6526c42a0
d7462f3088bd6a36968f422f3ebbddd0139e4328332c680f615aae5e123178dd
530f752b7e827af913246de77589860fdd9c0b1075f3a1832f6270577d062219
08396dad39ca07d4aea52325857eb38a53df3dc10b6fdd1b989578779f6a0934
57848fa8be80d40560eeee99c1805a7d6c036667c6e171bc94cf0c653eb84b1d
0def345315c5693d6afaf94818536590dbddb049eb7addc24f9175474f7f358e
5c45f2341b272a86dfbd2b7f530916b54838406c590adc3e821bd63bd46397ba
dd3922f6a8d54e5780ca69da0a820a8bf1f842455543ddc6572b80725310d07e
241a014d22461f2204e744a894c2c8bf39356ec070eb7845968930385cdd7cb1
07697b1d2bdcce917c6fbdcfcbec7e88d178510e53701d53a345cd7c4e60d31d
eee2de29b639b4fc2bff8e8f2d36f4eee18cbe1f22adfbc8398b3a5af1460119

Total reclaimed space: 59.9MB

> docker container ls -a
CONTAINER ID   IMAGE          COMMAND       CREATED             STATUS             PORTS     NAMES
1c0ba068ad88   ubuntu:20.04   "/bin/bash"   About an hour ago   Up About an hour             recursing_bartik
// コンテナ一覧を見ると、Upステータス以外のコンテナは削除されていることがわかる。
```
- `docker container run --rm`を実行すると、コンテナが起動して特定のコマンドを実行されると直ちにコンテナが破棄される。
```
> docker container run --rm ubuntu:20.04 ls
bin
boot
dev
etc
home
lib
lib32
lib64
libx32
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var

> docker container ls -a
CONTAINER ID   IMAGE          COMMAND       CREATED       STATUS             PORTS     NAMES
1c0ba068ad88   ubuntu:20.04   "/bin/bash"   2 hours ago   Up About an hour             recursing_bartik
// 先ほど作成したコンテナが消えていることがわかる。
// コンテナを使い捨てる場合に使うと効果的。
```
- `docker container rm -f`でステータスがUpのコンテナでもコンテナを削除できる。
```
> docker container rm -f 1c0ba068ad88

1c0ba068ad88

> docker container ls -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
// ステータスUpのコンテナを削除することが出来た。
// ステータスUpのコンテナをExitedにせずとも削除出来たので、便利。
```
