---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: FAQ
permalink: /docs/faq-detail/install
sidebar:
  nav: "faq"
---

こちらでは導入、インストールに関するよくある質問とそれに対する答えが読めます。



## 導入サポートを頼みたい
KAMONOHASHI導入支援を有償にて行っております。
詳しくは[サポートページ](/support/)をご覧ください。

## 自分でインストールする場合まずは何をすればよいですか
 
  1. [インストールガイド](/docs/install-and-update)を参考に、マシンの準備とKAMONOHASHIのインストールを行ってください。
  1. [チュートリアル](/docs/install-and-update)を一通りやってみてください。
  1. [ほかのドキュメント](/docs/)にも目を通してみてください。何か問題がおきたら[サポートに質問](/support/)してみましょう。



## 外部サービスのバージョンはどれを使えばよいですか

インストールガイドの[外部サービスとの互換性](/docs/install-and-update#外部サービスとの互換性)から確認することができます。


  
## 名前解決をしていない場合はどうすればよいですか

DNSがない場合、構築に用いる各マシンの「/etc/hosts」で名前解決をするように編集してください。

その場合にKAMONOHASHI構築後の既知の問題として、kamonohashiからストレージの名前解決ができないというものがあります。
ワークアラウンドとして、
[Admin Guide](/docs/how-to/admin/)
の[ストレージの項目](/docs/how-to/admin/#ストレージ情報の新規登録)の「サーバ名URL」が「storage:9000」になっているところを
「<IP>:9000」に変更してください。


## 各マシンにどのようなソフトウェアがインストールされますか
各マシンに配置されるソフトウェアの概要は図のようになっています。
![ソフトウェア配置図](\assets\images\kqi_sw.png)


![ソフトウェア配置図2](\assets\images\kqi_sw2.png)

コンテナ以外のソフトウェアの詳細は以下の通りです。

**KAMONOHASHIシステム管理サーバ**

| 導入SW・パッケージ|SW・パッケージ説明 |導入理由  |
|---|---|---|
|kubernetes worker |コンテナオーケストレーションツール	|KAMONOHASHIを利用する上で必須ツール。kubelet,kube-proxy,weave-net,nginx-proxyなど。 |
|docker	 |コンテナ管理ツール |KAMONOHASHIでコンテナを利用するために導入します。|


**Kubernetesマスターサーバ**

| 導入SW・パッケージ|SW・パッケージ説明 |導入理由  |
|---|---|---|
|Ansible	 |構成管理ツール	|Kubernetesの構築で利用するkubesprayが、本構成管理ツールを利用するため導入します。最新バージョンを利用します。  |
|kubernetes	worker |コンテナオーケストレーションツール|	KAMONOHASHIを利用する上で必須ツール。etcd,kube-apiserver,kube-scheduler,kube-controller-managerweave-net,nvidia-device-plugin-daemonsetなど。 |
|docker		 |コンテナ管理ツール|		KAMONOHASHIでコンテナを利用するために導入します。|
|nfs-common		 |NFSマウントを行うために利用するパッケージ|	KAMONOHASHI環境で共通で導入します。最新バージョンを利用します。|


**GPUサーバ**

| 導入SW・パッケージ|SW・パッケージ説明 |導入理由|
|---|---|---|
|nvidia-docker2 nvidia-container-rutime	 | DockerコンテナでNVDIA社のGPUを利用するためのパッケージ|コンテナでGPUを利用するために導入します。|
|NVIDIA　GPU　Driver	 | OSがGPUを認識するためのパッケージ	|コンテナでGPUを利用するために導入します。本パッケージは下位互換性があることから、最新バージョンを導入します。	|
| kubernetes worker| コンテナオーケストレーションツール | 	KAMONOHASHIを利用する上で必須ツール。kubelet,kube-proxy,weave-net,nginx-proxyなど|
| docker	|コンテナ管理ツール	|KAMONOHASHIでコンテナを利用するために導入します。 |
|nfs-common	|NFSマウントを行うために利用するパッケージ|	KAMONOHASHI環境で共通で導入します。GPUサーバ上のコンテナからNFSサーバをマウントするために利用します。最新バージョンを利用します。   |

**オブジェクトストレージサーバ**

| 導入SW・パッケージ|SW・パッケージ説明 |導入理由  |
|---|---|---|
| minio	| Amazon S3互換のAPIを提供し、オブジェクトストレージにアクセスするツール|	KAMONOHASHIのWebアプリケーションが、NFSサーバにアクセスする際に利用するため導入します。|
|nfs-common	|NFSマウントを行うために利用するパッケージ |	KAMONOHASHI環境で共通で導入します。オブジェクトストレージサーバからNFSサーバをマウントするために利用します。最新バージョンを利用します。|



