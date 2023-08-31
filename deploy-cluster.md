---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: "クラスタ構成でのインストール"
permalink: /docs/install-and-update/deploy-cluster
sidebar:
  nav: "docs"
---

## 構成について

KAMONOHASHI のクラスタは次の 4 種類のサーバで構成されます

![マシーン](/assets/images/basic-cluster-machenes.png)

- Kubernetes master: ディープラーニングの実行スケジューリング等に使用します
- KAMONOHASHI: KAMONOHASHI の WEB システム(Web,DB コンテナ)で使用します
- Storage: 学習用データと学習結果ファイルの保管に使用します
- 計算ノード: ディープラーニングの実行に使用します。GPU サーバ・CPU サーバを指定可能です

#### 構築の準備

- マシンを用意します
  - クラスタ構成では 4 種類のサーバを別々のマシンにインストールする前提です
  - 同一マシンにインストールすることも可能ですが、テストしていません
- [マシンの要件](/docs/install-and-update/prerequisite)に合わせた設定をしてください
- 各サーバの最小リソース要件は下記になります。

  - データ・ユーザ数・実施するディープラーニングの内容に応じて下記よりも多く必要になる場合があります

  | マシン種別        | CPU    | メモリ | 備考                                                                           |
  | :---------------- | :----- | :----- | :----------------------------------------------------------------------------- |
  | Kubernetes master | 2 コア | 2 GB   |                                                                                |
  | KAMONOHASHI       | 4 コア | 8 GB   | /var/lib/に 10GB 以上の空き容量                                                |
  | Storage           | 1 コア | 2 GB   | /var/lib/に学習データ・学習結果ファイル分の空き容量                            |
  | GPU サーバ        | 2 コア | 2 GB   | Fermi (2.1)より後の世代の NVIDIA GPU, /var/lib に 1 学習分のデータが入る空容量 |

## 構築ツールのセットアップ

- Kubernetes master をインストールするマシンにログインします。
- `mkdir -p /var/lib/kamonohashi/ && cd /var/lib/kamonohashi/ `を実行します
- `git clone https://github.com/KAMONOHASHI/deploy-tools.git -b 5.0.0.1 --recursive`を実行してデプロイスクリプトを入手します
- `/var/lib/kamonohashi/deploy-tools/`に移動します
- プロキシ環境下では次のファイルにプロキシ設定を記載してください
  - `./deepops/scripts/deepops/proxy.sh`
  - no_proxy には`localhost,127.0.0.1,.cluster.local,使用する各マシンのIPアドレス・ホスト名`の記載を含めてください
  - OS の no_proxy 設定にも上記と同様の no_proxy 設定が含まれるように設定してください。`/etc/environment`で指定することを推奨します
- `./deploy-kamonohashi.sh prepare`を実行して構築に必要なソフトウェアをインストールします
  - ansible でエラーが出る場合は ansible のアンインストールを実行してから`prepare`を実行してください
    - スクリプト実行中に適切な ansible がインストールされます

## デプロイ構成の設定

`./deploy-kamonohashi.sh configure cluster`を実行します。
対話形式で聞かれる以下の内容を入力します

| 質問文                                       | 解説                                                                                                                |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| Kubernetes master を<br>デプロイするサーバ名 |                                                                                                                     |
| KAMONOHASHI を<br>デプロイするサーバ名       |                                                                                                                     |
| Storage をデプロイするサーバ名               | HW ベンダーの NFS を使用する場合は[カスタマイズ設定ガイド](/docs/install-and-update/customize-4x)を参照してください |
| 計算ノード名                                 | ,区切りで複数指定できます。<br>例: gpu1,gpu2,gpu3                                                                   |
| SSH で利用するユーザ名:                      | 構築時に使用する SSH ユーザを指定します。構築ツールが SSH 経由で構築を行う仕様のため、指定が必要になります          |

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

実行後、対話形式で聞かれる以下の内容を入力します

| 質問文                                   | 解説                                                                                                                                                                                                                                                                                                                                            |
| ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Admin Password を入力:                   | KAMONOHASHI の admin アカウントで使用する 8 文字以上のパスワードです。数字のみのパスワードは使用不可となっているので注意してください。KAMONOHASHI Web UI ログイン・DB 接続、Object Storage へのログインに使用します。<br>パスワードを変更したい場合は[認証情報の更新](/docs/how-to/infra/#認証情報の更新)からパスワードの更新を行ってください。 |
| SSH password:                            | 構築時に使用する、ssh ユーザのパスワードです。`-k`指定時のみ聞かれます                                                                                                                                                                                                                                                                          |
| SUDO password[defaults to SSH password]: | 構築時に使用する、ssh ユーザの sudo パスワードです。`-K`指定時のみ聞かれます。                                                                                                                                                                                                                                                                  |

入力後に構築が始まります。
構築には 20 分程かかります。

構築完了のメッセージが出たらブラウザで次の URL にアクセスし、admin ユーザでログインして利用を開始してください

- KAMONOHASHI: http://KAMONOHASHI 用サーバ/kamonohashi
- アクアリウム機能: http://KAMONOHASHI 用サーバ/aquarium
