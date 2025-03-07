---
title: "DockerComposeでAPIサーバを起動する"
last_modified_at: 2024-09-19T00:00:00+0900
tags:
  - Docker
---

## 学んだこと

### DockerCompose を作成

- 以下の通り作成を行う。

```
version: "3.9"
services:
  api:
    build: ./
    ports:
      - 8080:8080
```

### 起動する

`docker compose up`でビルドとコンテナ起動同時に行う

```
PS D:\webapi> docker compose up
time="2025-03-07T12:25:12+09:00" level=warning msg="D:\\webapi\\docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion"
[+] Building 1.1s (2/3)                                                                                                                                                              docker:desktop-linux
[+] Running 0/1 Building                                                                                                                                                                             0.1s
[+] Building 2.2s (9/9)                                                                                                                                                              docker:desktop-linux
 => [api auth] library/gradle:pull token for registry-1.docker.io                                                                                                                                    0.0s
[+] Building 2.2s (10/10) FINISHED                                                                                                                                                   docker:desktop-linux
 => [api internal] load build definition from Dockerfile                                                                                                                                             0.0s
 => => transferring dockerfile: 117B                                                                                                                                                                 0.0s
 => [api internal] load metadata for docker.io/library/gradle:7-jdk17                                                                                                                                1.7s
 => [api auth] library/gradle:pull token for registry-1.docker.io                                                                                                                                    0.0s
 => [api internal] load .dockerignore                                                                                                                                                                0.0s
 => => transferring context: 2B                                                                                                                                                                      0.0s
 => [api 1/3] FROM docker.io/library/gradle:7-jdk17@sha256:c30c32900d6c4e199240d43ad982ecb70000832b619c6054e7f7b813613d9ba0                                                                          0.1s
 => => resolve docker.io/library/gradle:7-jdk17@sha256:c30c32900d6c4e199240d43ad982ecb70000832b619c6054e7f7b813613d9ba0                                                                              0.1s
 => [api internal] load build context                                                                                                                                                                0.0s
 => => transferring context: 1.23kB                                                                                                                                                                  0.0s
 => CACHED [api 2/3] WORKDIR /api                                                                                                                                                                    0.0s
 => CACHED [api 3/3] COPY . .                                                                                                                                                                        0.0s
 => [api] exporting to image                                                                                                                                                                         0.2s
 => => exporting layers                                                                                                                                                                              0.0s
 => => exporting manifest sha256:e5380a91b1bf0f7fe7a83837f8a5a335a9d5d74faecb24e3e82b861f2da0d08a                                                                                                    0.0s
 => => exporting config sha256:8c95c5d8aeeea29ba5a4ed0d34c517bcf289c0f8bdd4e2384585d146871ab4d2                                                                                                      0.0s
 => => exporting attestation manifest sha256:fabf8a1938964f1264628b8ade09637cd872888e55469eb20fdd9bf19ed9d01d                                                                                        0.0s
 => => exporting manifest list sha256:8203aadd99bc5263c1231f9c884f4f070644a7903b5bd12a0a719a3e5763d9dc                                                                                               0.0s
 => => naming to docker.io/library/webapi-api:latest                                                                                                                                                 0.0s
[+] Running 3/3g to docker.io/library/webapi-api:latest                                                                                                                                              0.0s
 ✔ Service api             Built                                                                                                                                                                     2.4s
 ✔ Network webapi_default  Created                                                                                                                                                                   0.1s
 ✔ Container webapi-api-1  Created                                                                                                                                                                   0.1s
Attaching to api-1
// ここでwebapi_defaultというブリッジネットワークが作成された。
// dockercomposeは何も設定せずとも、カスタムブリッジネットワークを作成してくれる。

api-1  | Downloading https://services.gradle.org/distributions/gradle-8.12.1-bin.zip
api-1  | .............10%.............20%.............30%.............40%.............50%.............60%.............70%.............80%.............90%.............100%
api-1  |
api-1  | Welcome to Gradle 8.12.1!
api-1  |
api-1  | Here are the highlights of this release:
api-1  |  - Enhanced error and warning reporting with the Problems API
api-1  |  - File-system watching support on Alpine Linux
api-1  |  - Build and test Swift 6 libraries and apps
api-1  |
api-1  | For more details see https://docs.gradle.org/8.12.1/release-notes.html
api-1  |
api-1  | Starting a Gradle Daemon (subsequent builds will be faster)
api-1  | > Task :compileJava
api-1  | > Task :processResources
api-1  | > Task :classes
api-1  | > Task :resolveMainClassName
api-1  |
api-1  | > Task :bootRun
api-1  |
api-1  |   .   ____          _            __ _ _
api-1  |  /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
api-1  | ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
api-1  |  \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
api-1  |   '  |____| .__|_| |_|_| |_\__, | / / / /
api-1  |  =========|_|==============|___/=/_/_/_/
api-1  |
api-1  |  :: Spring Boot ::                (v3.4.3)
api-1  |
api-1  | 2025-03-07T03:26:04.682Z  INFO 228 --- [api] [           main] com.example.api.ApiApplication           : Starting ApiApplication using Java 17.0.14 with PID 228 (/api/build/classes/java/main started by root in /api)
api-1  | 2025-03-07T03:26:04.685Z  INFO 228 --- [api] [           main] com.example.api.ApiApplication           : No active profile set, falling back to 1 default profile: "default"
api-1  | 2025-03-07T03:26:05.097Z  INFO 228 --- [api] [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data JPA repositories in DEFAULT mode.
api-1  | 2025-03-07T03:26:05.109Z  INFO 228 --- [api] [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 7 ms. Found 0 JPA repository interfaces.
api-1  | 2025-03-07T03:26:05.402Z  INFO 228 --- [api] [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port 8080 (http)
api-1  | 2025-03-07T03:26:05.410Z  INFO 228 --- [api] [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
api-1  | 2025-03-07T03:26:05.411Z  INFO 228 --- [api] [           main] o.apache.catalina.core.StandardEngine    : Starting Servlet engine: [Apache Tomcat/10.1.36]
api-1  | 2025-03-07T03:26:05.441Z  INFO 228 --- [api] [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
api-1  | 2025-03-07T03:26:05.442Z  INFO 228 --- [api] [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 720 ms
api-1  | 2025-03-07T03:26:05.522Z  INFO 228 --- [api] [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Starting...
api-1  | 2025-03-07T03:26:05.631Z  INFO 228 --- [api] [           main] com.zaxxer.hikari.pool.HikariPool        : HikariPool-1 - Added connection conn0: url=jdbc:h2:mem:c52bfc93-f576-4bf8-93f1-5b826c209c8d user=SA
api-1  | 2025-03-07T03:26:05.632Z  INFO 228 --- [api] [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Start completed.
api-1  | 2025-03-07T03:26:05.664Z  INFO 228 --- [api] [           main] o.hibernate.jpa.internal.util.LogHelper  : HHH000204: Processing PersistenceUnitInfo [name: default]
api-1  | 2025-03-07T03:26:05.699Z  INFO 228 --- [api] [           main] org.hibernate.Version                    : HHH000412: Hibernate ORM core version 6.6.8.Final
api-1  | 2025-03-07T03:26:05.720Z  INFO 228 --- [api] [           main] o.h.c.internal.RegionFactoryInitiator    : HHH000026: Second-level cache disabled
api-1  | 2025-03-07T03:26:05.996Z  INFO 228 --- [api] [           main] o.s.o.j.p.SpringPersistenceUnitInfo      : No LoadTimeWeaver setup: ignoring JPA class transformer
api-1  | 2025-03-07T03:26:06.046Z  INFO 228 --- [api] [           main] org.hibernate.orm.connections.pooling    : HHH10001005: Database info:
api-1  |        Database JDBC URL [Connecting through datasource 'HikariDataSource (HikariPool-1)']
api-1  |        Database driver: undefined/unknown
api-1  |        Database version: 2.3.232
api-1  |        Autocommit mode: undefined/unknown
api-1  |        Isolation level: undefined/unknown
api-1  |        Minimum pool size: undefined/unknown
api-1  |        Maximum pool size: undefined/unknown
api-1  | 2025-03-07T03:26:06.286Z  INFO 228 --- [api] [           main] o.h.e.t.j.p.i.JtaPlatformInitiator       : HHH000489: No JTA platform available (set 'hibernate.transaction.jta.platform' to enable JTA platform integration)
api-1  | 2025-03-07T03:26:06.289Z  INFO 228 --- [api] [           main] j.LocalContainerEntityManagerFactoryBean : Initialized JPA EntityManagerFactory for persistence unit 'default'
api-1  | 2025-03-07T03:26:06.314Z  WARN 228 --- [api] [           main] JpaBaseConfiguration$JpaWebConfiguration : spring.jpa.open-in-view is enabled by default. Therefore, database queries may be performed during view rendering. Explicitly configure spring.jpa.open-in-view to disable this warning
api-1  | 2025-03-07T03:26:06.535Z  INFO 228 --- [api] [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port 8080 (http) with context path '/'
api-1  | 2025-03-07T03:26:06.542Z  INFO 228 --- [api] [           main] com.example.api.ApiApplication           : Started ApiApplication in 2.148 seconds (process running for 2.38)


v View in Docker Desktop   o View Config   w Enable Watch
// 起動することができた。
```

- `docker compose up`実行時に、新しくカスタムネットワークが作成される。

```
> docker network ls
NETWORK ID     NAME               DRIVER    SCOPE
7eb2bda6e591   bridge             bridge    local
53713c5bc16a   host               host      local
5e56067c99c1   none               null      local
e6147610759a   travelog_default   bridge    local
d8422bb81a03   webapi_default     bridge    local
// 確認すると、新しくカスタムネットワークが作成されている。
```

- 同じく`docker compose up`したタイミングで、コンテナも作成される。

```
> docker container ls
CONTAINER ID   IMAGE        COMMAND                   CREATED         STATUS         PORTS                    NAMES
0ef1559d29f9   webapi-api   "/__cacert_entrypoin…"   6 minutes ago   Up 6 minutes   0.0.0.0:8080->8080/tcp   webapi-api-1
```
- 起動できたため、`http://localhost:8080/api/hello?lang=ja`にアクセスに`HELLO`が表示されるか確認する。

![result](/assets/images/dockercompose-webapi-result.png)
