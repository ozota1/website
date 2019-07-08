---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: "Installation"
permalink: /docs/install-and-update
sidebar:
  nav: "docs"
---
本書ではKAMONOHASHIのインストール方法、アンインストール方法、バージョンアップ方法について説明します。

## インストール方法


### ベーシッククラスタの構築

#### 構成について
KAMONOHASHIのクラスタは次の4種類のサーバーで構成されます

![マシーン](/assets/images/basic-cluster-machenes.png)

* Kubernetes master: ディープラーニングの実行スケジューリング等に使用します
* KAMONOHASHI: KAMONOHASHIのWEBシステム(Web,DBコンテナ)で使用します
* Storage: 学習用データと学習結果ファイルの保管に使用します
* GPUサーバー: ディープラーニングの実行に使用します

ベーシッククラスタ構成では、Kubernetes, KAMONOHASHI, Storage に1台ずつのマシンと、
複数台のGPUサーバーを想定しています

#### 構築の準備
* マシンを用意します
  * 物理または仮想のマシンを3台（Kubernetes, KAMONOHASHI, Storage　に使用）
  * NVIDIA GPUを搭載したマシンを1台以上
* 全てのマシンがAMD64(Intel 64bit CPU)である必要があります
* 各サーバーの最小リソース要件は下記になります。
  * データ・ユーザー数・実施するディープラーニングの内容に応じて下記よりも多く必要になる場合があります

  |マシン種別|CPU|メモリ|備考|
  |:---|:---|:---|:---|
  |Kubernetes master|2 コア|2 GB||
  |KAMONOHASHI|4 コア|8 GB|/var/lib/に10GB以上の空き容量|
  |Storage|1 コア|2 GB|/var/lib/に学習データ・学習結果ファイル分の空き容量|
  |GPUサーバー|2 コア|2 GB|Fermi (2.1)より後の世代のNVIDIA GPU|

* 全てのマシンに Ubuntu Server 16.04 をインストールします
* 全てのマシンに共通のアカウントでsshログインできるようにします
  * そのアカウントが全てのマシンでsudoできるようにします
  * sshキーを使用する場合は、id_rsaファイルをKubernetes masterマシンの/root/.ssh/に所有者root、パーミッション0600で配置します
* 用意したマシンの名前解決が出来るようにします
  * KAMONOHASHIユーザーの端末、各マシン上で名前解決可能にします。DNS利用を強く推奨します。
