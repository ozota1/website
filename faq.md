---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: FAQ
permalink: /docs/faq
sidebar:
  nav: "faq"
---

FAQではよくある質問とそれに対する答えが読めます。



## 一般的な質問
#### ■KAMONOHASHIの由来は何ですか？

 
 KAMONOHASHIは動物の「カモノハシ」からとりました。ご存じのようにカモノハシは、哺乳類なのに卵を産んだり、爬虫類のように毒を持っていたり、ヘラの部分で電気信号を感知できたりと、すごくおもしろい動物です。私たちのKAMONOHASHIは多くの人たちの協力を得て開発してきましたので「多様性」という意味と、「分類が難しいものを見分ける画像認識アプリケーション」という意味を込めてこの名前にしました。敢えて、日本語の名詞を英語表記にし、「日本発の世界共通の技術を作っていきたい」といった思いも込めています。


## 利用について

#### ■対応OSやブラウザを教えてください

KAMONOHASHIの動作確認済み環境を下表に示します。

|項目  |バージョン  |
|---|---|
|OS  |Ubuntu 16.04、Windows 10  |
|ブラウザ  |Chrome 最新版, Firefox 最新版  |

## インストールについて

#### ■まずは何をすればよいですか？
 
  1. [インストールガイド](/docs/install-and-update)を参考に、マシンの準備とKAMONOHASHIのインストールを行ってください。
  1. [チュートリアル](/docs/install-and-update)を一通りやってみてください。
  1. [ほかのドキュメント](/docs/)にも目を通してみてください。何か問題がおきたら[サポートに質問](/support/)してみましょう。



#### ■GitLabなどの外部サービスのバージョンはどれを使えばよいですか？

   動作を確認した環境は以下の通りです。

  |KAMONOHASHI|GitLab|MinIO| LDAP|Kubernetes |
  |---|---|---|---|---|
  |v1.0.1, v1.0.2|11.8以降|RELEASE.2019-01-23T23-18-58Z|version 3| v1.12.7,v1.14.1|
  |v1.0.0|11.7以前|RELEASE.2019-01-23T23-18-58Z|version 3| v1.12.7|

  v1.0.0では11.
  
#### ■DNSがない場合はどうすればよいですか？

構築に用いる各マシンの「/etc/hosts」で名前解決をするように編集してください。

その場合にKAMONOHASHI構築後の既知の問題として、kamonohashiからストレージの名前解決ができないというものがあります。
ワークアラウンドとして、
[Admin Guide](/docs/how-to/admin/)
の[ストレージの項目](/docs/how-to/admin/#ストレージ情報の新規登録)の「サーバ名URL」が「storage:9000」になっているところを
「<IP>:9000」に変更してください。


#### ■KAMONOHASHIをインストールすると各マシンにどのようなソフトウェアがインストールされますか？
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
|Ansible	 |構成管理ツール	|kubernetesの構築で利用するkubesprayが、本構成管理ツールを利用するため導入します。最新バージョンを利用します。  |
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



## システム運用全般について

#### ログの場所を教えてください

| ログ種別 | マシン | パス |
|---|---|---|
| 構築ツール | Kubernetes Master | `/var/log/kamonohashi/kubespray.log`|
| KAMONOHASHI | KAMONOHASHIマシン | `/var/log/kamonohashi/platypus-web-api/`配下の各ファイル |


## チームへの連絡

#### バグを発見した場合や要望がある場合どこへ連絡したらよいですか？

 修正・改善要望は[GitHubのIssue](https://github.com/KAMONOHASHI/kamonohashi/issues)にて受け付けています。[Issueの書き方](https://github.com/KAMONOHASHI/kamonohashi/wiki/Submitting-Bugs-and-Suggestions)を参考に提出してください。

#### その他質問がある場合どこへ連絡したらよいですか？
 [サポートページ](/support/)よりご連絡をお願いします。


