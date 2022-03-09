---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: Infra Guide
permalink: /docs/how-to/infra/credentials
sidebar:
  nav: "docs"
---

## 認証情報の更新

* v4.x以降のデプロイツールに認証情報更新用のコマンドを用意しています。
* このコマンドで、更新可能な認証情報は以下になります。
  * DB のパスワード
  * MinIO ストレージのパスワード
    * ※自動構築されたオブジェクトストレージを利用している場合のみ更新されます。

* KAMONOHASHI の Admin ユーザーのパスワードについては、KAMONOHASHI のユーザ管理画面から変更をしてください。

### DB のパスワード更新

* root ユーザーで次の DB の認証情報更新用コマンドを実行します。

```
cd /var/lib/kamonohashi/deploy-tools
./deploy-kamonohashi.sh credentials db
```

* 「DB Passwordを入力」と表示されるため、新たに設定するパスワードを入力してください。

### MinIO ストレージのパスワード更新

* root ユーザーで次の MinIO ストレージの認証情報更新用コマンドを実行します。

```
cd /var/lib/kamonohashi/deploy-tools
./deploy-kamonohashi.sh credentials storage
```

* 「新しいStorage Secret Keyを入力」と表示されるため、新たに設定するパスワードを入力してください。
* 新たに設定したパスワードを、KAMONOHASHI のストレージ管理画面から該当ストレージの「シークレットキー」を更新してください。

### KAMONOHASHI の各パスワード更新

* DB と MinIO ストレージのパスワードの両方を更新するため、次の更新用コマンドを実行します。

```
cd /var/lib/kamonohashi/deploy-tools
./deploy-kamonohashi.sh credentials all
```

* 「DB Passwordを入力」と表示されるため、新たに設定するパスワードを入力してください。
* 「新しいStorage Secret Keyを入力」と表示されるため、新たに設定するパスワードを入力してください。
* 新たに設定したパスワードを、KAMONOHASHI のストレージ管理画面から該当ストレージの「シークレットキー」を更新してください。

