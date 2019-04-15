---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: CLI Guide
permalink: /docs/how-to/cli/
sidebar:
  nav: "docs"
---
# 本書の目的

本書では、KAMONOHASHIを使用する人物を対象として、KAMONOHASHIのコマンドラインツールをインストールして使用するまでの手順を説明する事を目的としている。

# 前提

読者がLinux、Windows の基本的な知識を持つことを前提としている。

また、CLIの動作確認済み環境を下表に示す。
OSには 動作済みの Python バージョンおよび pip がインストールされており、パスが通っていること。

|項目  |バージョン  |
|---|---|
|OS  |Ubuntu 16.04、Windows 10  |
|Python  |3.5以上  |

# CLIの使用方法

## CLIインストール

### インストールコマンド
端末のコンソールから以下のコマンドを実行する。

```bash
> pip install kamonohashi-cli
```

### 初期設定
CLIを利用するために、KAMONOHASHI にログインする。
```bash
> kqi account login
```

上記コマンドを実行すると、サーバー名、ユーザー名、パスワードの入力プロンプトが表示される。必要な情報を入力し、ログインすると
以降必要な設定情報が書き込まれて CLI が利用可能となる。サーバー名等の情報は管理者より提供される。

## CLI使用方法
KAMONOHASHIのコマンドは
```bash
> kqi <サブコマンド> <操作> オプション
```
という構成になっている。
コマンドの使用方法はヘルプオプション(-h)で確認できる。

```bash
> kqi -h
```

下表に一覧と概要を示す。

|コマンド　|説明　|
|---|---|
|kqi account |アカウントに関連するコマンド|
|kqi data|データの登録・更新・削除などを行うコマンド|
|kqi dataset|データセットの登録・更新・削除などを行うコマンド|
|kqi inference|推論の登録・更新・削除などを行うコマンド|
|kqi preprocessing|前処理情報の登録・更新・削除などを行うコマンド|
|kqi training|学習の作成・実行・停止などを行うコマンド|

また、各コマンドについても詳細をヘルプオプション(-h)で確認できる。
training コマンドの使用方法を確認している例を以下に挙げる。

```bash
> kqi training -h
```

コマンドの引数についても同様にヘルプオプションで(-h)確認できる。

```bash
> kqi training create -h
```