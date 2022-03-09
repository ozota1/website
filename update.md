---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: "KAMONOHASHI 3.xのバージョンアップ"
permalink: /docs/install-and-update/update
sidebar:
  nav: "docs"
---

## バージョンアップ
3.xから4.xへのバージョンアップの場合は[4.xへの移行](/docs/install-and-update/migrate3xto4x)を実施してください
2.xを利用している場合は事前に[3.xへの移行](/docs/install-and-update/migrate2xto3x)を完了してください
以下は 3.a　から 3.b へのバージョンアップの方法となります

### KAMONOHASHI Webアプリのみのバージョンアップ

デプロイツールの準備を実施後に次を実施してください

```bash
cd /var/lib/kamonohashi/deploy-tools
git fetch --tags
git checkout tags/<デプロイツールバージョン>
./deploy-kamonohashi.sh update app
```

* デプロイツールバージョンは<アプリのバージョン>.<デプロイツールリビジョン>となってます
  * デプロイツールバージョンが3.1.0.nの場合、アプリバージョン3.1.0をデプロイするツールのn番目のリビジョンです

# バージョンダウン
1.  4.xから3.xへ戻す場合は、4.xをアンインストールして3.xを再インストールしてください
  * 3.xをインストール前に、3.x時点のデータベースのダンプファイルを使用し、[バックアップファイルからリストアのデータベースの復元](/docs/how-to/infra/)を実施してください
2.  3.xから2.xへ戻す場合は、3.xをアンインストールして2.xを再インストールしてください。2.xの再構築でansibleのバージョン不整合エラーが起きる場合は`pip uninstall ansible`を実行してから2.xを再構築してください
3. DBを戻します。次の2通りの方法のどちらか1つを選んでください
  * [バックアップファイルからリストア](/docs/how-to/infra/)します。この場合、バックアップ以降のデータは失われます
  * 次のDBマイグレーションのロールバック手順を実施します。この場合、戻すバージョンに存在しなかったDBのカラムやテーブルの情報が失われます
### DBマイグレーションのロールバック手順
  * `/var/lib/kamonohashi/deploy-tools/rollback/rollback.sh`を実行します
  * 対話形式で設定が聞かれるので、デプロイされているKAMONOHASHIのバージョンを入力してください。
  * マイグレーション名の一覧が出るので、戻したいバージョンのマイグレーション名を記入してください。マイグレーション名は`日付_バージョン`のフォーマットになっています。全てのバージョンにマイグレーションがあるわけではないので、戻したいバージョンのマイグレーションがなければそれ以前のバージョンのマイグレーションを選択してください
  * DBの切り戻し処理が終了するまで数分間待機します。
  * 切り戻し処理終了後、再デプロイ可能なバージョンの一覧が表示されるため、戻したいバージョンを指定します。
  * KAMONOHASHIのWEB画面にアクセスし、バージョン情報より、バージョンが戻っていることを確認します。