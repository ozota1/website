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
KAMONOHASHIのクラスタは次の4種類のサーバーで構成されます

![マシーン](/assets/images/basic-cluster-machenes.png)

* Kubernetes master: ディープラーニングの実行スケジューリング等に使用します
* KAMONOHASHI: KAMONOHASHIのWEBシステム(Web,DBコンテナ)で使用します
* Storage: 学習用データと学習結果ファイルの保管に使用します
* 計算ノード: ディープラーニングの実行に使用します。GPUサーバー・CPUサーバーを指定可能です

#### 構築の準備
* マシンを用意します
  * クラスタ構成では4種類のサーバーを別々のマシンにインストールする前提です
  * 同一マシンにインストールすることも可能ですが、テストしていません
* [マシンの要件](/prerequisite)に合わせた設定をしてください
* 各サーバーの最小リソース要件は下記になります。
  * データ・ユーザー数・実施するディープラーニングの内容に応じて下記よりも多く必要になる場合があります

  |マシン種別|CPU|メモリ|備考|
  |:---|:---|:---|:---|
  |Kubernetes master|2 コア|2 GB||
  |KAMONOHASHI|4 コア|8 GB|/var/lib/に10GB以上の空き容量|
  |Storage|1 コア|2 GB|/var/lib/に学習データ・学習結果ファイル分の空き容量|
  |GPUサーバー|2 コア|2 GB|Fermi (2.1)より後の世代のNVIDIA GPU, /var/libに1学習分のデータが入る空容量|

## 構築ツールのセットアップ
* Kubernetes masterをインストールするマシンにログインします。
* `sudo su -`を実行し、rootユーザーになります
* `mkdir -p /var/lib/kamonohashi/ && cd /var/lib/kamonohashi/ `を実行します
* `git clone https://github.com/KAMONOHASHI/deploy-tools.git -b 2.0.0.2 --recursive`を実行してデプロイスクリプトを入手します
* `/var/lib/kamonohashi/deploy-tools/`に移動します
* プロキシ環境下では次のファイルにプロキシ設定を記載してください
  * `./deepops/scripts/proxy.sh`
  * no_proxyには`localhost,127.0.0.1,.cluster.local,使用するマシンのIPアドレス・ホスト名`の記載をしてください
* `./deploy-kamonohashi.sh prepare`を実行して構築に必要なソフトウェアをインストールします
  * ansibleでエラーが出る場合はansibleのアンインストールを実行してから`prepare`を実行してください

## デプロイ構成の設定 
`./deploy-kamonohashi.sh configure cluster`を実行します。
対話形式で聞かれる以下の内容を入力します

|質問文|解説|
|---|---|
|Kubernetes masterを<br>デプロイするサーバ名||
|KAMONOHASHIを<br>デプロイするサーバ名||
|Storageをデプロイするサーバ名|HWベンダーのNFSを使用する場合は|
|計算ノード名|,区切りで複数指定できます。<br>例: gpu1,gpu2,gpu3 |
|SSHで利用するユーザー名:|構築時に使用するSSHユーザーを指定します。構築ツールがSSH経由で構築を行う仕様のため、指定が必要になります|
|プロキシを設定しますか？ [y/N]|プロキシ環境にデプロイする場合はyを入力して<br> http_proxy, https_proxy, no_proxy<br>を設定します<br>no_proxyはこれまでの入力内容を元に必要なものが自動生成されます。<br>自組織のドメイン等を生成されたno_proxyに更に追加することもできます|

入力内容に応じ、以下の設定ファイルに書き込みが行われます
* deepopsの設定ファイル(deepops/config/inventry)
* kamonohashiの設定ファイル(kamonohashi/conf/settings.yml)

設定内容をカスタマイズする場合は次を参照し、設定ファイルの編集を行ってください。
[カスタマイズ設定ガイド](/docs/install-and-update/customize-2x)

## デプロイの実行
`./deploy-kamonohashi.sh deploy all`を実行します。
この際にデプロイ構成の設定で指定したユーザーでSSHが実行されます。
指定したユーザーでのSSHにパスワードが必要な場合は`-k`、
指定したユーザーでのsudoにパスワードが必要な場合は`-K`のオプションを指定します。
例： `./deploy-kamonohashi.sh deploy all -k -K`

実行後、対話形式で聞かれる以下の内容を入力します


|質問文|解説|
|---|---|
|Admin Passwordを入力:|KAMONOHASHIのadminアカウントで使用する8文字以上のパスワードです。数字のみのパスワードは使用不可となっているので注意してください。KAMONOHASHI Web UIログイン・DB接続、Object Storageへのログインに使用します。<br>一度構築に使用したパスワードはデプロイツールでは変更できません。パスワードを変える場合は、完全にデータを削除するか、パスワード変更手順を実施する必要があります。パスワード変更手順は[kamonohashi-support@jp.nssol.nipponsteel.com](mailto:kamonohashi-support@jp.nssol.nipponsteel.com)にお問い合わせください|
|SSH password: |構築時に使用する、sshユーザーのパスワードです。`-k`指定時のみ聞かれます|
|SUDO password[defaults to SSH password]: |構築時に使用する、sshユーザーのsudoパスワードです。`-K`指定時のみ聞かれます。|

入力後に構築が始まります。
構築には20分程かかります。

* DGX利用時のみ、構築後に次の作業を行ってください
  * `rm /etc/systemd/system/docker.service.d/docker-override.conf`
  * これは構築に使用する NVIDIA deepopsのバグで、20.02.1の次のdeepopsのリリースがされれば[対応される見込み](https://github.com/NVIDIA/deepops/commit/980cfe42685e17f0d3688fe50b1939aeaa51f314#diff-25c48ad81ab2a8e8c03e25d8d023bc1c)です。

構築後にアクセス用のURLが表示されるので、それをブラウザで開きます
