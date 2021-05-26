---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: single
classes: wide
title: FAQ
permalink: /docs/faq-detail/operation
sidebar:
  nav: "faq"
---

こちらではKAMONOHASHI利用に関するよくある質問とそれに対する答えが読めます。



## 対応環境を教えてください

KAMONOHASHIの動作確認済み環境を下表に示します。

|項目  |バージョン  |
|---|---|
|OS  |Ubuntu 18.04、Windows 10  |
|ブラウザ  |Chrome 最新版, Firefox 最新版  |

## ログの場所を教えてください

| ログ種別 | マシン | パス |
|---|---|---|
| 構築ツール | Kubernetes Master | `/var/log/kamonohashi/kubespray.log`|
| KAMONOHASHI | KAMONOHASHIマシン | `/var/log/kamonohashi/platypus-web-api/`配下の各ファイル |

