# コンテナレジストリの利用

## はじめてのDocker Hub

今回は個人でも簡単にアカウントが作れる、Docker Hubを使います。<br/>
こちらにコンテナをpushしてみましょう。
https://hub.docker.com

本当は、Red Hatのコンテナレジストリ「Quay.io」にアカウントを作りたいのですが、Red Hatアカウントを作成するのは少し時間がかかるので、今回は手軽にdockerを使います。
ちなみに、Quayは「キー」と呼ぶそうです。

### Docker Hub にサインインする
コンソール作業に戻ります。loginコマンドでDocker Hub にサインインします。
```
podman login docker.io
```

ユーザ名とパスワードを求められるので、別途共有するログイン情報を入力します。

### Docker Hub を使ってみよう

今回は、 102 で作成した python flask WebアプリをDocker Hubにpushします。

まずは、Docker HubにプッシュするコンテナのコンテナIDを確認します。
```sh
$ podman ps
CONTAINER ID  IMAGE                                COMMAND        CREATED        STATUS        PORTS                   NAMES
d63564fb5890  localhost/python-hello-world:latest  python app.py  8 minutes ago  Up 8 minutes  0.0.0.0:5001->5000/tcp  awesome_ardinghelli
```

上記では`d63564fb5890` がコンテナIDです。みなさんの環境ごとに違うIDとなるのでご留意下さい。

#### 1. コンテナーをイメージにタグ付けする
次に、コンテナーを既知のイメージ名にタグ付けします。

```
podman commit <container id> docker.io/<username>/<reponame>
```

`<container id>`: 先ほど書き留めたコンテナID<br/>
`<username>`: Docker Hub のユーザ名<br/>
`<reponame>`: 新しく作成したコンテナの名前


#### 2. コンテナイメージを コンテナレジストリ にプッシュする
```
podman push docker.io/<username>/<reponame>
```

これで、コンテナイメージがコンテナレジストリに登録されました。

#### 3. 登録されたコンテナイメージを確認

ブラウザで以下にアクセスしましょう
https://hub.docker.com/repositories/<username>

今回登録したコンテナイメージがありましたか？
