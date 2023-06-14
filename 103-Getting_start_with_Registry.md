# コンテナレジストリの利用

## はじめてのDocker Hub

今回は個人でも簡単にアカウントが作れる、Docker Hubを使います。<br/>
こちらにコンテナをpushしてみましょう。
https://hub.docker.com

本当は、Red Hatのコンテナレジストリ「Quay.io」にアカウントを作り対のですが、Red Hatアカウントを作ったり大変なので省略します。
Quayは「キー」と呼ぶそうです。

### Docker Hub にサインインする
コンソール作業に戻ります。loginコマンドでサインインします。
```
podman login docker.io
```

ユーザ名とパスワードを求められるので、別途共有するログイン情報を入力します。

### Docker Hub を使ってみよう

#### 1. 新しいコンテナを作成する
コンテナイメージ ubuntu を実行し、新しいファイルを含むコンテナを新たに作成します。

```
podman run ubuntu echo "<すきな言葉をいれて実行してください>" > newfile
```
コンテナはすぐに終了します。psコマンドでコンテナIDを表示し、どこかに書き留めましょう。(次で必要になります)

```
podman ps -a
```

#### 2. コンテナーをイメージにタグ付けする
次に、コンテナーを既知のイメージ名にタグ付けする必要があります

```
podman commit <container id> quay.io/<username>/<reponame>
```

`<container id>`: 先ほど書き留めたコンテナID<br/>
`<username>`: Docker Hub のユーザ名<br/>
`<reponame>`: 新しく作成したコンテナの名前


#### 3. コンテナイメージを コンテナレジストリ にプッシュする
```
podman push docker.io/<username>/<reponame>
```

これで、コンテナイメージがコンテナレジストリに登録されました。

#### 4. 登録されたコンテナイメージを確認

ブラウザで以下にアクセスしましょう
https://hub.docker.com/repositories/<username>

今回登録したコンテナイメージがありましたか？

#### 5. コンテナレジストリ からイメージをダウンロードする
他の参加者がpushしたイメージをrunしてみましょう！reponameを他の参加者のレポジトリ名に変更して、以下を実行します。

```
podman run docker.io/<username>/<reponame>
```

自分が入れた好きな言葉とは別の言葉が出ましたか?