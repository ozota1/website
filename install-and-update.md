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

 - <a href="/docs/install-and-update/deploy-single-node">ノード構成でのインストール</a>
 - <a href="/docs/install-and-update/deploy-cluster">クラスタ構成でのインストール</a>
- <a href="/docs/install-and-update/customize-2x">カスタマイズしたクラスタ構成でのインストール</a>

* ベーシッククラスタの構成では要件が足りず、カスタマイズしたい場合は[kamonohashi-support@jp.nssol.nipponsteel.com]にお問い合わせください


## アンインストール方法
* `./deploy-kamonohashi.sh clean all`を実行するとソフトウェアがアンインストールされます。
  * このコマンドではKAMONOHASHIの内部データ(データベース, ストレージのデータ)は削除しません
    * adminパスワードも保存されたままです
  * 再度デプロイすると過去のデータベース, ストレージの中身を引き続き使用します
  * 完全にデータを削除する場合は KAMONOHASHIノード, STORAGEノードで`/var/lib/kamonohashi` を削除してください
    * 構築に失敗してやり直す際にパスワードも変更する場合はこのディレクトリを削除してください
    
## バージョンアップ
  - <a href="/docs/install-and-update/migrate1xto2x">KAMONOHASHI 1.xから2.xへの移行</a>


## 外部サービスとの互換性

動作を確認した環境は以下の通りです。

|KAMONOHASHI|GitLab|MinIO| LDAP|Kubernetes |Ubuntu |
|---|---|---|---|---|---|
|v2.0.0|11.8以降|RELEASE.2019-01-23T23-18-58Z|version 3| v1.12.7,v1.14.1|Ubuntu 18.04|
|v1.0.1, v1.1.7|11.8以降|RELEASE.2019-01-23T23-18-58Z|version 3| v1.12.7,v1.14.1|ubuntu 16.04|
|v1.0.0|11.7以前|RELEASE.2019-01-23T23-18-58Z|version 3| v1.12.7|Ubuntu 16.04|

v1.0.0では11.8以降のGitLabに対応していませんので注意してください。


