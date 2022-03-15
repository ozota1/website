---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: Infra Guide
permalink: /docs/how-to/infra/
sidebar:
  nav: "docs"
---

## 概要

KAMONOHASHI のシステム運用について以下を説明します。
* [バックアップとリストア](/docs/how-to/infra/#バックアップとリストア)
* [認証情報の更新](/docs/how-to/infra/#認証情報の更新)


## バックアップとリストア

KAMONOHASHIのバックアップ・リストアには、
* マシン・Linux OSのバックアップ・リストア
* KAMONOHASHIのアプリレベルのバックアップ・リストア

の２種類の方法があります。
この内、KAMONOHASHIのアプリレベルのバックアップ・リストアについて解説します。
マシン・Linux OSのバックアップ・リストアについてはご自身の環境に合わせて、
仮想マシンのバックアップリストアやdumpコマンドの利用を検討してください

### バックアップ対象と設定方法
バックアップの対象は次の２つになります
* KAMONOHASHIのデータベース
* KAMONOHASHIで利用しているNFSストレージ


### データベース

#### データベースのバックアップと設定
* KAMONOHASHIマシンの/var/lib/kamonohashi/postgresql/backupにダンプファイルが生成されます
* kqi-backup-<日付>.sqlというファイル名になります
* デフォルトでは毎週日曜日01:00に生成され、1世代のみ保管されます
* 世代数と取得日付を変更可能です

##### バックアップ設定を変更する

* Kubernetes Masterマシンにrootでログインします。
* /var/lib/kamonohashi/deploy-tools/<version>/kamonohashi/conf/settings.ymlを開きます
* BackupPostgresTimerOptions__WeeklyTimeSchedule, BackupPostgresTimerOptions__MaxNumberOfBackupFilesのコメントアウトを外し、編集します

|プロパティ名|解説　　　　　|設定例|
|---|-------------|---|
|BackupPostgresTimerOptions__WeeklyTimeSchedule|取得日を指定します。曜日=時刻のフォーマットで記載します。曜日は英語表記の先頭3文字で指定します。<br>;区切りで複数日時を指定可能です|"Sun=01:00:00;Mon=01:00:00"|
|BackupPostgresTimerOptions__MaxNumberOfBackupFiles|保管する世代数です。指定した世代数を超える古いファイルは削除されます<br>|"2"|

* 修正例は次になります。インデントをDeployOptionsとそろえてください

* before

```
appsettings:
  DeployOptions__GpuNodes: ...
  ...
#   BackupPostgresTimerOptions__WeeklyTimeSchedule: "Sun=01:00:00"
#   BackupPostgresTimerOptions__MaxNumberOfBackupFiles: "1"
```

* after

```
appsettings:
  DeployOptions__GpuNodes: ...
  ...
  BackupPostgresTimerOptions__WeeklyTimeSchedule: "Sun=01:00:00;Mon=01:00:00"
  BackupPostgresTimerOptions__MaxNumberOfBackupFiles: "2"
```

#### データベースの復元
##### Kubernetes Masterマシンでの作業
* Kubernetes Masterマシンにrootでログインします。
* 次を実行し、KAMONOHASHI を停止します
`/var/lib/kamonohashi/deploy-tools/deploy-kamonohashi.sh clean app`

##### KAMONOHASHIマシンでの作業
* KAMONOHASHIマシンにrootでログインします
* `/var/lib/kamonohashi/postgresql/backup/`に復元したいデータベースのダンプファイル(kqi-backup-<日付>.sql)を配置してください
* 次のコマンドを実行し、ダンプファイル名を変数に設定します

```
DUMP_FILE=kqi-backup-<日付>.sql
```

* 既存のデータベースのデータを別の場所に退避します

```
mv /var/lib/kamonohashi/postgresql/data /var/tmp/
```

* リストアコマンドを実行します

```
docker run -d --name restore-postgres -e POSTGRES_HOST_AUTH_METHOD=trust -v /var/lib/kamonohashi/postgresql/data/:/var/lib/postgresql/data postgres:10
docker cp /var/lib/kamonohashi/postgresql/backup/$DUMP_FILE restore-postgres:/$DUMP_FILE
docker exec -it restore-postgres psql -U postgres -f /$DUMP_FILE
```

* 完了後に次のコマンドを実施します

```
docker stop restore-postgres
docker rm restore-postgres
```

##### Kubernetes Masterマシンでの作業
* Kubernetes Masterマシンにrootでログインします。
* 次を実行し、KAMONOHASHI を起動します
`/var/lib/kamonohashi/deploy-tools/deploy-kamonohashi.sh deploy app`


### NFSストレージ
* NFSの中身をrsyncやバックアップ製品で保護してください
* ベーシッククラスタではstorageの/var/lib/kamonohashi/nfsを保護します
* 保護した方法に合わせてリストアしてください


## 認証情報の更新

* v4.x 以降のデプロイツールに認証情報更新用のコマンドを用意しています。
* このコマンドで、更新可能な認証情報は以下になります。
  * DB のパスワード
  * MinIO ストレージのパスワード
    * ※自動構築されたオブジェクトストレージを利用している場合のみ更新されます。

* KAMONOHASHI の admin ユーザのパスワードについては、KAMONOHASHI のユーザ管理画面から変更をしてください。

### DB のパスワード更新

* root ユーザで次の DB の認証情報更新用コマンドを実行します。

```
cd /var/lib/kamonohashi/deploy-tools
./deploy-kamonohashi.sh credentials db
```

* 「DB Passwordを入力」と表示されるため、新たに設定するパスワードを入力してください。

### MinIO ストレージのパスワード更新

* root ユーザで次の MinIO ストレージの認証情報更新用コマンドを実行します。

```
cd /var/lib/kamonohashi/deploy-tools
./deploy-kamonohashi.sh credentials storage
```

* 「新しいStorage Secret Keyを入力」と表示されるため、新たに設定するパスワードを入力してください。
* 新たに設定したパスワードを、KAMONOHASHI のストレージ管理画面から該当ストレージの「シークレットキー」を更新してください。

### KAMONOHASHI の各パスワード更新

* DB と MinIO ストレージのパスワードの両方を更新するため、次の更新用コマンドを実行します。

```
cd /var/lib/kamonohashi/deploy-tools
./deploy-kamonohashi.sh credentials all
```

* 「DB Passwordを入力」と表示されるため、新たに設定するパスワードを入力してください。
* 「新しいStorage Secret Keyを入力」と表示されるため、新たに設定するパスワードを入力してください。
* 新たに設定したパスワードを、KAMONOHASHI のストレージ管理画面から該当ストレージの「シークレットキー」を更新してください。

