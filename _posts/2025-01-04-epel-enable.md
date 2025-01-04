---
title: "EPELインストールで躓いたこと"
last_modified_at: 2025-01-04T00:00:00+0900
tags:
  - Linux
---

## 開発環境
- Amazon Linux2

## 問題
cerbotを使いhttps化を行うため、EPELのインストールが必要になった。
公式ドキュメントを確認し、以下のコマンドでEPELのインストールを行った。
https://snapcraft.io/docs/installing-snap-on-centos

```
$ sudo yum install epel-release
```
実行すると以下のエラーが発生した。
```
sudo yum install epel-release
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
No package epel-release available.
Error: Nothing to do


epel-release is available in Amazon Linux Extra topic "epel"

To use, run
# sudo amazon-linux-extras install epel

Learn more at
https://aws.amazon.com/amazon-linux-2/faqs/#Amazon_Linux_Extras

```

## やったこと
エラー文に書かれている通り、以下のコマンドを実行すると成功した。
```
$ sudo amazon-linux-extras install epel
```
