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

# ベーシッククラスタの構築について

## 構成について
KAMONOHASHIのクラスタは次の4コンポーネントで構成されます。

![アーキテクチャ](/assets/images/basic-cluster-arc.bmp)

* Kubernetes: ディープラーニングのスケジューリング等に使用します
* Storage: データの保管に使用します
* GPUサーバー: ディープラーニングの実行に使用します
* KAMONOHASHI: KAMONOHASHIのWEBシステムです

ベーシッククラスタ構成では、Kubernetes, Storage, KAMONOHASHI に1台ずつのマシンと、
複数台のGPUサーバーを想定しています。

# 構築の準備
* 物理または仮想のマシンを3台、NVIDIA GPUを搭載したマシンを1台用意する
* 各マシン同士でマシン名の名前解決が出来るようにする
* 各マシンがインターネットアクセスが出来るようにする

# 構築方法について
git cloneでdeploy-toolを取得し
rootユーザーで次のコマンドを実行します。　（後日変更予定）

```bash
./deploy-basic-cluster.sh
```
![ターミナル](/assets/images/kqi-terminal.bmp)


対話形式で以下の項目の質問に答えます。
各サーバー名はマシンのOS上で`hostname`コマンドを打つことで確認できます。
* Kubernetes masterをデプロイするサーバ名
* KAMONOHASHIをデプロイするサーバ名
* Storageをデプロイするサーバ名
* GPU サーバ名
  * ,区切りで複数指定することができます
  * 例: gpu1,gpu2,gpu3 
* SSHユーザー名
  * 構築に使用するSSHユーザー名を指定してください
  * そのユーザーにはSSH後にSUDOを実行できる権限が必要です
* SSHパスワード
  * SSHにパスワードを使用する場合は入力してください
  * SSH認証キー`~/.ssh/id_rsa`を用意している場合は何も入力せずにEnterを押してこの項目はスキップしてください
* SUDOパスワード
  * パスワードなしでSUDO可能な場合は何も入力せずにEnterを押してこの項目はスキップしてください
* ブラウザアクセスホスト名
  * デフォルトでは http://<KAMONOHASHIをデプロイするサーバ名>でアクセスできます
  * クラウド等を利用し、別ホスト名でアクセスする必要がある場合に使用する項目です
  * 例： http://aaa.bbb.ccc でブラウザアクセスする場合は`aaa.bbb.ccc`になります

# インストール完了の確認
上記で指定したブラウザアクセスホスト名(例: http://aaa.bbb.ccc)にアクセスします。

KAMONOHASHIのログイン画面が表示されたことを確認できたら、インストールは完了です。

![ログイン](/assets/images/figure2.png)


# 備考
* Proxy使用時には各サーバーの/etc/environmentのProxy設定を書き換えます

