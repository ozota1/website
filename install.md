---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: "Install Guide"
permalink: /docs/beforequickstart/install
sidebar:
  nav: "docs"
---

# はじめに
本書ではKAMONOHASHIのインストールについて説明します。

**※2019年4月段階の情報であり、2019年5月のOSS化時に変更になる可能性があります。また、実際にインストールできるのは2019年5月のOSS化後です。今しばらくお待ちください。**

# ベーシッククラスタの構築について

## 構成について
KAMONOHASHIのクラスタは次の4種類のサーバーで構成されます

![マシーン](/assets/images/basic-cluster-machenes.png)

* Kubernetes master: ディープラーニングの実行スケジューリング等に使用します
* Storage: データの保管に使用します
* KAMONOHASHI: KAMONOHASHIのWEBシステムです
* GPUサーバー: ディープラーニングの実行に使用します

ベーシッククラスタ構成では、Kubernetes, Storage, KAMONOHASHI に1台ずつのマシンと、
複数台のGPUサーバーを想定しています

# 構築の準備
* 物理または仮想のマシンを3台（Kubernetes, Storage, KAMONOHASHI　に使用）
* NVIDIA GPUを搭載したマシンを1台以上用意する
* 全てのマシンに Ubuntu Server 16.04 をインストールする
* 全てのマシンに共通のアカウントでsshログインできるようにする
  * そのアカウントが全てのマシンでsudoできるようにする 
* 各マシン同士でホスト名の名前解決が出来るようにする
  * 各マシンのホスト名をメモする。(ホスト名はOSにログインして`hostname`コマンドで確認できる)
* 各マシンがインターネットアクセス出来るようにする

# 構築方法について
git cloneでdeploy-toolを取得し
rootユーザーで次のコマンドを実行します　
```bash
./deploy-basic-cluster.sh
```
![ターミナル](/assets/images/kqi-terminal.png)


対話形式で以下の項目の質問に答えます
* Kubernetes masterをデプロイするサーバ名
* KAMONOHASHIをデプロイするサーバ名
  * Kubernetes masterと同一は不可
* Storageをデプロイするサーバ名
* GPU サーバ名
  * ,区切りで複数指定することが可能
    * 例: gpu1,gpu2,gpu3 
  * Kubernetes masterと同一は不可
* SSHユーザー名
  * 構築の準備で用意したSSHユーザー名を指定
* SSHパスワード
  * SSHにパスワードを使用する場合は入力
  * SSH認証キー`~/.ssh/id_rsa`を用意している場合は何も入力せずにEnterを押してこの項目はスキップ
* SUDOパスワード
  * パスワードなしでSUDO可能な場合は何も入力せずにEnterを押してこの項目をスキップ
* KAMONOHASHIにブラウザでアクセスするURLはhttp://<KAMONOHASHI用ホスト名>でよいですか?
  * デフォルトでは http://<KAMONOHASHI用ホスト名>でアクセスできます
  * クラウド等を利用し、別ホスト名でアクセスする必要がある場合にはnを入力し、別途設定する
    * 例： http://aaa.bbb.ccc でブラウザアクセスする場合は`aaa.bbb.ccc`とする
* プロキシを設定しますか？ [y/n]
  * プロキシ環境にデプロイする場合は http_proxy, https_proxy, no_proxyを設定する
  * no_proxyは自動生成されるが、必要があれば追加で設定することが可能
  * 備考: Proxy使用時には各サーバーの/etc/environmentのProxy設定を書き換えます
* nvidia-384 GPUドライバを自動インストールしますか？　[y/n]
  * GPU型番に合わせたドライバを別途インストールする場合は n
  * 一律で nvidia-384 をインストールする場合は y