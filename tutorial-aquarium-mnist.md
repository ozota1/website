---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: "アクアリウム mnist Tutorial"
permalink: /docs/tutorial/aquarium-mnist
sidebar:
  nav: "docs"
---

(準備中)

## はじめる前に

このチュートリアルを始める前に、アクアリウムにログインできることを確認してください。

## 概要

本チュートリアルでは、アクアリウムを使用する方法について簡単に説明します。
具体的にはアクアリウムを使用して、機械学習手法を試す際によく用いられるmnistの画像分類モデルを学習させる方法を以下の手順に沿って説明します。

1.  データセットを作成する
1.  テンプレートを作成する    
1.  実験を実行する
1.  ステータスを確認する
1.  TensorBoard で学習の状況を表示する

## データセットを作成する

## テンプレートを登録する

テンプレートはAI開発者が登録します。データ保持者は、AI開発者にテンプレートの登録を依頼してください。

### 前処理なしテンプレートを登録する

テンプレートの登録画面にて、次のように入力します。

* Step 1

|項目|入力値|
|-----|-----|
|テンプレート名|(任意)|
|説明文|(任意)|
|公開設定|現在のテナント|

* Step 2

入力しない

* Step 3

|項目|入力値|
|-----|-----|
|レジストリ|official-docker-hub|
|イメージ|tensorflow/tensorflow|
|タグ|2.1.0-gpu-py3|
|token|(空)|
|Gitサーバ|GitHub|
|リポジトリ|KAMONOHASHI/aquarium-tutorial|
|ブランチ|master|
|コミットID|HEAD|
|token|(空)|
|CPU|4|
|メモリ(GB)|8|
|GPU|1|

実行コマンド
```shell
pip install --upgrade pip && pip install scikit-image scikit-learn pandas tqdm
unzip /kqi/input/*/*.zip -d /kqi/input/images
python -u train.py
```

### 前処理ありテンプレートを登録する

テンプレートの登録画面にて、次のように入力します。

* Step 1

|項目|入力値|
|-----|-----|
|テンプレート名|(任意)|
|説明文|(任意)|
|公開設定|現在のテナント|

* Step 2

|項目|入力値|
|-----|-----|
|レジストリ|official-docker-hub|
|イメージ|tensorflow/tensorflow|
|タグ|2.1.0-gpu-py3|
|token|(空)|
|Gitサーバ|GitHub|
|リポジトリ|KAMONOHASHI/aquarium-tutorial|
|ブランチ|master|
|コミットID|HEAD|
|token|(空)|
|CPU|4|
|メモリ(GB)|8|
|GPU|0|

実行コマンド
```shell
pip install --upgrade pip && pip install scikit-image scikit-learn pandas tqdm
unzip /kqi/input/*/*.zip -d /kqi/input/images
python -u preprocess.py
```


* Step 3

|項目|入力値|
|-----|-----|
|レジストリ|official-docker-hub|
|イメージ|tensorflow/tensorflow|
|タグ|2.1.0-gpu-py3|
|token|(空)|
|Gitサーバ|GitHub|
|リポジトリ|KAMONOHASHI/aquarium-tutorial|
|ブランチ|master|
|コミットID|HEAD|
|token|(空)|
|CPU|4|
|メモリ(GB)|8|
|GPU|1|

実行コマンド
```shell
pip install --upgrade pip && pip install scikit-image scikit-learn pandas tqdm
python -u train_after_preprocess.py --input_path /kqi/parent/
```


## 実験を開始する

実験を開始するには、新規実験画面からこのチュートリアル用に登録したテンプレートを選択します。

続いて、次のように入力します。

|項目|入力値|
|-----|-----|
|実験名|(任意)|
|データセットの選択|本チュートリアル用に用意したデータセット|
|テンプレートバージョン|1|

「実験を開始する」を選択すると、実験が開始されます。

## 学習の状況を確認する

### ステータスを確認する

実験一覧画面から実験のステータスを確認できます。ステータスは「実行準備中」「実行中」「完了」「異常終了」のいずれかです。

### Tensorboard で学習の状況を確認する

実験一覧画面からチュートリアルの実験を選択し、実験詳細画面に移動します。
この画面で「詳細をグラフで見る」を選択すると、Tensorboardの準備が開始されます。
準備が完了すれば「グラフを開く」が表示されるので選択してください。Tensorboardが表示されます。
