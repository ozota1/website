---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: "カスタマイズ設定ガイド"
permalink: /docs/install-and-update/customize-2x
sidebar:
  nav: "docs"
---

## 自動構築のNFSサーバーでなく既存のNFSサーバーを使用する
例えばHWベンダーのNFSサーバーを使用する場合にこのカスタマイズを行います。
NFSサーバーはrootユーザーでの読み書きが可能であることが要件です

#### 1. 次の`deepops/config/inventry`の記述を削除します

```
[nfs-server]
${NFS}
```

#### 2. `kamonohashi/conf/settings.yml`の次の箇所を編集します

```yaml
appsettings:
...
  DeployOptions__NfsStorage: "storage"
  DeployOptions__NfsPath: "/var/lib/kamonohashi/nfs"
```

|yamlの項目|内容|
|---|---|
|appsettings.DeployOptions__NfsStorage|NFSサーバーのホスト名|
|appsettings.DeployOptions__NfsPath|NFSサーバーのエクスポートパス|

## insecure-registryを設定する
`deepops/config/group_vars/k8s-cluster.yml`に次の記述を追記します
```
docker_insecure_registries: ["<host名:ポート>"]
```
