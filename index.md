---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: splash


feature_row0:
  - image_path: /assets/images/logo_002.png
    alt: "placeholder image 0"
    title: "Deep Learning 開発者を煩雑な作業から解放！"
    excerpt: 'KAMONOHASHIは、新日鉄住金ソリューションズが Deep Learning を適用する過程で生み出した学習実行の運用環境です。Deep learningプロセスで発生する多くの副次的作業（雑用）の一つ一つ効率化したノウハウをプラットフォームとして凝縮しています。
    <br><br>KAMONOHASHIはOSSであり、どなたでも使用することができます。AI開発プロセスを最大限効率化した、最高のモデル開発を体験してみませんか。'
    url: "/tutorial/"
    btn_label: "Tutorialをみる"
    btn_class: "btn--primary"
    url: "https://github.com/KAMONOHASHI"
    btn_label: "GitHubをみる"
    btn_class: "btn--primary"

feature_row1:
  - image_path: /assets/images/illust_file2_bl.png
    alt: "placeholder image 1"
    title: "データの管理が大変"
    excerpt: "学習に使用する大量のデータファイルを、手作業で管理するのが面倒..."
  - image_path: /assets/images/illust_gpu_full.png
    alt: "placeholder image 2"
    title: "リソースの管理が大変"
    excerpt: "GPUはとても高額なのに、使用頻度はバラつきがあったり...効率よくリソースをシェアしたい！"
  - image_path: /assets/images/illust_job_manage.png
    title: "ジョブの管理が大変"
    excerpt: "学習に使用したデータ・モデルは手作業で管理。過去の学習条件を再現するのにもひと手間…"

feature_row2:
  - image_path: /assets/images/date_tag.png
    alt: "placeholder image 1"
    title: "タグ・メモ機能"
    excerpt: "大量のデータは形式問わずまとめてアップロード。ひとつひとつにメモやタグをつけて整理できるためデータ検索が容易です。"
  - image_path: /assets/images/resource2.png
    alt: "placeholder image 2"
    title: "リソース一覧機能"
    excerpt: "学習用に用意されたリソースはKAMONOHASHIで一覧化。空き状況を常に確認することができ、チームでのAI開発がはかどります。"
  - image_path: /assets/images/job_manage2.png
    title: "ジョブ管理機能"
    excerpt: "学習に使用した諸々の設定は全てKAMONOHASHIが記録して一覧化。手書きや表計算ソフトでの転記はもう必要ありません。"

feature_row3:
  - image_path: /assets/images/secur.png
    alt: "placeholder image 1"
    title: "複数案件を同居"
    excerpt: "KAMONOHASHIはマルチテナント方式で構成されます。管理者がユーザごとに案件参加権限を付与することで、案件やチーム間でストレージや計算環境などのリソース共有を実現します。"
  - image_path: /assets/images/resource_optimization.png
    alt: "placeholder image 2"
    title: "計算リソース稼働率の最適化"
    excerpt: "KAMONOHASHIは各ジョブに対して、GPU単位でのリソース割り当てが可能です。リソースを最適な粒度で分割して割り振ることで稼働率を向上できます。また、特定のチームだけでリソースを独占されないように、利用上限を設定したり、ノードごとに利用できるチームを制限することも可能です。"

feature_row4:
  - image_path: /assets/images/illust_book2.png
    alt: "placeholder image 1"
    title: "マニュアル"
    excerpt: "開発者向けとシステム管理者向けの2種類のマニュアルを用意しています。<br>KAMONOHASHIをCLIで利用する際のガイドもあります。"
    url: "/docs/"
    btn_label: "Manual"
    btn_class: "btn--primary"

  - image_path: /assets/images/illust_book.png
    alt: "placeholder image 2"
    title: "チュートリアル"
    excerpt: "初めての利用者向けのチュートリアルを用意しています。ステップバイステップ形式で、KAMONOHASHIの使用方法を知ることができます。"
    url: "/tutorial/"
    btn_label: "Tutorial"
    btn_class: "btn--primary"


---

<div class="feature__wrapper">
    <div class="feature__item--left">
      <div class="archive__item">
          <div class="archive__item-teaser">
            <img src="/assets/images/logo_002.png" alt="placeholder image 0">
          </div>
        <div class="archive__item-body">
            <h2 class="archive__item-title">Deep Learning 開発者を煩雑な作業から解放！</h2>
            <div class="archive__item-excerpt">
              <p>KAMONOHASHIは、Deep Learningのモデル開発を効率的に行うためのAI開発プラットフォームです。
Deep learningのモデル開発には、複雑な環境構・計算リソースの確保・大量に発生するデータや学習履歴の管理といった、煩雑な作業が多く存在します。KAMONOHASHIはこれらの作業を削減し、AI開発者がモデル開発に集中できる環境を提供します。 <br><br>AI開発プロセスを最大限に効率化し、最高のモデル開発を体験してみませんか。</p>
            </div>
            <div id="index__btn">
            <!-- <p><a href="https://github.com/KAMONOHASHI" class="btn btn--primary">GitHubをみる</a></p> -->
            <p><a href="/tutorial/" class="btn btn--primary">Tutorialをみる</a></p>
            </div>
        </div>
      </div>
    </div>
</div>


<h2 style="text-align: center;font-size:1.5em; font-weight:bold; border-bottom:none; margin-bottom: 1em;">
　AI開発におけるこんな悩みはありませんか？
</h2>
{% include feature_row id="feature_row1" %}

<h2 style="text-align: center;font-size:1.5em; font-weight:bold; border-bottom:none; margin-bottom: 1em;">
　KAMONOHASHIで解決できます
</h2>
{% include feature_row id="feature_row2" %}

<h2 style="text-align: center;font-size:1.5em; font-weight:bold; border-bottom:none; margin-bottom: 1em;">
　管理も楽々
</h2>
<div id="feature_row3">
{% include feature_row id="feature_row3" type="left" %}
</div>

<!-- <h2 style="text-align: center;font-size:1.5em; font-weight:bold; border-bottom:none; margin-bottom: 1em;">
　チュートリアルやマニュアルを完備
</h2>
{% include feature_row id="feature_row4" type="center" %} -->

<h2 style="text-align: center;font-size:1.5em; font-weight:bold; border-bottom:none; margin-bottom: 1em;">
　お問い合わせ
</h2>  
  
<!-- KAMONOHASHI についてのお問合せは、下記のお問い合わせフォームからどうぞ。担当者よりご連絡させていただきます。  -->
KAMONOHASHI についてのお問合せは、下記からどうぞ。担当者よりご連絡させていただきます。 

```
kamonohashi-support@jp.nssol.nipponsteel.com
```
