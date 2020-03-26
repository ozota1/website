# 1ノード構成でのインストール
## 構築ツールのセットアップ
* インストール対象マシンにログインします。
* `sudo su -`を実行し、rootユーザーになります
* (暫定手順)`git clone https://github.com/ozota1/kamonohashi.git -b deepops_deploy`を実行してスクリプトを入手します
  * (暫定手順)deploy-tools/kamonohashi/charts/*/Chart.yaml-templateをChart.yamlにリネームします
  * 各Chart.yamlの`${VERSION}`を`2.0.0`に書き換えます
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
[カスタマイズ設定ガイド]()

## デプロイの実行
`./deploy-kamonohashi.sh deploy all`を実行します。
この際にデプロイ構成の設定で指定したユーザーでSSHが実行されます。
指定したユーザーでのSSHにパスワードが必要な場合は`-k`、
指定したユーザーでのsudoにパスワードが必要な場合は`-K`のオプションを指定します。
例： `./deploy-kamonohashi.sh deploy all -k -K`

実行後、対話形式で聞かれる以下の内容を入力します
|質問文|解説|
|---|---|
|Admin Passwordを入力:|KAMONOHASHIのアプリケーションのAdminユーザーのパスワードです。|
|SSH password: |構築時に使用する、sshユーザーのパスワードです。`-k`指定時のみ聞かれます|
|SUDO password[defaults to SSH password]: |構築時に使用する、sshユーザーのsudoパスワードです。`-K`指定時のみ聞かれます。|

入力後に構築が始まります。
構築には20分程かかります。

構築後にアクセス用のURLが表示されるので、それをブラウザで開きます