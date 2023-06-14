# コンテナ開発

コンテナはマニフェストで詳細を記述します。

マニフェストは Dockerfile、または Containerfile と呼びます。
どちらを使っても構いません。ただ、DockerfileはDocker用のマニフェスト、という意思を感じてしまうため、敢えて「Containerfile」としています。
同義だと思っていいです。

Googleなどで検索する際は「Containerfile」または「Dockerfile」のそれぞれで検索してOKです。

作業用フォルダ work を作成し、移動します。
```
mkdir work
cd work
```

## HelloWorld
HelloWorldと出力するアプリ **hw** を作成します。でもただ文字を出してもつまらないので、少しひねった内容にします。

### Containerfileの作成
これからアプリを複数作るので、hwフォルダを用意します。
その中に"Hello World"と書かれた messagefile と、 Containerfile を作成します。
```
mkdir hw
cd hw
echo "Hello World" > messagefile
vi Containerfile
```

vi エディタで Containerfileを開いたら、以下を記載します。
```
FROM fedora
RUN yum -y update && yum -y install figlet
ADD ./messagefile /messagefile
CMD cat /messagefile | figlet
```

Containerfileは、コマンドを1行づつ記載します。
それぞれ意味を説明します。

| コマンド | 説明 |
| ---- | ---- |
| FROM | ベースイメージを指定して取得します。ここでは、[fedora](https://hub.docker.com/_/fedora) というLinuxディストリビューションの1つをベースのイメージとして指定しています。Containerfileは大抵このコマンドから始めます。  |
| RUN |  指定したコマンドを実行します。ここでは、yumコマンドを使ってパッケージ情報の更新と figlet をインストールしています。  |
| ADD | ファイルをコンテナ内へコピーします。今回は、messagefileというファイルを、コンテナにコピーしています。 |
| CMD | コンテナを起動した際に実行されるコマンドを指定します。Containerfileの最後の方に記述するこ多いです。ここではmessagefileの中身("Hello World")を figlet コマンドに入力として与えています。figletコマンドは文字列をアスキーバナーに変換して出力します。 |


### ビルド実行
Containerfileからコンテナイメージをビルド・生成します。 buildコマンドを使います。<br/>
ここで、最後の **.(ドット)** を抜かさないようにして下さい。これは現在のディレクトリを対象にビルドをする、という意図です。<br/>
また、"-t" は "--tag" の省略形で、指定しているのは、hw がリポジトリ名、 1.0 がtag(およそバージョンと同義)になります。
```
podman build -t hw:1.0 .
```

<details>
<summary>実行例 (yumによるインストールがほとんど)</summary>

```
$ podman build .
STEP 1/4: FROM fedora
STEP 2/4: RUN yum -y update && yum -y install figlet
Fedora 38 - x86_64                              3.0 MB/s |  66 MB     00:21
Fedora 38 openh264 (From Cisco) - x86_64        1.4 kB/s | 2.5 kB     00:01
Fedora Modular 38 - x86_64                      1.4 MB/s | 2.3 MB     00:01
Fedora 38 - x86_64 - Updates                    2.9 MB/s | 7.1 MB     00:02
Fedora Modular 38 - x86_64 - Updates            388  B/s | 257  B     00:00
Last metadata expiration check: 0:00:01 ago on Sun Apr 16 07:01:24 2023.
Dependencies resolved.
================================================================================
 Package             Architecture  Version                 Repository      Size
================================================================================
Upgrading:
 libgcrypt           x86_64        1.10.2-1.fc38           updates        514 k
 libgpg-error        x86_64        1.47-1.fc38             updates        230 k
 python3             x86_64        3.11.3-1.fc38           updates         28 k
 python3-libs        x86_64        3.11.3-1.fc38           updates        9.6 M
 rpm-sequoia         x86_64        1.4.0-1.fc38            updates        851 k

Transaction Summary
================================================================================
Upgrade  5 Packages

Total download size: 11 M
Downloading Packages:
(1/5): python3-3.11.3-1.fc38.x86_64.rpm          96 kB/s |  28 kB     00:00
(2/5): libgpg-error-1.47-1.fc38.x86_64.rpm      676 kB/s | 230 kB     00:00
(3/5): libgcrypt-1.10.2-1.fc38.x86_64.rpm       1.3 MB/s | 514 kB     00:00
(4/5): rpm-sequoia-1.4.0-1.fc38.x86_64.rpm      1.9 MB/s | 851 kB     00:00
(5/5): python3-libs-3.11.3-1.fc38.x86_64.rpm    4.7 MB/s | 9.6 MB     00:02
--------------------------------------------------------------------------------
Total                                           3.8 MB/s |  11 MB     00:02
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1
  Upgrading        : python3-libs-3.11.3-1.fc38.x86_64                     1/10
  Upgrading        : python3-3.11.3-1.fc38.x86_64                          2/10
  Upgrading        : libgpg-error-1.47-1.fc38.x86_64                       3/10
  Upgrading        : libgcrypt-1.10.2-1.fc38.x86_64                        4/10
  Upgrading        : rpm-sequoia-1.4.0-1.fc38.x86_64                       5/10
  Cleanup          : python3-3.11.2-1.fc38.x86_64                          6/10
  Cleanup          : libgcrypt-1.10.1-7.fc38.x86_64                        7/10
  Cleanup          : libgpg-error-1.46-2.fc38.x86_64                       8/10
  Cleanup          : python3-libs-3.11.2-1.fc38.x86_64                     9/10
  Cleanup          : rpm-sequoia-1.3.0-1.fc38.x86_64                      10/10
  Running scriptlet: rpm-sequoia-1.3.0-1.fc38.x86_64                      10/10
  Verifying        : libgcrypt-1.10.2-1.fc38.x86_64                        1/10
  Verifying        : libgcrypt-1.10.1-7.fc38.x86_64                        2/10
  Verifying        : libgpg-error-1.47-1.fc38.x86_64                       3/10
  Verifying        : libgpg-error-1.46-2.fc38.x86_64                       4/10
  Verifying        : python3-3.11.3-1.fc38.x86_64                          5/10
  Verifying        : python3-3.11.2-1.fc38.x86_64                          6/10
  Verifying        : python3-libs-3.11.3-1.fc38.x86_64                     7/10
  Verifying        : python3-libs-3.11.2-1.fc38.x86_64                     8/10
  Verifying        : rpm-sequoia-1.4.0-1.fc38.x86_64                       9/10
  Verifying        : rpm-sequoia-1.3.0-1.fc38.x86_64                      10/10

Upgraded:
  libgcrypt-1.10.2-1.fc38.x86_64        libgpg-error-1.47-1.fc38.x86_64
  python3-3.11.3-1.fc38.x86_64          python3-libs-3.11.3-1.fc38.x86_64
  rpm-sequoia-1.4.0-1.fc38.x86_64

Complete!
Last metadata expiration check: 0:00:12 ago on Sun Apr 16 07:01:24 2023.
Dependencies resolved.
================================================================================
 Package    Arch       Version                                 Repository  Size
================================================================================
Installing:
 figlet     x86_64     2.2.5-25.20151018gita565ae1.fc38        fedora     137 k

Transaction Summary
================================================================================
Install  1 Package

Total download size: 137 k
Installed size: 666 k
Downloading Packages:
figlet-2.2.5-25.20151018gita565ae1.fc38.x86_64. 1.2 MB/s | 137 kB     00:00
--------------------------------------------------------------------------------
Total                                           193 kB/s | 137 kB     00:00
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1
  Installing       : figlet-2.2.5-25.20151018gita565ae1.fc38.x86_64         1/1
  Running scriptlet: figlet-2.2.5-25.20151018gita565ae1.fc38.x86_64         1/1
  Verifying        : figlet-2.2.5-25.20151018gita565ae1.fc38.x86_64         1/1

Installed:
  figlet-2.2.5-25.20151018gita565ae1.fc38.x86_64

Complete!
--> Pushing cache []:b5be31b75a7b031ee291e82907b39c9103ca848683d349aaef5c9e28ad94388b
--> 70ea8b96d07
STEP 3/4: ADD ./messagefile /messagefile
--> Pushing cache []:10ab90b4d35516f71a092ba0348367d3f0a1e5194fc36b5b321aeab8ba0202b0
--> 08d3196efe3
STEP 4/4: CMD cat /messagefile | figlet
COMMIT
--> Pushing cache []:08354a00bfa748c1dad8d8833f91c75f3a6ccefb370df730735711ecb461e1dd
--> 016f667375a
016f667375a74fb752d4f929f08b6b5a90ca319b178d4fca693fba152fde84e0
```
</details>

作成したコンテナイメージを確認します。
```
podman images
```

<details open>
<summary>実行例</summary>
  
```
$ podman images
REPOSITORY                         TAG         IMAGE ID      CREATED     SIZE
registry.fedoraproject.org/fedora  latest      c9bfca6d0ac2  3 days ago  196 MB
localhost/hw                       1.0         016f667375a7  6 days ago  437 MB
```
</details>

localhostにhwというコンテナが、TAG1.0で作成されているのを確認できました。

では、早速実行してみましょう。

```
podman run hw:1.0
```

<details>
<summary>実行例</summary>
  
```
$ podman run hw:1.0
 _   _      _ _        __        __         _       _
| | | | ___| | | ___   \ \      / /__  _ __| | ____| |
| |_| |/ _ \ | |/ _ \   \ \ /\ / / _ \| '__| |/ / _` |
|  _  |  __/ | | (_) |   \ V  V / (_) | |  |   < (_| |
|_| |_|\___|_|_|\___/     \_/\_/ \___/|_|  |_|\_\__,_|


```
</details>

Hello Worldのアスキーバナーが表示されましたね。<br/>
コンテナの開発に成功しました！

ここで、各自5分～10分程度使って、以下のような内容を試してみましょう。
* messagefileの中身を好きな言葉にして再度ビルド・実行し、その結果を楽しみましょう
* TAGを1.0から1.1や1.2にして、`podman images`の結果を確認してみましょう。実行してみましょう。
* `podman ps -a`を実行すると、tag1.0などの古いコンテナが貯まっていますね。コンテナを削除してみましょう。イメージも削除してみましょう。

## 静的Webアプリ

次に、Webサーバ nginx を使って 静的Webアプリ **linux_tweet_app** を配信します。

### ソースコード一式のclone
ソースコードを一から作るのは時間が足りないので、sampleをダウンロードします。
```
git clone https://github.com/dockersamples/linux_tweet_app.git
```

<details>
<summary>実行例</summary>

```
$ git clone https://github.com/dockersamples/linux_tweet_app.git
Cloning into 'linux_tweet_app'...
remote: Enumerating objects: 14, done.
remote: Counting objects: 100% (8/8), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 14 (delta 4), reused 4 (delta 4), pack-reused 6
Receiving objects: 100% (14/14), 10.79 KiB | 10.79 MiB/s, done.
Resolving deltas: 100% (5/5), done.
```
</details>

### ビルド実行
git clone したソースコードには、静的Webアプリ(html)とDockerfileが含まれています。<br/>
Dockerfileの中身を読んでみます。

| コマンド | 説明 |
| ---- | ---- |
| FROM | ベースイメージを指定して取得します。ここでは、[nginx:latest](https://hub.docker.com/_/nginx) というlinuxにWebサーバアプリケーションnginxが配置されたコンテナイメージをベースのイメージとして指定しています。 |
| COPY | ファイルをコンテナ内へコピーします。ここでは、index.htmlというファイルを、コンテナの /usr/share/nginx/html にコピーしています。 |
| COPY | ファイルをコンテナ内へコピーします。ここでは、linux.pngというファイルを、コンテナの /usr/share/nginx/html にコピーしています。 |
| EXPOSE | 指定したポートをLISTEN状態とします。ここでは80(HTTP)と443(HTTPS)をLISTEN状態にしています。 |
| CMD | コンテナを起動した際に実行されるコマンドを指定します。ここでは nginx を起動しています。 |

COPY と ADD は同じようなコマンドですが、以下の違いがあります。
* COPY - 明示的なコピー元とコピー先のファイルまたはディレクトリを指定して、ローカルファイルを再帰的にコピーします。 COPYでは、場所を宣言する必要があります。
* ADD - ローカルファイルを再帰的にコピーし、存在しない場合は暗黙的にコピー先ディレクトリを作成し、アーカイブをコピー元としてローカルURLまたはリモートURLとして受け入れます。アーカイブはそれぞれコピー先ディレクトリに展開またはダウンロードされます。


では、 **linux_tweet_app** という名前のコンテナとしてビルドします。
```
cd linux_tweet_app
podman build -t linux_tweet_app .
```

<details>
<summary>実行例</summary>
  
```
STEP 1/5: FROM nginx:latest
Resolving "nginx" using unqualified-search registries (/etc/containers/registries.conf.d/999-podman-machine.conf)
Trying to pull docker.io/library/nginx:latest...
Getting image source signatures
Copying blob sha256:4b98867cde79486d55399cb95957331018b849a2bf04ee9a6dcae9b8bc482d76
Copying blob sha256:5b5fe70539cd6989aa19f25826309f9715a9489cf1c057982d6a84c1ad8975c7
Copying blob sha256:441a1b46536703aec84bf2cabdd2524d4cc7dad9c6772491e01ff8ffe15b5507
Copying blob sha256:3b9543f2b5005b74aae0ffaa779803f2b162ec3bd461c9e10a21671512718c87
Copying blob sha256:ca89ed5461a9f2c681ebd39ed6583a7405d82f51b42177b7388c99eb6869e780
Copying blob sha256:b0e1283145af839c63923f671b4ab6c5d9e22826ab4372c6660db5a5556446c8
Copying blob sha256:4a85ce26214d83c77b5464631a67c71e1c2793b655261befe52ba0e20ffc3bd1
Copying config sha256:eb4a57159180767450cb8426e6367f11b999653d8f185b5e3b78a9ca30c2c31d
Writing manifest to image destination
Storing signatures
STEP 2/5: COPY index.html /usr/share/nginx/html
--> fa46eba0ae60
STEP 3/5: COPY linux.png /usr/share/nginx/html
--> ee76d79be354
STEP 4/5: EXPOSE 80 443
--> c369df24005a
STEP 5/5: CMD ["nginx", "-g", "daemon off;"]
COMMIT linux_tweet_app
--> f78b0d7e4b55
Successfully tagged localhost/linux_tweet_app:latest
f78b0d7e4b55790eb89d8a47fe3361ad96493991472f5f2a53d3ffe55565f2e7
```
</details>

作成されたコンテナイメージを確認します。
```
podman images
```

<details>
<summary>実行例</summary>

```
REPOSITORY                                   TAG         IMAGE ID      CREATED            SIZE
localhost/linux_tweet_app                    latest      f78b0d7e4b55  8 seconds ago      191 MB
```
</details>

コンテナイメージを実行します。 **-p** でhttp(80)をホストネットワークの8081ポートにバインドします。**-d**でバックグラウンド実行させます。
```
podman run -d -p 8081:80 linux_tweet_app
```
ブラウザで http://localhost:8081 を開き、以下のようなサイトが表示されれば成功です。

![image](./images/102-1.png)

## Spring Boot (Java) アプリケーションのコンテナ化
o

最後に、Webサーバ nginx を使って Spring Boot Java Webアプリ **petclinic-app** を配信します。

### ソースコード一式のclone
ソースコードを一から作るのは無理なので、ソースコードをダウンロードします。
```
git clone https://github.com/dockersamples/spring-petclinic-docker.git
```

### ビルド実行
git clone したソースコードには、JavaアプリケーションとDockerfileが含まれています。<br/>
Dockerfileの中身を読んでみます。

| コマンド | 説明 |
| ---- | ---- |
| FROM | ベースイメージを指定して取得します。ここでは、eclipse-temurin:17-jdk-jammy というspring bootアプリケーションのベースイメージを指定しています。 |
| WORKDIR | 作業ディレクトリを変更します。 linux/windowsのcdと同義です |
| COPY | ファイルをコンテナ内へコピーします。 |
| RUN | コマンドを実行します。 |
| CMD | コンテナを起動した際に実行されるコマンドを指定します。ここでspring bootを起動しています |


では、 **petclinic-app** という名前のコンテナとしてビルドします。
```
cd spring-petclinic-docker
podman build -t linux_tweet_app .
```

### 必要資材の作成

Spring Bootアプリケーションを実行するために必要なソースコード一式を準備します。<br/>
以下の内容をもとにファイルの作成とコードのコピーを行なって下さい。

**pom.xml**

```XML
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>spring-docker-demo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.1</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <java.version>11</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
    </dependencies>

    <build>
        <finalName>app</finalName>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

**src/hello/HelloController.java**
```Java
package com.example.web.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
public class HelloController {

    @GetMapping("/")
    public String root() {
        return "hello/index";
    }
}
```

**index.html**
```html
Hello World
```

**Containerfile**
```
FROM maven:3.6.3-jdk-11 AS builder
WORKDIR /tmp
COPY ./src ./src
COPY ./pom.xml .
RUN mvn package
FROM adoptopenjdk/openjdk11:debianslim-jre
COPY --from=builder /tmp/target/app.jar /app/app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "/app/app.jar"]
```

## 参考
Containerfile/Dockerfileには多くのコマンドがあり、すべては紹介しきれません。より多くのコマンドを知りたい方は、以下をご確認ください。
* Dockerfileリファレンス(日本語翻訳版): https://docs.docker.jp/engine/reference/builder.html
* Containerfileのコマンド説明(チートシート): https://qiita.com/mintak21/items/a6766e3efd6730c9519d
* Docker Sample(Docker doc): https://docs.docker.com/samples/
* Docker Sample(GitHub): https://github.com/dockersamples
