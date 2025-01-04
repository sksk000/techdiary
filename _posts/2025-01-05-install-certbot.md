---
title: "cerbotインストールまでに躓いたこと"
last_modified_at: 2025-01-05T00:00:00+0900
tags:
  - Linux
---

## 開発環境

- Amazon Linux2
- python 2.7.18

## 問題

certbot 実行すると以下のエラーが発生

```
ImportError: urllib3 v2.0 only supports OpenSSL 1.1.1+, currently the 'ssl' module is compiled with 'OpenSSL 1.0.2k-fips 26 Jan 2017'. See: https://github.com/urllib3/urllib3/issues/2168
```

## やったこと

- Python3.8 をインストール

```
$ sudo amazon-linux-extras install -y python3.8
```

- certbot の環境づくり

```
$ sudo python3 -m venv /opt/certbot/
$ sudo /opt/certbot/bin/pip  install --upgrade pip
```

- certbot インストール

```
$ sudo /opt/certbot/bin/pip install certbot certbot-nginx
```

- certbot 実行したが、上記エラーが発生した。

```
$ sudo certbot --nginx

...

ImportError: urllib3 v2.0 only supports OpenSSL 1.1.1+, currently the 'ssl' module is compiled with 'OpenSSL 1.0.2k-fips 26 Jan 2017'. See: https://github.com/urllib3/urllib3/issues/2168
```

## 解決策

- pip3 にて urllib3 v1.26.6 をインストールします。

```
$ pip3 install urllib3==1.26.6
```

## 参考 URL

- https://dx-tech.blog/issue/python-pip-command-notopensslwarning/
