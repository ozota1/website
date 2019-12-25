---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: "CIFAR-10 Tutorial (pytorch)"
permalink: /docs/tutorial/cifar-10-pytorch
sidebar:
  nav: "docs"
---


## はじめる前に

このチュートリアルを始める前に、以下のKAMONOHASHIのインストールが終わり、KAMONOHASHIにログインできることを確認してください。

(参考) [KAMONOHASHIをインストールする](/docs/install-and-update#インストール方法)


## はじめに


本チュートリアルでは、KAMONOHASHIを使用する方法について簡単に説明します。
具体的にはKAMONOHASHIを使用して、機械学習手法を試す際によく用いられるcifar-10の画像分類モデルを学習させる方法を以下の手順に沿って説明します。


 1. データをアップロードする
 1. データセットを作成する
 1. 学習を実行する
 1. TensorBoardで学習の状況を表示する
 1. 学習のログを確認する

## データをアップロードする
KAMONOHASHIにデータをアップロードする流れを説明します。

### cifar-10データセットをダウンロードする
このチュートリアルでは、[cifar-10データセット](https://www.cs.toronto.edu/~kriz/cifar.html)<i class="material-icons" class="material-icons blue">launch</i>を使用します。cifar10は、カラー画像のデータセットです。
 - 画像サイズは32 x 32px
 - 10クラスの画像がそれぞれ6000枚、計60000枚の画像がある
 - そのうち50000枚が学習データ、10000枚がテストデータ
 - クラスはairplane, automobile, bird, cat, deer, dog, frog, horse, ship, truck

KAMONOHASHIにアクセスできる端末に CIFAR-10 binary version (cifar-10-binary.tar.gz) をダウンロードしてください。 
[cifar-10のデータをダウンロードする](http://www.cs.toronto.edu/~kriz/cifar-10-binary.tar.gz)<i class="material-icons" class="material-icons blue">save_alt</i>

### データを解凍する
このデータセットは解凍すると、

- data_batch_1.bin
- data_batch_2.bin
- data_batch_3.bin
- data_batch_4.bin
- data_batch_5.bin
- test_batch.bin

というファイルに分割されます。それぞれのbinファイルは、32x32x3の画像データが1万枚ずつ含まれています。
画像1枚あたり、先頭1バイトがラベル + 32x32x3バイトが画素情報という構成になっており、これが先頭から1万個結合されているのがbinファイルです。

### データをKAMONOHASHIにアップロードする
データが用意できたらKAMONOHASHIにアップロードしましょう。
[データ管理]を選択し、右上の新規登録ボタンから行います。

|種類　|説明　|
|---|---|
|データ名|(例)data_batch_1.bin|
|メモ|画像の説明など補足情報。|
|タグ|データの種類や受領日などグルーピングしたい単位に付与し、検索等で利用する。|
|ファイル|複数のデータを登録できる。jpg/png/csv/zipなど、ファイルのデータ形式は任意。|

上記の情報を入力し、右下の登録ボタンを押すとデータをアップロードすることができます。
コマンドラインインターフェイス（[CLI](/docs/how-to/cli/)）を使用してデータをアップロードすることも可能です。

KAMONOHASHIでは、複数のファイルを一つのデータとして管理可能ですが、今回は個別に、一ファイル一データとして登録します。

![データアップロード](/assets/images/data-create.PNG)

登録したデータは、データの一覧画面で確認できます。

![データ一覧](/assets/images/data-index.png)

## データセットを作成する
[データセット管理]を選択し、右上の新規登録ボタンから行います。
アップロードしたデータをtraining用、test用にまとめます。
下図では、data_batchをtraining、test_batchをtestingに移動させています。

![データセットアップロード](/assets/images/dataset.PNG)


## 学習を実行する
学習は KAMONOHASHI が管理するクラスタで計算を実行するための最小単位を表します。
学習を開始すると KAMONOHASHI はクラスタから指定されたCPU、メモリ、GPUリソースを確保し、Dockerコンテナを起動し計算環境を用意します。ユーザはこの環境を利用し、任意の計算を行うことができます。
学習はCLI、GUIの両方から起動できます。
GUIで学習を開始するには[学習管理]を選択し、新規登録ボタンから行います。
詳細は[User Guide](/docs/how-to/user#学習)を参照してください。

### step1
学習名を記入し、先ほど登録したデータセットを選択します。
半角英数小文字、または記号(“-”（ハイフン）) 30 文字以下で指定可能です。
![ジョブ1](/assets/images/job-step1.PNG)

### step2
フレームワークとモデルをテキストエリアに記述し、実行コマンドを記述します。
本チュートリアルではフレームワークは[Docker Hub](https://hub.docker.com/)の公式イメージ(pytorch/pytorch)を使用しています。

<!-- ![ジョブ2](/assets/images/job-step2.PNG) -->
* コンテナイメージ

|コンテナイメージ　|記述例　|
|---|---|
|レジストリ|officail-docker-hub(選択)|
|イメージ|pytorch/pytorch|
|タグ|1.3-cuda10.1-cudnn7-runtime|

※Docker Hubを指定した後イメージ、タグをテキストエリアに入力してください。

* モデル の[サンプルコード](https://github.com/KAMONOHASHI/tutorial)

|モデル　|記述例　|
|---|---|
|Gitサーバ|GitHub(選択)|
|リポジトリ|KAMONOHASHI/tutorial|
|ブランチ|master|

※GitHubを指定した後リポジトリ、ブランチをテキストエリアに入力してください。

* 実行コマンド例
```
pip install -U tensorboard
python -u pytorch/train.py \
--images /kqi/input/training \
--anns /kqi/input/training \
--train_log_dir /kqi/output/demo \
--parameter_dir /kqi/output/demo \
--test_images /kqi/input/testing \
--test_anns /kqi/input/testing
--max_epochs 10
```

### step3
必要なリソースを指定します。
![ジョブ3](/assets/images/job-step3.PNG)

### step4
オプションとして追記したい項目があれば追記し実行ボタンを押します。
![ジョブ4](/assets/images/job-run.PNG)

ステータスがRunningになったら学習中です。
初回起動の場合は、DockerコンテナをDockerHubからpullする必要があるため、学習の開始までに少し時間がかかります。



## 学習の状況を確認する

### Tensorboardで学習の状況を確認する

実行した学習を選択し、学習履歴画面を開きます。
<!-- ![ジョブ確認](/assets/images/job-edit.PNG) -->
Tensorboardの起動ボタンを押し、開きます。
<!-- ![Tensorboard確認](/assets/images/tensorboard.PNG) -->

注意：TensorBoardを表示する場合、モデルのpythonファイル中に対応するプログラムを書く必要があります。



### 学習の標準出力を確認する

学習実行中でも標準出力をダウンロードして確認することができます。
GUIからは添付ファイル欄にあるログファイル閲覧ボタンを押すとブラウザ上で標準出力を確認することができます。
![標準出力](/assets/images/training-stdout.PNG)

標準出力は学習履歴画面からダウンロードすることもできます。



### 学習の正常終了を確認する
学習が正常に終了すると、ステータスがCompletedになります。


学習履歴画面のファイル一覧ボタンを押すと
コンテナに出力したファイルを確認することができます。

今回はepoch数を10としたため、10epoch終了後にcheckpoint(.pthファイル)が出力されていることが確認できます。
上述した標準出力をみることでも、学習の正常終了を確認することができます。


## おわりに
このように、KAMONOHASHIでは、簡単に学習を開始することができます。

このチュートリアルでは、KAMONOHASHIを用いて、単一のGPUノードでモデルをトレーニングしました。


KAMONOHASHIでは簡単に学習を開始・管理できるため、効率的なAI開発ができます！
より詳しくKAMONOHASHIの使い方を知りたい場合は[How-to Guide](/docs/how-to/)を参照してください。

