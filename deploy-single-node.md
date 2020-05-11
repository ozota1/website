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
* [マシンの要件](/docs/install-and-update/prerequisite)に合わせた設定をしてください
* CPU8コアメモリ32GB以上を推奨します


## 構築ツールのセットアップ
* インストール対象マシンにログインします。
* `sudo su -`を実行し、rootユーザーになります
* `mkdir -p /var/lib/kamonohashi/ && cd /var/lib/kamonohashi/ `を実行します
* `git clone https://github.com/KAMONOHASHI/deploy-tools.git -b 2.0.0.6 --recursive`を実行してデプロイスクリプトを入手します
* `/var/lib/kamonohashi/deploy-tools/`に移動します
* プロキシ環境下では次のファイルにプロキシ設定を記載してください
  * `./deepops/scripts/proxy.sh`
  * no_proxyには`localhost,127.0.0.1,.cluster.local,使用するマシンのIPアドレス・ホスト名`の記載をしてください
* `./deploy-kamonohashi.sh prepare`を実行して構築に必要なソフトウェアをインストールします
  * ansibleでエラーが出る場合はansibleのアンインストールを実行してから`prepare`を実行してください
    * スクリプト実行中に適切なansibleがインストールされます

## デプロイ構成の設定 
`./deploy-kamonohashi.sh configure single-node`を実行します。


対話形式で聞かれる以下の内容を入力します


|質問文|解説|
|---|---|
|SSHで利用するユーザー名:|構築時に使用するSSHユーザーを指定します。構築ツールがSSH経由で構築を行う仕様のため、指定が必要になります|

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

* sshキーを~/.ssh/id_rsaで配置している場合は、ペアのid_rsa.pubも~/.sshに配置してください

実行後、対話形式で聞かれる以下の内容を入力します


|質問文|解説|
|---|---|
|Admin Passwordを入力:|KAMONOHASHIのadminアカウントで使用する8文字以上のパスワードです。数字のみのパスワードは使用不可となっているので注意してください。KAMONOHASHI Web UIログイン・DB接続、Object Storageへのログインに使用します。<br>一度構築に使用したパスワードはデプロイツールでは変更できません。パスワードを変える場合は、完全にデータを削除するか、パスワード変更手順を実施する必要があります。パスワード変更手順は[kamonohashi-support@jp.nssol.nipponsteel.com](mailto:kamonohashi-support@jp.nssol.nipponsteel.com)にお問い合わせください。|
|SSH password: |構築時に使用する、sshユーザーのパスワードです。`-k`指定時のみ聞かれます|
|SUDO password[defaults to SSH password]: |構築時に使用する、sshユーザーのsudoパスワードです。`-K`指定時のみ聞かれます。|

入力後に構築が始まります。

初回はGPUドライバのインストール後に再起動されます。  
再起動後rootユーザーでログインしなおし、再度デプロイスクリプトを実行してください。
```bash
cd /var/lib/kamonohashi/
./deploy-kamonohashi.sh deploy all
```
構築には20分程かかります。

* DGX利用時のみ、構築後に次の作業を行ってください

```bash
rm /etc/systemd/system/docker.service.d/docker-override.conf
```
これは構築に使用する NVIDIA deepopsのバグで、20.02.1の次のdeepopsのリリースがされれば[対応される見込み](https://github.com/NVIDIA/deepops/commit/980cfe42685e17f0d3688fe50b1939aeaa51f314#diff-25c48ad81ab2a8e8c03e25d8d023bc1c)です。

構築後にアクセス用のURLが表示されるので、それをブラウザで開きます
