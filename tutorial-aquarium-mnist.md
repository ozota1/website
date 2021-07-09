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

## はじめる前に

このチュートリアルを始める前に、アクアリウムにログインできることを確認してください。

## 概要

本チュートリアルでは、アクアリウムを使用する方法について簡単に説明します。
具体的にはアクアリウムを使用して、機械学習手法を試す際によく用いられるmnistの画像分類モデルを学習させる方法を以下の手順に沿って説明します。

1.  テンプレートを作成する
1.  データセットを作成する
1.  実験を実行する
1.  学習ステータスを確認する
1.  TensorBoard で学習の状況を表示する
1.  推論を実行する
1.  推論ステータスを確認する
1.  TensorBoard で推論の状況を表示する

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
|タグ|2.5.0-gpu|
|token|(空)|
|Gitサーバ|GitHub|
|リポジトリ|KAMONOHASHI/aquarium_tutorial|
|ブランチ|main|
|コミットID|(空)|
|token|(空)|
|CPU|2|
|メモリ(GB)|8|
|GPU|1|

実行コマンド
```shell
pip install -r requirements.txt
unzip /kqi/input/*/*.zip -d /kqi/input/images
python -u train.py --input_type chinese
```

* Step 4

|項目|入力値|
|-----|-----|
|レジストリ|official-docker-hub|
|イメージ|tensorflow/tensorflow|
|タグ|2.5.0-gpu|
|token|(空)|
|Gitサーバ|GitHub|
|リポジトリ|KAMONOHASHI/aquarium_tutorial|
|ブランチ|main|
|コミットID|(空)|
|token|(空)|
|CPU|2|
|メモリ(GB)|8|
|GPU|1|

実行コマンド
```shell
pip install -r requirements.txt
unzip /kqi/input/*/*.zip -d /kqi/input/images
python -u inference.py
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
|タグ|2.5.0-gpu|
|token|(空)|
|Gitサーバ|GitHub|
|リポジトリ|KAMONOHASHI/aquarium_tutorial|
|ブランチ|main|
|コミットID|(空)|
|token|(空)|
|CPU|2|
|メモリ(GB)|8|
|GPU|0|

実行コマンド
```shell
pip install -r requirements.txt
unzip /kqi/input/*/*.zip -d /kqi/input/images
python -u preprocess.py --input_type chinese
```


* Step 3

|項目|入力値|
|-----|-----|
|レジストリ|official-docker-hub|
|イメージ|tensorflow/tensorflow|
|タグ|2.5.0-gpu|
|token|(空)|
|Gitサーバ|GitHub|
|リポジトリ|KAMONOHASHI/aquarium_tutorial|
|ブランチ|main|
|コミットID|(空)|
|token|(空)|
|CPU|2|
|メモリ(GB)|8|
|GPU|1|

実行コマンド
```shell
pip install -r requirements.txt
python -u train_after_preprocess.py
```

* Step 4

|項目|入力値|
|-----|-----|
|レジストリ|official-docker-hub|
|イメージ|tensorflow/tensorflow|
|タグ|2.5.0-gpu|
|token|(空)|
|Gitサーバ|GitHub|
|リポジトリ|KAMONOHASHI/aquarium_tutorial|
|ブランチ|main|
|コミットID|(空)|
|token|(空)|
|CPU|2|
|メモリ(GB)|8|
|GPU|1|

実行コマンド
```shell
pip install -r requirements.txt
unzip /kqi/input/*/*.zip -d /kqi/input/images
python -u inference.py
```


## データセットを作成する

この実験では、Chinese MNISTと呼ばれているデータセットを使用します。

1. [Chinese MNIST](https://www.kaggle.com/gpreda/chinese-mnist)からzipファイルになっているデータセットをダウンロードします。
2. ダウンロードしたzipファイルを展開します。
3. 展開したファイル群から、data/data/にある画像ファイルだけを取り出し、学習用と推論用に分割します。
分け方は任意ですが、推論用が数十枚もあればお試し用としては十分です。
一例としては、上記ディレクトリのinput_1_1_1.jpgからinput_1_1_15.jpgまでの15枚を推論用とし、残りを学習用とします。
4. 学習用と推論用のそれぞれのファイルを別のzipファイルとして圧縮します。
これらの新たなzipファイルは、展開したときにフォルダが作成されないように、つまりルート直下に画像ファイルがあるようにします。
4. これらの新たなzipファイルをアクアリウムの別のデータセットとしてアップロードします。
   データセット一覧画面から「新しいデータセット」を選択して、次のように入力します。
   
|種類　|説明　|
|---|---|
|データセット名|任意。学習用なのか推論用なのか区別できるような名前を付ける。|
|画像|新たなzipファイルのファイル名。|


## 実験を開始する

実験を開始するには、新規実験画面からこのチュートリアル用に登録したテンプレートのいずれかを選択します。

続いて、次のように入力します。

|項目|入力値|
|-----|-----|
|実験名|(任意)|
|データセットの選択|学習用に用意したデータセット|
|テンプレートバージョン|1|

「実験を開始する」を選択すると、実験が開始されます。

## 学習の状況を確認する

### 学習ステータスを確認する

実験一覧画面から実験のステータスを確認できます。ステータスは「実行準備中」「実行中」「完了」「異常終了」のいずれかです。

### Tensorboard で学習の状況を確認する

実験一覧画面からチュートリアルの実験を選択し、実験詳細画面に移動します。
この画面で「詳細をグラフで見る」を選択すると、Tensorboardの準備が開始されます。
準備が完了すれば「グラフを開く」が表示されるので選択してください。Tensorboardが表示されます。

## 推論を実行する

学習が完了すれば、その結果を用いて推論を実行することができます。
推論を実行するには、その実験の詳細画面で推論タブを選択し、推論一覧を表示します。
この画面で「推論を実行」を選択し、次のように入力します。

|項目|入力値|
|-----|-----|
|名前|(任意)|
|データセットの選択|推論用に用意したデータセット|
|テンプレートバージョン|1|

「実行」を選択すると、推論が開始されます。

### 推論ステータスを確認する

推論一覧画面から推論のステータスを確認できます。ステータスは「実行準備中」「実行中」「完了」「異常終了」のいずれかです。

### Tensorboard で推論の状況を確認する

推論一覧画面で「詳細をグラフで見る」を選択すると、Tensorboardの準備が開始されます。
準備が完了すれば「グラフを開く」が表示されるので選択してください。Tensorboardが表示されます。
