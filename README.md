## Telnet・SSH 接続可能な 草の根 BBS に対応した Sshwifty (Docker)

Web ブラウザから 草の根 BBS へ接続できる環境を構築しましょう。

## Sshwifty とは？

[Sshwifty](https://github.com/nirui/sshwifty)

Web 上で動作する SSH・Telnet クライアントです。

[Request: Connection destination specification or connection list #14 | GitHub nirui/sshwifty issues](https://github.com/nirui/sshwifty/issues/14)

エンコード対応をしていただいた事で、\
シフト JIS を採用している日本の草の根 BBS も接続して使用できます。

対応後は草の根 BBS を運用していたサーバに Sshwifty を設置し、\
Web ブラウザから素早く接続できる環境にしていたのですが、\
訳あってサーバを閉鎖していました。

Sshwifty のサンプルサーバも Heroku を使用していて、\
無料提供終了により、サーバの引っ越しが行われていますが、\
そもそも海外にあって速度が遅い問題があります。

Sshwifty は Docker イメージが公開されているたね、\
Docker を採用する Web サービスに設置して使用する事ができます。\
そこで、Docker イメージに草の根 BBS のリストを入れる事で、\
草の根 BBS 接続環境を構築できるようにしてみました。

## 使用できる Web サービス

Docker を使用できる PaaS で運用する事ができます。\
無料枠があるホスティングサービスは次があります。

- [Google Cloud Run](https://cloud.google.com/run?hl=ja)
- [Fly (fly.io)](https://fly.io/)
- [Railway (railway.app)](https://railway.app/)

VPS などのサーバへインストールするサービスは次があります。

- [CapRover](https://caprover.com/)
- [Dokku](https://dokku.com/)

## ファイル構成

次のファイルで構成されています。\
最低でも `sshwifty.conf.json` の編集が必要です。\
サービスによっては更に編集や設定が必要になります。\
設定した後、デプロイして下さい。正常であれば、Sshwifty を参照できます。

### Dockerfile

全サービス共通で使用します。編集する必要はありません。

### sshwifty.conf.json

Web 参照するドメインを HostName に入れます。\
https://example.net.eu.org/ であれば `example.net.eu.org` です。

パスワード `SharedKe` を入れると、参照時に入力を求められます。\
個人使用やパスワードを知っている人限定にできます。\
省略する場合は表示されないので、URL を知っていれば誰でも使用できます。

```
  "HostName": "サーバ名",
  "SharedKey": "パスワード",
```

Sshwifty Docker のデフォルトリッスンポート番号は 8182 です。\
必要に応じて変更して下さい。

```
      "ListenPort": 8182,
```

このファイルには 草の根 BBS の一覧も含まれていますので、\
必要に応じて追加・編集して下さい。

ユーザー名・パスワードは ssh の接続で有効です。\
telnet のコマンド操作は行えませんので、入力必須です。

### captain-definition

CapRover の構成ファイルです。 `Dockerfile` を参照するようにしています。\
編集する必要はありません。

### fly.toml

Fly の構成ファイルです。 \
作成したアプリ名に合わせて app を編集します。

```
app = "アプリ名"
```

`sshwifty.conf.json` の `ListenPort` を変更した時は、\
次の項目も変更して下さい。

```
[[services]]
  internal_port = 8182
```

## Sshwifty の使用方法

Sshwifty を起動すると、パスワードを設定している場合は入力画面になります。\
起動したら、上部に **＋** をクリックし、**Known remotes** を選択すると、\
`sshwifty.conf.json` に入れた一覧が表示されています。こちらを選択します。

Back Scace キー（Apple Keyboard では delete キー）は機能しません。\
代わりに Ctrl+H を使用して下さい。

## BBS 別情報

[西和ネット](http://jp3tlc.com/com/coms.shtml) は BBS 運用に支障がないように、海外からの接続をブロックしています。\
世界規模で提供されている VPS やインスタンス（VM）などを使用する場合、\
他の BBS には接続できるのに、西和ネット はすぐに切断される場合があります。\
その場合は シスオペ の ＪＰ３ＴＬＣ さんへ\
接続しようとしている元の Web サービスとホスト名を連絡してみて下さい。