* NTPを設定し、各マシンの時刻を揃えます
* 各マシンがインターネットアクセス出来るようにします
* GPUサーバーにGPUドライバをインストールします。
  * [NVIDIAドライバダウンロードサイト](https://www.nvidia.co.jp/Download/index.aspx?lang=jp)からインストール用ファイルがダウンロード可能です

#### 構築方法
* Kubernetes master用に用意したマシンにログインします
* root userで次を実行します
```bash
KQI_VERSION=1.0.2
wget -O /tmp/deploy-tools-$KQI_VERSION.tar.gz https://github.com/KAMONOHASHI/kamonohashi/releases/download/$KQI_VERSION/deploy-tools-$KQI_VERSION.tar.gz
mkdir -p /var/lib/kamonohashi/deploy-tools/$KQI_VERSION/
cd /var/lib/kamonohashi/deploy-tools/$KQI_VERSION/
tar --strip=1 -xf /tmp/deploy-tools-$KQI_VERSION.tar.gz
sed -i s/docker18.09.6-3/docker18.09.5-3/ infra/conf/group_vars/all.yml
./deploy-basic-cluster.sh deploy
```
対話形式で設定が聞かれるので、下記に従って設定を入力します

![ターミナル](/assets/images/kqi-terminal.png)


対話形式で以下の項目の質問に答えます。[y/n]形式での質問は大文字の方がデフォルトの値です。

|質問文|解説|
|---|---|
|Kubernetes masterを<br>デプロイするサーバ名||
|KAMONOHASHIを<br>デプロイするサーバ名||
|Storageをデプロイするサーバ名||
|GPU サーバ名|,区切りで複数指定できます。<br>例: gpu1,gpu2,gpu3 |
|SSHユーザー名|構築の準備で用意したSSHユーザー名を指定します|
|SSHパスワード|SSHにパスワードを使用する場合は入力します。<br>SSH認証キー`~/.ssh/id_rsa`を使う場合は何も入力せずにEnterを押してこの項目はスキップします|
|SUDOパスワード|パスワードなしでsudoコマンド実行可能な場合は何も入力せずにEnterを押してこの項目をスキップします|
|プロキシを設定しますか？ [y/N]|プロキシ環境にデプロイする場合はyを入力して<br> http_proxy, https_proxy, no_proxy<br>を設定します<br>no_proxyはこれまでの入力内容を元に必要なものが自動生成されます。<br>自組織のドメイン等を生成されたno_proxyに更に追加することもできます|
|KAMONOHASHIのadminパスワード|adminアカウントで使用する8文字以上のパスワードです。数字のみのパスワードは使用不可となっているので注意してください。KAMONOHASHI Web UIログイン・DB接続、Object Storageへのログインに使用します。<br>一度構築に使用したパスワードはデプロイツールでは変更できません。パスワードを変える場合は、完全にデータを削除するか、パスワード変更手順を実施する必要があります。パスワード変更手順は[kamonohashi-support@jp.nssol.nipponsteel.com]にお問い合わせください|

これでKAMONOHASHIのインストールは完了です。
[チュートリアル](/docs/tutorial)に進みKAMONOHASHIを用いたAI開発を開始しましょう！

### カスタマイズしたクラスタの構築
* ベーシッククラスタの構成では要件が足りず、カスタマイズしたい場合は[kamonohashi-support@jp.nssol.nipponsteel.com]にお問い合わせください


## アンインストール方法
* `./deploy-basic-cluster.sh clean`を実行するとソフトウェアがアンインストールされます。
  * このコマンドではKAMONOHASHIの内部データ(データベース, ストレージのデータ)は削除しません
    * 特に、adminパスワードも保存されたままです
  * 再度デプロイすると過去のデータベース, ストレージの中身を引き続き使用します
  * 完全にデータを削除する場合は KAMONOHASHIノード, STORAGEノードの 2台で`/var/lib/kamonohashi` を削除してください
    * 特に、構築に失敗してやり直す際にパスワードも変更する場合はこのディレクトリを削除してください
    
## バージョンアップ
バージョンアップには次の2種類のバージョンアップがあります
* KAMONOHASHI Webアプリのみのバージョンアップ
* k8sなども含めたインフラ全体のバージョンアップ

どちらもバージョンアップするバージョンのデプロイツールを準備する必要があります

### デプロイツールの準備
1. 現在のKAMONOHASHIのバージョンをシェル変数で指定します
```bash:現在1.0.0を使用している場合
OLD_KQI_VERSION=1.0.0
```

2. 次のコマンドを実施して新しいデプロイツール取得と設定ファイルのコピーを行います
```bash
KQI_VERSION=1.0.2
wget -O /tmp/deploy-tools-$KQI_VERSION.tar.gz https://github.com/KAMONOHASHI/kamonohashi/releases/download/$KQI_VERSION/deploy-tools-$KQI_VERSION.tar.gz
mkdir -p /var/lib/kamonohashi/deploy-tools/$KQI_VERSION/
cd /var/lib/kamonohashi/deploy-tools/$KQI_VERSION/
tar --strip=1 -xf /tmp/deploy-tools-$KQI_VERSION.tar.gz
cd /var/lib/kamonohashi/deploy-tools/
cp -nr $OLD_KQI_VERSION/infra/conf $KQI_VERSION/infra/
cp -nr $OLD_KQI_VERSION/kamonohashi/conf $KQI_VERSION/kamonohashi/
mkdir -p old
mv $OLD_KQI_VERSION old/
sed -i s/docker18.09.6-3/docker18.09.5-3/ infra/conf/group_vars/all.yml
```

### KAMONOHASHI Webアプリのみのバージョンアップ
デプロイツールの準備を実施後に次を実施してください

```bash
cd /var/lib/kamonohashi/deploy-tools/1.0.1/kamonohashi/
./deploy-kqi.sh update
```

### k8sなども含めたインフラ全体のバージョンアップ
現在デプロイツールでは古いバージョンのアンインストールと新しいバージョンのインストールによるアップグレードのみ可能です。
それは次を考慮しているためです。
* k8sを2マイナーバージョン以上アップデートできる
* マシンの移行も同じ方法でサポートできる
* cordonとuncordonによる無停止アップグレードは、ディープラーニングの動いているシステムでは難しい
  * ディープラーニングジョブがノードからはけるのに数日かかることからクラスタ全体のアップグレードでは数週間が必要になるためです

インフラ全体のバージョンアップ手順は次になります
* 古いバージョンのデプロイツールでアンインストールを実行
  * 詳細はアンインストールの項目を参照

```
cd /var/lib/kamonohashi/deploy-tools/$OLD_KQI_VERSION/
./deploy-basic-cluster.sh clean
```

*　新しいバージョンのデプロイツールでインストールを実行
  * 詳細はインストールの項目を参照
  * パスワードは初期構築時と同じものを指定してください
  
```
cd /var/lib/kamonohashi/deploy-tools/$KQI_VERSION/
./deploy-basic-cluster.sh deploy
```

* 注意事項
  * デプロイツールやKAMONOHASHI WEBアプリ外で手で入れた設定は元に戻ります

## 外部サービスとの互換性

動作を確認した環境は以下の通りです。

|KAMONOHASHI|GitLab|MinIO| LDAP|Kubernetes |
|---|---|---|---|---|
|v1.0.1, v1.0.2|11.8以降|RELEASE.2019-01-23T23-18-58Z|version 3| v1.12.7,v1.14.1|
|v1.0.0|11.7以前|RELEASE.2019-01-23T23-18-58Z|version 3| v1.12.7|

v1.0.0では11.8以降のGitLabに対応していませんので注意してください。


