---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: "Training a model on cifar-10 Using KAMONOHASHI"
permalink: /tutorial/cifar10
---


# はじめに
このチュートリアルは、KAMONOHASHIプラットフォームを使用する方法を説明することを目的としています。具体的には、KAMONOHASHIを使用して次のことを行う方法について説明します。

 1. データをアップロードする
 1. データセットを作成する
 1. 学習を実行する
 1. TensorBoardで学習の状況を表示する
 1. 学習のログを確認する

# データをアップロードする

## cifar-10データセットをダウンロードする
このチュートリアルでは、[cifar-10データセット](https://www.cs.toronto.edu/~kriz/cifar.html)を使用します。cifar10は、カラー画像のデータセットです。
 - 画像サイズは32 x 32px
 - 10クラスの画像がそれぞれ6000枚、計60000枚の画像がある
 - そのうち50000枚が学習データ、10000枚がテストデータ
 - クラスはairplane, automobile, bird, cat, deer, dog, frog, horse, ship, truck
 
[cifar-10のデータセットページ](http://www.cs.toronto.edu/~kriz/cifar-10-binary.tar.gz)からダウンロードしてください。

## データを解凍する
このデータセットは解凍すると、

- data_batch_1.bin
- ...
- data_batch_5.bin
- test_batch.bin

というファイルに分割されます。それぞれのbinファイルは、32x32x3の画像データが1万枚ずつ含まれています。
画像1枚あたり、先頭1バイトがラベル + 32x32x3バイトが画素情報という構成になっており、これが先頭から1万個結合されているのがbinファイルです。

## データをKAMONOHASHIにアップロードする
データが用意できたらKAMONOHASHIにアップロードしましょう。GUIまたはコマンドラインインターフェイス（CLI）を使用してKAMONOHASHI上にデータを作成します。

```
> kqi data create -name "dataの名前" -d "uploadするデータのpath"
```

![データアップロード](/assets/images/data-create.PNG)

登録したデータは、データの一覧画面で確認できます。

![データ一覧](/assets/images/data-index.png)

# データセットを作成する
アップロードしたデータをtraining用、test用にまとめます。
下図では、data_batchをtrain、test_batchをtestに移動させています。

![データセットアップロード](/assets/images/dataset.PNG)


# 学習を実行する
学習は KAMONOHASHI が管理するクラスタで計算を実行するための最小単位を表します。
学習を開始すると KAMONOHASHI はクラスタから指定されたCPU、メモリ、GPUリソースを確保し、Dockerコンテナを起動し計算環境を用意します。ユーザはこの環境を利用し、任意の計算を行うことができます。
学習はCLI、GUIの両方から起動できます。
GUIで学習を起動するには[学習管理]を選択し、新規登録ボタンから行います。
詳細は[User Guide](/docs/user/#学習)を参照してください。

## step1
学習名を記入し、先ほど登録したデータセットを選択します。
![ジョブ1](/assets/images/job-step1.PNG)

## step2
フレームワークとモデルを選択し、実行コマンドを記述します。
![ジョブ2](/assets/images/job-step2.PNG)

## step3
必要なリソースを指定します。
![ジョブ3](/assets/images/job-step3.PNG)

## step4
オプションとして追記したい項目があれば追記し実行ボタンを押します。
![ジョブ4](/assets/images/job-run.PNG)

ステータスがRunningになったら学習中という意味です。



# Tensorboardで学習の状況を確認する
実行中の学習を選択し、編集画面を開きます。
![ジョブ確認](/assets/images/job-edit.PNG)
Tensorboardの起動ボタンを押し、開きます。
![Tensorboard確認](/assets/images/tensorboard.PNG)

注意：TensorBoardを表示する場合、モデルのpythonファイル中に対応するプログラムを書く必要があります。


# 学習のログを確認する
学習実行中でもログをダウンロードして確認することができます。

```
> kqi training download-log trainingID
```

また、GUIからは添付ファイル欄からログがダウンロードできることに加え、
コンテナに出力したファイルを確認することができます。
![ジョブ確認](/assets/images/job-edit.PNG)
![コンテナ出力ファイル](/assets/images/container-files.PNG)

# おわりに
このように、KAMONOHASHIでは、簡単に学習を開始することができます。

本チュートリアルでは、KAMONOHASHIを用いて、単一のGPUノードでモデルをトレーニングしました。
KAMONOHASHIでは簡単に学習を開始・管理できるため、チームでのAI開発がはかどります！