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
  * DBのバックアップを用いた、過去のバージョンの再デプロイのみ可能です

## 事前準備

* KAMONOHASHI のバックアップが取れていることを確認します
* KAMONOHASHI の Admin アカウントでログインできることを確認します

## KAMONOHASHI 3.x のアンインストール
```
cd /var/lib/kamonohashi/deploy-tools/
./deploy-kamonohashi.sh clean all
```

- アンインストールは ssh を用いて実行されます。
  - 設定されている ssh ユーザは`deepops/config/inventory`の`ansible_user`の項目で確認できます。
  - ssh ユーザでの SSH にパスワードが必要な場合は、clean all に`-k`オプションを追加します。sudo にパスワードが必要な場合は`-K`のオプションを指定します。

### KAMONOHASHI バージョン 4.0.0 用の構築ツールを入手します

```
cd /var/lib/kamonohashi/deploy-tools/
git fetch --tags
git checkout 4.0.0.0
git submodule update --init --recursive
```


- 構築ツールが正常に取得できているか次のコマンドで確認します

```
echo KAMONOHASHI deploy tool: $(git tag --points-at HEAD)
echo deepops: $(cd deepops; git tag --points-at HEAD)
```

- KAMONOHASHI deploy tool: 4.0.0.0 , deepops: 22.01 　が表示されれば正常です。
- 正常でない場合、`git submodule update --init --recursive`でエラーが出ていないかを確認してください

### 構築ツールの実行ユーザーの移行
* 過去のdeepopsではrootユーザーで実行しないと構築途中にエラーが発生していましたが、新しいdeepopsではその部分の改善に加え、rootユーザーでの実行を禁止するようになりました。
* そのため、root以外のユーザーを用意してそのユーザーでKAMONOHASHIの構築を実行する必要があります。そのためには過去にrootユーザーで実行していたスクリプトの所有者等の変更が必要になります。
  * sudoのできるユーザーを用意してください
  * KAMONOHASHIの全てのサーバーにSSHできるユーザーにしてください 
* 下記のコマンドをk8s-masterサーバーのrootユーザーで実行してください。deploy-userの部分は用意したデプロイ用ユーザーに変更してください
```
chown -R deploy-user:deploy-user /var/lib/kamonohashi/
chown -R deploy-user:deploy-user /var/log/kamonohashi/
```
* /var/lib/kamonohashi/deepops/config/inventoryを編集し、ansible_userの値をdeploy-userに変更してください

###  構築に必要なパッケージを入手
- k8s-masterの構築用ユーザーで次のコマンドを実行し、構築に必要なパッケージを入手します

```
./deploy-kamonohashi.sh prepare
```

### 移行設定の生成

- 次のコマンドを実行し、設定ファイルの生成を行います

```
./deploy-kamonohashi.sh configure verup
```

### 構築の実施

- 構築用ユーザで次の構築コマンドを実行します

```
cd /var/lib/kamonohashi/deploy-tools
./deploy-kamonohashi.sh deploy all
```

- 構築は ssh を用いて実行されます。冒頭のアンインストールと同様に`-K`,`-k`オプションを必要に応じ追加します
- シングルノード構成の場合は次に注意してください
  - ドライバインストールの際に k8s master サーバが再起動されるため、再起動後に再度`./deploy-kamonohashi.sh deploy all`を実行してください。
  - 既知の不具合でhelmによるKAMONOHASHIのコンテナデプロイが一度エラーになりますが、再度`./deploy-kamonohashi.sh deploy all`を実行すればエラー部分を通過します

構築完了のメッセージが出たらブラウザで次の URL にアクセスし、admin ユーザでログインして利用を開始してください

- KAMONOHASHI: http://KAMONOHASHI 用サーバ/kamonohashi
- アクアリウム機能: http://KAMONOHASHI 用サーバ/aquarium


# 4.xで追加された設定項目

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

| 旧環境変数         | 新環境変数            | 概要                              | 
| :----------------- | :-------------------- | :-------------------------------- |
| `MINIO_ACCESS_KEY` | `MINIO_ROOT_USER`     | MinIO の 管理者ユーザ名           |
| `MINIO_SECRET_KEY` | `MINIO_ROOT_PASSWORD` | MinIO の 管理者ユーザのパスワード |

### Webhookの環境設定

Slack 通知機能を使用する場合は次を参照し、設定ファイルの編集を行ってください。
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

