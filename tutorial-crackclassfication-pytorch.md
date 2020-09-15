---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: "Concrete Crack Classification Tutorial (pytorch)"
permalink: /docs/tutorial/crackclassification-pytorch
sidebar:
  nav: "docs"
---

## はじめる前に

このチュートリアルを始める前に、以下の KAMONOHASHI のインストールが終わり、KAMONOHASHI にログインできることを確認してください。

(参考) [KAMONOHASHI をインストールする](/docs/install-and-update#インストール方法)

## はじめに

本チュートリアルでは、KAMONOHASHI を使用して、コンクリート画像のヒビの有無を検出する方法を以下の手順に沿って説明します。

1.  データをアップロードする
1.  データセットを作成する
1.  学習を実行する
1.  TensorBoard で学習の状況を表示する
1.  学習のログを確認する

## データをアップロードする

KAMONOHASHI にデータをアップロードする流れを説明します。

### concrete データセットを準備する

このチュートリアルでは、[concrete データセット](https://data.mendeley.com/datasets/5y9wdsg2zt/2)<i class="material-icons" class="material-icons blue">launch</i>(Özgenel, Çağlar Fırat (2019), “Concrete Crack Images for Classification”, Mendeley Data, V2, doi: 10.17632/5y9wdsg2zt.2)を使用します。

[Concrete Crack Images for Classification のデータをダウンロードする](https://data.mendeley.com/public-files/datasets/5y9wdsg2zt/files/8a70d8a5-bce9-4291-bab9-b48cfb3e87c3/file_downloaded)<i class="material-icons" class="material-icons blue">save_alt</i>

Concrete Crack Images for Classification は、カラー画像のデータセットです
このデータセットは解凍すると、

- Positive
- Negatvie

の 2 ディレクトリに分かれており、それぞれ 20000 枚のデータが含まれています。

KAMONOHASHI へのデータのアップロードの前にそれぞれのクラスのファイル名の先頭にクラスの頭文字(P または N)をつけ、
そのうち 15000 枚を学習データ、5000 枚をテストデータとしてまとめて zip 化します。

- train.zip(Positive 15000 枚, Negative 15000 枚)
- test.zip(Positive 5000 枚, Negative 5000 枚)

![ローカルでのデータ準備](/assets/images/concrete-data-preparation.png)

### データを KAMONOHASHI にアップロードする

データが用意できたら KAMONOHASHI にアップロードしましょう。
[データ管理]を選択し、右上の新規登録ボタンから行います。

| 種類　   | 説明　                                                                       |
| -------- | ---------------------------------------------------------------------------- |
| データ名 | (例)train.zip                                                                |
| メモ     | 画像の説明など補足情報。                                                     |
| タグ     | データの種類や受領日などグルーピングしたい単位に付与し、検索等で利用する。   |
| ファイル | 複数のデータを登録できる。jpg/png/csv/zip など、ファイルのデータ形式は任意。 |

上記の情報を入力し、右下の登録ボタンを押すとデータをアップロードすることができます。
コマンドラインインターフェイス（[CLI](/docs/how-to/cli/)）を使用してデータをアップロードすることも可能です。

KAMONOHASHI では、複数のファイルを一つのデータとして管理可能ですが、今回は個別に、一ファイル一データとして登録します。

![データアップロード](/assets/images/concrete-data.png)

登録したデータは、データの一覧画面で確認できます。

![データ一覧](/assets/images/concrete-data-index.png)

## データセットを作成する

[データセット管理]を選択し、右上の新規登録ボタンから行います。
アップロードしたデータを training 用、test 用にまとめます。
下図では、train.zip を training、test.zip を testing に移動させています。

![データセットアップロード](/assets/images/concrete-dataset.PNG)

## 学習を実行する

学習は KAMONOHASHI が管理するクラスタで計算を実行するための最小単位を表します。
学習を開始すると KAMONOHASHI はクラスタから指定された CPU、メモリ、GPU リソースを確保し、Docker コンテナを起動し計算環境を用意します。ユーザはこの環境を利用し、任意の計算を行うことができます。
学習は CLI、GUI の両方から起動できます。
GUI で学習を開始するには[学習管理]を選択し、新規登録ボタンから行います。
詳細は[User Guide](/docs/how-to/user#学習)を参照してください。

### step1

学習名を記入し、先ほど登録したデータセットを選択します。
半角英数小文字、または記号(“-”（ハイフン）) 30 文字以下で指定可能です。
![ジョブ1](/assets/images/job-step1.PNG)

### step2

フレームワークとモデルをテキストエリアに記述し、実行コマンドを記述します。
本チュートリアルではフレームワークは[Docker Hub](https://hub.docker.com/)の公式イメージ(pytorch/pytorch)を使用しています。

<!-- ![ジョブ2](/assets/images/job-step2.PNG) -->

- コンテナイメージ

| コンテナイメージ　 | 記述例　                    |
| ------------------ | --------------------------- |
| レジストリ         | officail-docker-hub(選択)   |
| イメージ           | pytorch/pytorch             |
| タグ               | 1.3-cuda10.1-cudnn7-runtime |

※Docker Hub を指定した後イメージ、タグをテキストエリアに入力してください。

- モデル の[サンプルコード](https://github.com/KAMONOHASHI/tutorial)

| モデル　   | 記述例　             |
| ---------- | -------------------- |
| Git サーバ | GitHub(選択)         |
| リポジトリ | KAMONOHASHI/tutorial |
| ブランチ   | master               |

※GitHub を指定した後リポジトリ、ブランチをテキストエリアに入力してください。

- 実行コマンド例

```
pip install matplotlib --force
pip install pandas
apt-get update
apt-get install unzip
unzip /kqi/input/training/*/train.zip -d "/kqi/input/training"
unzip /kqi/input/testing/*/test.zip -d "/kqi/input/testing"
python -u train.py \
--train_log_dir /kqi/output/demo \
--parameter_dir /kqi/output/demo \
```

### step3

必要なリソースを指定します。
以下は例です。

例

| リソース　 | 使用量　 |
| ---------- | -------- |
| CPU        | 8        |
| Memory     | 8        |
| GPU        | 1        |

### step4

オプションとして追記したい項目があれば追記し実行ボタンを押します。
![ジョブ4](/assets/images/job-run.PNG)

ステータスが Running になったら学習中です。
初回起動の場合は、Docker コンテナを DockerHub から pull する必要があるため、学習の開始までに少し時間がかかります。

## 学習の状況を確認する

### Tensorboard で学習の状況を確認する

実行した学習を選択し、学習履歴画面を開きます。

<!-- ![ジョブ確認](/assets/images/job-edit.PNG) -->

Tensorboard の起動ボタンを押し、開きます。

<!-- ![Tensorboard確認](/assets/images/tensorboard.PNG) -->

注意：TensorBoard を表示する場合、モデルの python ファイル中に対応するプログラムを書く必要があります。

### 学習の標準出力を確認する

学習実行中でも標準出力をダウンロードして確認することができます。
GUI からは添付ファイル欄にあるログファイル閲覧ボタンを押すとブラウザ上で標準出力を確認することができます。
![標準出力](/assets/images/training-stdout.PNG)

標準出力は学習履歴画面からダウンロードすることもできます。

### 学習の正常終了を確認する

学習が正常に終了すると、ステータスが Completed になります。

学習履歴画面のファイル一覧ボタンを押すと
コンテナに出力したファイルを確認することができます。

今回は epoch 数を 3 としたため、3epoch 終了後に checkpoint(.pth ファイル)が出力されていることが確認できます。
上述した標準出力をみることでも、学習の正常終了を確認することができます。

## おわりに

このように、KAMONOHASHI では、簡単に学習を開始することができます。

このチュートリアルでは、KAMONOHASHI を用いて、コンクリートのひびを検出するモデルを作成し、
GPU ノードを用いて転移学習を行いました。

実際の現場ではこのチュートリアルのように異常画像が豊富にあるケースは少ないかもしれません。
そういった際にはより試行錯誤が必要になってくるかと思います。
KAMONOHASHI では学習の試行錯誤を管理できるため、効率的な AI 開発ができます。

より詳しく KAMONOHASHI の使い方を知りたい場合は[How-to Guide](/docs/how-to/)を参照してください。
