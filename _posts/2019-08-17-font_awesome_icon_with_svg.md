---
layout: post
title: Font Awesome のWebFontとSVGの共存について
categories: tech
---

Font AwesomeのWebFontを使いつつ、レイヤーやマスキングの部分だけSVG + JSを使う方法です。
SVGを使う場合は `prefix-svg` のクラスを使うことで `<i>` をSVGに変換します。

```
import { library, dom } from '@fortawesome/fontawesome-svg-core';
import { fab } from '@fortawesome/free-brands-svg-icons';
import { fas } from '@fortawesome/free-solid-svg-icons';
import { far } from '@fortawesome/free-regular-svg-icons';

// font-awesome setting
Object.keys(fab).forEach(icon_key => (fab[icon_key].prefix = 'fab-svg'));
Object.keys(fas).forEach(icon_key => (fas[icon_key].prefix = 'fas-svg'));
Object.keys(far).forEach(icon_key => (far[icon_key].prefix = 'far-svg'));

library.add(fab, fas, far);

dom.watch();
```

例えば `fa-envelope`をレイヤーとして使う場合です。

```
<span class="fa-layers fa-fw" style="background:MistyRose">
  <i class="fas fas-svg fa-envelope"></i>
  <span class="fa-layers-counter" style="background:Tomato">1,419</span>
</span>
```
