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

- KAMONOHASHI 4.x ではアプリのフレームワークのバージョンアップを行いました。
- それに伴い、4.x へのバージョンアップ後は、3.x へのバージョンダウンはできません。
  - DB のバックアップを用いた、過去のバージョンの再デプロイのみ可能です

## 事前準備

- KAMONOHASHI のバックアップが取れていることを確認します
- KAMONOHASHI の Admin アカウントでログインできることを確認します

## KAMONOHASHI 3.x のアンインストール

```
cd /var/lib/kamonohashi/deploy-tools/
./deploy-kamonohashi.sh clean all
```

- アンインストールは ssh を用いて実行されます。
  - 設定されている ssh ユーザは`deepops/config/inventory`の`ansible_user`の項目で確認できます。
  - ssh ユーザでの SSH にパスワードが必要な場合は、clean all に`-k`オプションを追加します。sudo にパスワードが必要な場合は`-K`のオプションを指定します。

### KAMONOHASHI バージョン 4.0.1 用の構築ツールを入手します

```
cd /var/lib/kamonohashi/deploy-tools/
git fetch --tags
git checkout 4.0.1.0
git submodule update --init --recursive
```

- 構築ツールが正常に取得できているか次のコマンドで確認します

```
echo KAMONOHASHI deploy tool: $(git tag --points-at HEAD)
echo deepops: $(cd deepops; git tag --points-at HEAD)
```

- KAMONOHASHI deploy tool: 4.0.1.0 , deepops: 22.01 　が表示されれば正常です。
- 正常でない場合、`git submodule update --init --recursive`でエラーが出ていないかを確認してください

### 構築ツールの実行ユーザーの移行

- 過去の deepops では root ユーザーで実行しないと構築途中にエラーが発生していましたが、新しい deepops ではその部分の改善に加え、root ユーザーでの実行を禁止するようになりました。
- そのため、root 以外のユーザーを用意してそのユーザーで KAMONOHASHI の構築を実行する必要があります。そのためには過去に root ユーザーで実行していたスクリプトの所有者等の変更が必要になります。
  - sudo のできるユーザーを用意してください
  - KAMONOHASHI の全てのサーバーに SSH できるユーザーにしてください
- 下記のコマンドを k8s-master サーバーの root ユーザーで実行してください。deploy-user の部分は用意したデプロイ用ユーザーに変更してください

```
chown -R deploy-user:deploy-user /var/lib/kamonohashi/
chown -R deploy-user:deploy-user /var/log/kamonohashi/
```

- /var/lib/kamonohashi/deploy-tools/deepops/config/inventory を編集し、ansible_user の値を deploy-user に変更してください

### 構築に必要なパッケージを入手

- k8s-master の構築用ユーザーで次のコマンドを実行し、構築に必要なパッケージを入手します

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
  - 既知の不具合で helm による KAMONOHASHI のコンテナデプロイが一度エラーになりますが、再度`./deploy-kamonohashi.sh deploy all`を実行すればエラー部分を通過します

構築完了のメッセージが出たらブラウザで次の URL にアクセスし、admin ユーザでログインして利用を開始してください

- KAMONOHASHI: http://KAMONOHASHI 用サーバ/kamonohashi
- アクアリウム機能: http://KAMONOHASHI 用サーバ/aquarium
