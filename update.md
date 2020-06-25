---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: "KAMONOHASHI 2.xのバージョンアップ"
permalink: /docs/install-and-update/update
sidebar:
  nav: "docs"
---

## バージョンアップ
バージョン2.0.0以降の手順になります。バージョン1.xの場合は[以前のバージョン](https://kamonohashi.ai/docs/supported-doc-versions)から確認してください

### KAMONOHASHI Webアプリのみのバージョンアップ

デプロイツールの準備を実施後に次を実施してください

```bash
cd /var/lib/kamonohashi/deploy-tools
git fetch --tags
git checkout tags/<デプロイツールバージョン>
./deploy-kamonohashi.sh update app
```

* デプロイツールバージョンは<アプリのバージョン>.<デプロイツールリビジョン>となってます
  * デプロイツールバージョンが2.0.0.nの場合、アプリバージョン2.0.0をデプロイするツールのn番目のリビジョンです
  * 古いアプリバージョンに戻す場合は[バージョンダウン](#バージョンダウン)を確認してください

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
./deploy-kamonohashi.sh clean all
```

*　新しいバージョンのデプロイツールでインストールを実行
  * 詳細はインストールの項目を参照
  * パスワードは初期構築時と同じものを指定してください
  
```
git fetch --tags
git checkout --recurse-submodules tags/<バージョン>
./deploy-kamonohashi.sh deploy all
```

## バージョンダウン
### DBの切り戻しを含むバージョンダウン
2.0.0から2.1.0以降へのバージョンアップには、DBのテーブル変更が含まれています。
そのため以前のバージョンに戻す際には、下記手順によりDBの切り戻し作業を実施する必要があります(作業中はKAMONOHASHIのサービスが停止します)。
なおこの作業を行った場合、マイグレーション適用後にのみ存在しているテーブルおよびカラムに格納されていたデータは削除されるため注意してください。

|version|Migration|主な変更|
|---|---|---|
|v2.0.0|20200319064613_v2.0.0|v2.x初期状態|
|v2.1.0|20200615001935_v2.1.0|Tensorboard生存期間、ノートブックの実行コマンド、学習へのタグ付け、データセットローカルコピーに関するカラム追加|

1. `/var/lib/kamonohashi/deploy-tools/rollback/rollback.sh`を実行します。
2. (v2.1.0からv2.0.0に戻す場合)デプロイされているKAMONOHASHIのバージョンに`2.1.0`、戻したい時点のMigrationファイルに`20200319064613_v2.0.0`を入力します。
3. DBの切り戻し処理が終了するまで数分間待機します。
4. 切り戻し処理終了後、再デプロイ可能なバージョンの一覧が表示されるため、戻したいバージョンを指定します。
5. KAMONOHASHIのWEB画面にアクセスし、バージョン情報より、バージョンが戻っていることを確認します。

## 外部サービスとの互換性

動作を確認した環境は以下の通りです。

|KAMONOHASHI|GitLab|MinIO| LDAP|Kubernetes |Ubuntu |
|---|---|---|---|---|---|
|v2.0.0|11.8以降|RELEASE.2020-03-09T18-26-53Z|version 3| v1.15.3 |Ubuntu 18.04|
|v1.0.1 ~ v1.1.7|11.8以降|RELEASE.2019-01-23T23-18-58Z|version 3| v1.12.7,v1.14.1|ubuntu 16.04|
|v1.0.0|11.7以前|RELEASE.2019-01-23T23-18-58Z|version 3| v1.12.7|Ubuntu 16.04|

v1.0.0では11.8以降のGitLabに対応していませんので注意してください。