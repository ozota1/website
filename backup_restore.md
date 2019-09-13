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
* /var/lib/kamonohashi/postgresql/backupにダンプファイルが生成されます
* kqi-backup-<日付>.sqlというファイル名になります
* デフォルトでは毎週日曜日01:00に生成され、1世代のみ保管されます
* 世代数と取得日付を変更可能です

##### バックアップ設定を変更する

* Kubernetes Masterマシンにrootでログインします。
* /var/lib/kamonohashi/deploy-tools/<version>/kamonohashi/conf/settings.ymlを開きます
* BackupPostgresTimerOptions__WeeklyTimeSchedule, BackupPostgresTimerOptions__MaxNumberOfBackupFilesのコメントアウトを外し、編集します

|プロパティ名|解説　　　　　|設定例|
|---|-------------|---|
|BackupPostgresTimerOptions__WeeklyTimeSchedule|取得日を指定します。曜日=時刻のフォーマットで記載します。<br>;区切りで複数日時を指定可能です|"Sun=01:00:00;Mon=01:00:00"|
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
* 次を実行し、kamonohashiを停止します
`/var/lib/kamonohashi/deploy-tools/<version>/kamonohashi/deploy-kqi.sh clean`

##### KAMONOHASHIマシンでの作業
* KAMONOHASHIマシンにrootでログインします
* `/var/lib/kamonohashi/postgresql/backup/`に復元したいデータベースのダンプファイル(kqi-backup-<日付>.sql)を配置してください
* 次のコマンドを実行します
```
mv /var/lib/kamonohashi/postgres/data /var/tmp/
docker run -d --name restore-postgres -v /var/lib/kamonohashi/postgresql/data/:/var/lib/postgresql/data postgres:10
docker cp /var/lib/kamonohashi/postgresql/backup/kqi-backup-20190908.sql restore-postgres:/kqi-backup-20190908.sql
docker exec -it restore-postgres psql -U postgres -f /kqi-backup-20190908.sql
docker stop restore-postgres
docker rm restore-postgres
```

##### Kubernetes Masterマシンでの作業
* Kubernetes Masterマシンにrootでログインします。
* 次を実行し、kamonohashiを起動します
`/var/lib/kamonohashi/deploy-tools/<version>/kamonohashi/deploy-kqi.sh deploy`


### NFSストレージ
* NFSの中身をrsyncやバックアップ製品で保護してください
* ベーシッククラスタではstorageの/var/lib/kamonohashi/nfsを保護します
* 保護した方法に合わせてリストアしてください
