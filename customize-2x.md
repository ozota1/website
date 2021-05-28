---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: "カスタマイズ設定ガイド"
permalink: /docs/install-and-update/customize-3x
sidebar:
  nav: "docs"
---

## インストールするGPUドライババージョンの指定
* `deepops/config/settings.yml`に`nvidia_driver_ubuntu_branch: <version>`のフォーマットで記載します
* 460の場合の例
```
nvidia_driver_ubuntu_branch: "460"
```

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

## 自動構築のオブジェクトストレージではなく既存のものを使用する
* オブジェクトストレージとNFSは同期されている必要があります。  
NFSに書き込んだファイルはオブジェクトストレージからアクセスできる必要があります。逆も同様です。

次の設定ファイル編集を行います

#### 1. `deepops/config/inventry`のkube-nodeの項目からオブジェクトストレージに指定したマシンを削除します
* 編集前
```
[kube-node]
gpu-node1
object-storage
gpu-node2
```
* 編集後
```
[kube-node]
gpu-node1
gpu-node2
```

#### 2.  `kamonohashi/conf/settings.yml`の`minio_deploy`を`false`に指定します

```yaml
minio_deploy: false
```

#### 3. 構築完了後、kamonohashiのweb画面のストレージ設定画面から、オブジェクトストレージの設定を変更します

## insecure-registryを設定する
`deepops/config/group_vars/k8s-cluster.yml`に次の記述を追記します
```
docker_insecure_registries: ["<host名:ポート>"]
```



