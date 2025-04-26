---
title: "DockerでSpringbootを使用し、リクエストを送ってみる"
last_modified_at: 2025-03-06T00:00:00+0900
tags:
  - JavaScript
  - Docker
---

## 学んだこと

### 開発環境

- ホストマシン(ポート番号:8080)
  - カスタムブリッジ
    - API 用コンテナ(ポート番号:8080)
      - Springboot
    - DB 用コンテナ(ポート番号:5432)
      - PostgreSQL
  - ボリューム
    - DB のストレージ

### 要件

- `http://localhost:8080/api/hello?lang=ja`でリクエストする場合は、こんにちはと返す。
- `http://localhost:8080/api/hello?lang=en`でリクエストする場合は、Hello とかえす。

### Springboot のプロジェクト取得

- SpringbootInitializr で Springboot のプロジェクトを以下の設定で作成。
  ![settingspringboot](/assets/images/settingspringboot.png)

### Springboot のプロジェクト編集

- 取得したプロジェクトの`api/src/main/java/com/example/api`内に`HelloController.java`を作成する。
- 作成後、以下の処理を`HelloController.java`に追加する。

```
package com.example.api;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
// /apiから始まるURLに対応する。
@RequestMapping("/api")

public class HelloController {

    // /api/helloのリクエストを処理する。
    @RequestMapping("/hello")
    public String hello(
            @RequestParam String lang

    ) {
        // リクエストを受け取ったらHELLOを返している。
        return "HELLO";
    }
}
// この状態だと、言語関係なしにHELLOが返される。
```

- `/api`に`Dockerfile`を作成する。

```
FROM gradle:7-jdk17
// 今回はJavaを使用するため、JDKをインストールする。

WORKDIR /api
COPY . .

CMD [ "./gradlew", "bootRun"]

```

- ビルドを行う。

```
D:\webapi\api> docker image build -t api-img .
// 今回はイメージに名前を付けたいので-tで名前を付ける。

[+] Building 33.0s (9/9) FINISHED                            docker:desktop-linux
 => [internal] load build definition from Dockerfile                         0.1s
 => => transferring dockerfile: 117B                                         0.0s
 => [internal] load metadata for docker.io/library/gradle:7-jdk17            2.7s
 => [auth] library/gradle:pull token for registry-1.docker.io                0.0s
 => [internal] load .dockerignore                                            0.1s
 => => transferring context: 2B                                              0.0s
 => [1/3] FROM docker.io/library/gradle:7-jdk17@sha256:c30c32900d6c4e19924  28.7s
 => => resolve docker.io/library/gradle:7-jdk17@sha256:c30c32900d6c4e199240  0.1s
 => => sha256:22ca3d571447331ace3a8d2e76f1dc13e20a34b5b40 54.90kB / 54.90kB  0.2s
 => => sha256:d86d8b1d106c46bc162cfa5cabfdf1a5593bdcde 122.73MB / 122.73MB  19.4s
 => => sha256:9b8c214eb978ea6cce091ccdcf81ef3bfd9855f440 65.90MB / 65.90MB  11.6s
 => => sha256:9845315e722b565ce0ca283331b00674928fec392a4dc 1.29kB / 1.29kB  0.6s
 => => sha256:3d2700d3f64bcbf1b67ccf65a715330aa34cb16c0c1e8 2.28kB / 2.28kB  0.5s
 => => sha256:0b4fff2c206cae13745f200704b38134105ac3a4bcf862dc0 159B / 159B  0.2s
 => => sha256:da5768705e3b7e7e3c61f95a6ed0478a61076d08 144.58MB / 144.58MB  22.3s
 => => sha256:a2ff804eb32a079e6f9a54eb31c0f43d9d6c56fc09c 22.94MB / 22.94MB  3.8s
 => => extracting sha256:a2ff804eb32a079e6f9a54eb31c0f43d9d6c56fc09c7735ec5  0.9s
 => => extracting sha256:da5768705e3b7e7e3c61f95a6ed0478a61076d084e1adedfe2  1.5s
 => => extracting sha256:0b4fff2c206cae13745f200704b38134105ac3a4bcf862dc0d  0.0s
 => => extracting sha256:3d2700d3f64bcbf1b67ccf65a715330aa34cb16c0c1e8c9146  0.0s
 => => extracting sha256:9845315e722b565ce0ca283331b00674928fec392a4dce922e  0.0s
 => => extracting sha256:4f4fb700ef54461cfa02571ae0db9a0dc1e0cdb5577484a6d7  0.0s
 => => extracting sha256:9b8c214eb978ea6cce091ccdcf81ef3bfd9855f44059b51b9f  2.6s
 => => extracting sha256:d86d8b1d106c46bc162cfa5cabfdf1a5593bdcdef8864a89ea  0.7s
 => => extracting sha256:22ca3d571447331ace3a8d2e76f1dc13e20a34b5b40aa8ba01  0.0s
 => [internal] load build context                                            0.2s
 => => transferring context: 60.43kB                                         0.0s
 => [2/3] WORKDIR /api                                                       0.6s
 => [3/3] COPY . .                                                           0.1s
 => exporting to image                                                       0.5s
 => => exporting layers                                                      0.3s
 => => exporting manifest sha256:62dfd8732bd0922944874d72c12882611ef653ecb1  0.0s
 => => exporting config sha256:dd9a123868d0e0d616814dd36eaca7611cf2641f9745  0.0s
 => => exporting attestation manifest sha256:920ddd747c5ea54b519b592eb04878  0.0s
 => => exporting manifest list sha256:7837e08ea66b0fff049aa5116ee14ed79cce6  0.0s
 => => naming to docker.io/library/api-img:latest                            0.0s
 => => unpacking to docker.io/library/api-img:latest                         0.1s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/oiwghi8ysg1g4iqut9ngap532
// 無事成功。
```

- コンテナ実行し、実際にリクエストを送ってみる。

```
PS D:\webapi\api> docker container run -p 8080:8080 --rm api-img
// 今回は、ポート番号8080を設定し、起動後削除する設定を行い起動する。
// ログが長いため、省略。問題なく起動することができた。
```

![requestresult](/assets/images/result-request.png)
