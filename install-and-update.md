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

 - [シングルノード構成でのインストール](/docs/install-and-update/deploy-single-node)
 - [クラスタ構成でのインストール](/docs/install-and-update/deploy-cluster)

上記の構成では要件が足りず、カスタマイズしたい場合は[kamonohashi-support@jp.nssol.nipponsteel.com](mailto:kamonohashi-support@jp.nssol.nipponsteel.com)にお問い合わせください


## バージョンアップ
  - <a href="/docs/install-and-update/update">KAMONOHASHI 2.xのバージョンアップ</a>
  - <a href="/docs/install-and-update/migrate1xto2x">KAMONOHASHI 1.xから2.xへの移行</a>

## バージョンダウン
  - <a href="/docs/install-and-update/update#バージョンダウン">バージョンダウン</a>

## 外部サービスとの互換性

動作を確認した環境は以下の通りです。

|KAMONOHASHI|GitLab|MinIO| LDAP|Kubernetes |Ubuntu |
|---|---|---|---|---|---|
|v2.0.0|11.8以降|RELEASE.2020-03-09T18-26-53Z|version 3| v1.15.3 |Ubuntu 18.04|
|v1.0.1 ~ v1.1.7|11.8以降|RELEASE.2019-01-23T23-18-58Z|version 3| v1.12.7,v1.14.1|ubuntu 16.04|
|v1.0.0|11.7以前|RELEASE.2019-01-23T23-18-58Z|version 3| v1.12.7|Ubuntu 16.04|

v1.0.0では11.8以降のGitLabに対応していませんので注意してください。

## アンインストール方法

以下のコマンドを実行するとソフトウェアがアンインストールされます。
```
./deploy-kamonohashi.sh clean all
```

* このコマンドではKAMONOHASHIの内部データ(データベース, ストレージのデータ)は削除しません
* adminパスワードも保存されたままです
* 再度デプロイすると過去のデータベース, ストレージの中身を引き続き使用します
* 完全にデータを削除する場合は構成に応じ次のディレクトリを削除してください
  * シングルノード構成: マシン内の`/var/lib/kamonohashi`
  * クラスタ構成: KAMONOHASHIノード, STORAGEノードの`/var/lib/kamonohashi`
  * 構築に失敗してやり直す際にパスワードも変更する場合はこのディレクトリを削除してください

### アンインストールについての補足事項
* 2.0.0のアンインストールでは、内部で利用するツールの[既知のバグ](https://github.com/kubernetes-sigs/kubespray/issues/5185)により、次のようなエラーがメッセージに含まれます。
これは無視して良いエラーです

```
    The task includes an option with an undefined variable. The error was: 'dict object' has no attribute 'stdout'

    The error appears to have been in '/kubespray/kubespray/roles/remove-node/post-remove/tasks/main.yml': line 14, column 3, but may
    be elsewhere in the file depending on the exact syntax problem.

    The offending line appears to be:


    - name: Set node IP
      ^ here
```


