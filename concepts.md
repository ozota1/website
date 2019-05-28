---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: "Concepts"
permalink: /docs/concepts
sidebar:
  nav: "docs"
---

本書では、KAMONOHASHIを使用する前の読者を対象として、KAMONOHASHIのなかで登場する概念や用語について解説し、
読者がスムーズにKAMONOHASHIを使用開始できるようにすることを目的としている。

## 環境構成
KAMONOHASHIはローカル環境、外部サービスの環境と連携して利用します。

| |説明|
|:--|:--|
|ローカル環境|開発者自身が作業する環境です。NVIDIA GPUを搭載したマシンも使用することが可能です。モデルのソースコード、データ、実行コマンドなどを用意します。|
|外部サービス環境|GitやDockerなど、外部のサービスです。KAMONOHASHIには含まれません。個別に用意が必要です。|

![構成概念図](/assets/images/illust_userFlow.png)

## テナントとは？

テナントはKAMONOHASHIの管理単位です。テナント毎に、ユーザの管理、クォータ(リソースの使用量制限)の設定が行えます。<br>
テナントには、Gitリポジトリ、Docker Registryおよびオブジェクトストレージが関連付けられ、それぞれ複数のテナント間で共有可能です。
Gitリポジトリ、Docker Registryは1テナントに複数登録することができます。

初期状態では、「Sandbox」という名前のテナントが既定で用意されています。

![テナント概念(図3-1)](/assets/images/illust_tenant.png)

## ノードとは？

ノードは、KAMONOHASHIに管理される物理的あるいは仮想的な計算用コンピュータを指します。
KAMONOHASHI自体を構成する管理サーバ群は含まれません。


![ノード概念](/assets/images/node.png)



## ロールとは？
ユーザにロールを付与することで、操作可能な機能を制御できます。
一人のユーザには複数のロールを関連付けられます。

ロールは大きく以下の2種類に大別されます。

* システムロール
    * テナントを横断する、システム全体に関連した情報を取り扱うためのロール
    * 既定では全てのシステム管理権限がある「Adminロール」が使用可能
* テナントロール
    * 特定のテナント内に限定された情報を扱うためのロール
    * 所属するテナントごとに、ユーザに付与するテナントロールを指定する
        * 例えば、同一のユーザに対し、AテナントにはManagerロールを付与し、BテナントにはUserロールとResercherロールを付与する、など
    * 既定では以下の3種類のテナントロールが使用できる

|ロール  |説明  |
|---|---|
|User  |データの追加・削除、およびデータセットの作成・変更を行う権限を持つ。  |
|Researcher  |学習の実行、結果の評価を行う権限を持つ。  |
|Manager  |そのテナント内で使用する計算リソースや権限の管理を行う権限を持つ。  |

