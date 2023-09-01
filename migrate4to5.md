---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: "KAMONOHASHI 4.xから5.xへの移行"
permalink: /docs/install-and-update/migrate4xto5x
sidebar:
  nav: "docs"
---

## 考慮事項

- KAMONOHASHI 5.x では 4.x から DeepOps のバージョンアップを行いました。

## 事前準備

- KAMONOHASHI のバックアップが取れていることを確認します
- KAMONOHASHI の Admin アカウントでログインできることを確認します

## KAMONOHASHI 4.x のアンインストール

```
cd /var/lib/kamonohashi/deploy-tools/
./deploy-kamonohashi.sh clean all
```

- アンインストールは ssh を用いて実行されます。
  - 設定されている ssh ユーザは`deepops/config/inventory`の`ansible_user`の項目で確認できます。
  - ssh ユーザでの SSH にパスワードが必要な場合は、clean all に`-k`オプションを追加します。sudo にパスワードが必要な場合は`-K`のオプションを指定します。

```
pip uninstall ansible
```
* ansibleのバージョンアップのために、現行のansibleをアンインストールします

### KAMONOHASHI バージョン 5.0.0 用の構築ツールを入手します

```
cd /var/lib/kamonohashi/deploy-tools/
git fetch --tags
git checkout 5.0.0.1
git submodule update --init --recursive
```

- 構築ツールが正常に取得できているか次のコマンドで確認します

```
echo KAMONOHASHI deploy tool: $(git tag --points-at HEAD)
echo deepops: $(cd deepops; git tag --points-at HEAD)
```

- KAMONOHASHI deploy tool: 5.0.0.1 , deepops: 23.08 　が表示されれば正常です。
- 正常でない場合、`git submodule update --init --recursive`でエラーが出ていないかを確認してください

### 構築に必要なパッケージを入手

- k8s-master の構築用ユーザーで次のコマンドを実行し、構築に必要なパッケージを入手します

```
./deploy-kamonohashi.sh prepare
```

### 移行の設定
```
./deploy-kamonohashi.sh configure verup
```
- 上記実行後にdeepops/config/settings.ymlに次の設定を追加します
```
kube_feature_gates:
        - "LegacyServiceAccountTokenNoAutoGeneration=False"
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
