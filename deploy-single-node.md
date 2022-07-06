---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: "1ノード構成でのインストール"
permalink: /docs/install-and-update/deploy-single-node
sidebar:
  nav: "docs"
---

#### 構築の準備

- [マシンの要件](/docs/install-and-update/prerequisite)に合わせた設定をしてください
- CPU8 コアメモリ 32GB 以上を推奨します

## 構築ツールのセットアップ

- インストール対象マシンにログインします。
- `mkdir -p /var/lib/kamonohashi/ && cd /var/lib/kamonohashi/ `を実行します
- `git clone https://github.com/KAMONOHASHI/deploy-tools.git -b 4.0.1.0 --recursive`を実行してデプロイスクリプトを入手します
- `/var/lib/kamonohashi/deploy-tools/`に移動します
- プロキシ環境下では次のファイルにプロキシ設定を記載してください
  - `./deepops/scripts/deepops/proxy.sh`
  - no_proxy には`localhost,127.0.0.1,.cluster.local,使用する各マシンのIPアドレス・ホスト名`の記載を含めてください
  - OS の no_proxy 設定にも上記と同様の no_proxy 設定が含まれるように設定してください。`/etc/environment`で指定することを推奨します
- `./deploy-kamonohashi.sh prepare`を実行して構築に必要なソフトウェアをインストールします
  - ansible でエラーが出る場合は ansible のアンインストールを実行してから`prepare`を実行してください
    - スクリプト実行中に適切な ansible がインストールされます

## デプロイ構成の設定

`./deploy-kamonohashi.sh configure single-node`を実行します。

対話形式で聞かれる以下の内容を入力します

| 質問文                  | 解説                                                                                                       |
| ----------------------- | ---------------------------------------------------------------------------------------------------------- |
| SSH で利用するユーザ名: | 構築時に使用する SSH ユーザを指定します。構築ツールが SSH 経由で構築を行う仕様のため、指定が必要になります |

入力内容に応じ、以下の設定ファイルに書き込みが行われます

- deepops の設定ファイル(deepops/config/inventry)
- kamonohashi の設定ファイル(kamonohashi/conf/settings.yml)

設定内容をカスタマイズする場合は次を参照し、設定ファイルの編集を行ってください。
[カスタマイズ設定ガイド](/docs/install-and-update/customize-4x)

## デプロイの実行

`./deploy-kamonohashi.sh deploy all`を実行します。
この際にデプロイ構成の設定で指定したユーザで SSH が実行されます。
指定したユーザでの SSH にパスワードが必要な場合は`-k`、
指定したユーザでの sudo にパスワードが必要な場合は`-K`のオプションを指定します。

例： `./deploy-kamonohashi.sh deploy all -k -K`

- ssh キーを~/.ssh/id_rsa で配置している場合は、ペアの id_rsa.pub も~/.ssh に配置してください

実行後、対話形式で聞かれる以下の内容を入力します

| 質問文                                   | 解説                                                                                                                                                                                                                                                                                                                                            |
| ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Admin Password を入力:                   | KAMONOHASHI の admin アカウントで使用する 8 文字以上のパスワードです。数字のみのパスワードは使用不可となっているので注意してください。KAMONOHASHI Web UI ログイン・DB 接続、Object Storage へのログインに使用します。<br>パスワードを変更したい場合は[認証情報の更新](/docs/how-to/infra/#認証情報の更新)からパスワードの更新を行ってください。 |
| SSH password:                            | 構築時に使用する、ssh ユーザのパスワードです。`-k`指定時のみ聞かれます                                                                                                                                                                                                                                                                          |
| SUDO password[defaults to SSH password]: | 構築時に使用する、ssh ユーザの sudo パスワードです。`-K`指定時のみ聞かれます。                                                                                                                                                                                                                                                                  |

入力後に構築が始まります。

初回は GPU ドライバのインストール後に再起動されます。  
再起動後、再度デプロイスクリプトを実行してください。

```bash
cd /var/lib/kamonohashi/deploy-tools/
./deploy-kamonohashi.sh deploy all
```

構築には 20 分程かかります。
途中で既知の不具合で構築エラーが発生することがあるため、その際は再度実行してください

構築完了のメッセージが出たらブラウザで次の URL にアクセスし、admin ユーザでログインして利用を開始してください

- KAMONOHASHI: http://KAMONOHASHI 用サーバ/kamonohashi
- アクアリウム機能: http://KAMONOHASHI 用サーバ/aquarium
