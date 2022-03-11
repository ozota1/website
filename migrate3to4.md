---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: "KAMONOHASHI 3.xから4.xへの移行"
permalink: /docs/install-and-update/migrate3xto4x
sidebar:
  nav: "docs"
---

## 考慮事項

* KAMONOHASHI 4.x ではアプリのフレームワークのバージョンアップを行いました。
* それに伴い、4.x へのバージョンアップ後は、3.x へのバージョンダウンはできません。

## 事前準備

* KAMONOHASHI のバックアップが取れていることを確認します
* KAMONOHASHI の Admin アカウントでログインできることを確認します

## KAMONOHASHI 4.x 構築

### KAMONOHASHI バージョン 4.0.0 用の構築ツールを入手します

```
cd /var/lib/kamonohashi/deploy-tools/
git fetch --tags
git checkout 4.0.0.0
```

### MinIO の認証情報更新

* ツール入手後に root ユーザで次の MinIO の認証情報更新コマンドを実行します
* 「新しいStorage Secret Keyを入力」と表示されるため、既存のパスワードまたは新たに設定するパスワードを入力してください。
* 新たに設定するパスワードを入力した場合、バージョンアップコマンド実行後 KAMONOHASHI のストレージ管理から該当ストレージの「シークレットキー」を更新してください。

```
cd /var/lib/kamonohashi/deploy-tools
./deploy-kamonohashi.sh credentials storage
```

#### 補足事項

* KAMONOHASHI v4.x から MinIO のバージョンが `RELEASE.2020-03-09T18-26-53Z` から `RELEASE.2021-12-10T23-03-39Z` に更新されます。
* これに伴い、 MinIO の認証情報を設定している環境変数が以下のように変わります。

* 旧環境変数
  * `MINIO_ACCESS_KEY`
  * `MINIO_SECRET_KEY`
* 新環境変数
  * `MINIO_ROOT_USER`
  * `MINIO_ROOT_PASSWORD`

### Webhookの環境設定

Slack通知機能を使用する場合は次を参照し、設定ファイルの編集を行ってください。
* [カスタマイズ設定ガイド Webhookの環境設定](/docs/install-and-update/customize-4x/#webhookの環境設定)

### バージョンアップの実施

* MinIO の認証情報更新後に root ユーザで次のバージョンアップコマンドを実行します

```
cd /var/lib/kamonohashi/deploy-tools
./deploy-kamonohashi.sh update app
```

### KAMONOHASHI のストレージの認証情報更新

バージョンアップ完了のメッセージが出たらブラウザで次の URL にアクセスし、admin ユーザでログインしてください

* KAMONOHASHI: http://KAMONOHASHI 用サーバ/kamonohashi

MinIO の root ユーザの認証情報更新時、既存のパスワードではなく新たに設定するパスワードを入力した場合、ストレージ管理画面から該当ストレージの詳細を開き、「シークレットキー」を更新してください。

