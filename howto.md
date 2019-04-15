---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: "How-to Guide"
permalink: /docs/how-to/
sidebar:
  nav: "docs"
feature_row0:
  - image_path: /assets/images/illust_machineLearning_bl.png
    alt: "placeholder image 0"
    title: "User Guide"
    excerpt: "学習の実行や履歴の管理を行います。"
    url: "/docs/how-to/user"
    btn_label: "Read More"
    btn_class: "btn--primary"
feature_row1:
  - image_path: /assets/images/illust_serverPC_bl.png
    alt: "placeholder image 1"
    title: "Admin Guide"
    excerpt: "各種設定やリソースの監視を行います。"
    url: "/docs/how-to/admin/"
    btn_label: "Read More"
    btn_class: "btn--primary"
feature_row2:
  - image_path: /assets/images/illust_terminal_bl.png
    alt: "placeholder image 2"
    title: "CLI Guide"
    excerpt: "コマンドラインからの操作を行います。"
    url: "/docs/how-to/cli/"  
    btn_label: "Read More"
    btn_class: "btn--primary"
---

{% include feature_row id="feature_row0" type="left" %}
{% include feature_row id="feature_row1" type="left" %}
{% include feature_row id="feature_row2" type="left" %}