---
layout: post
title: play1.*でサードパーティのjarを追加する方法
categories: tech
---

表題の件．調べるのに時間がかかったのでメモ．


dependencies.ymlに以下のものを追加

```
require:
    - provided -> evernote-api 1.20

repositories:
    - provided:
        type:       local
        artifact:   "${application.path}/jar/[module]-[revision].jar"
        contains:
            - provided -> *
```

そして，アプリケーションフォルダの直下にjarフォルダを作成し，

必要なサードパーティ製jarを追加していくという寸法

(evernote-api-1.20.jarを入れている)
