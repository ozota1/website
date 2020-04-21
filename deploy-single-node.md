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


## 構築ツールのセットアップ
* インストール対象マシンにログインします。
* `sudo su -`を実行し、rootユーザーになります
* `mkdir -p /var/lib/kamonohashi/ && cd /var/lib/kamonohashi/ `を実行します
* `git clone https://github.com/KAMONOHASHI/deploy-tools.git -b 2.0.0 --recursive`を実行してデプロイスクリプトを入手します
* `./deploy-kamonohashi.sh prepare`を実行して構築に必要なソフトウェアをインストールします

## デプロイ構成の設定 
`./deploy-kamonohashi.sh configure single-node`を実行します。
対話形式で聞かれる以下の内容を入力します
|質問文|解説|
|---|---|
|SSHで利用するユーザー名:|構築時に使用するSSHユーザーを指定します。構築ツールがSSH経由で構築を行う仕様のため、指定が必要になります|
|プロキシを設定しますか？ [y/N]:|プロキシ環境にデプロイする場合はyを入力して<br> http_proxy, https_proxy, no_proxy<br>を設定します<br>no_proxyは必要なものが自動生成されますが、この項目に入力することで<br>自組織のドメイン等を生成されたno_proxyに更に追加することができます|

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

構築後にアクセス用のURLが表示されるので、それをブラウザで開きます