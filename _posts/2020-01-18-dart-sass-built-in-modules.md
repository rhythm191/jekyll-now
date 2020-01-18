---
layout: post
title: SassのBuilt-in modulesで遊んでみた
categories: tech
---

Sassにはたくさんの便利なビルトインモジュールが実装されています。
今回は"sass:math"モジュールのrandom関数を使ってアニメーションを作ってみました。

ビルトインモジュールのより詳細な情報は公式のドキュメントを読んでね。  
[https://sass-lang.com/documentation/modules](https://sass-lang.com/documentation/modules)


今回作ったアニメーションは100個のランダムな大きさ・色の円をランダムな位置に配置して拡大や透過を行います。
この時のランダムな数値を"sass:math"モジュールのrandom関数で算出します。

まず、random関数を使うためには"sass:math"モジュールをロードします。

```scss
@use "sass:math";
```

randam関数は引数なしで使うことで0から1までの値を出力します。
例えば、30px〜300pxまでのランダムな大きさを取得するには次にように使います。


```scss
$size: math.random() * 300px + 30px;
```

また、ランダムに色をつけるには次のように使います。

```scss
$color: rgb(math.random() * 255, math.random() * 255, math.random() * 255);
```


あとは"@for"を使って100個分のアニメーションクラスを作ります。(詳細はソースコードを読んでね)

```scss
@for $i from 1 through 100 {
  .animation#{$i} {
    // something property
    animation: scale-#{$i}
      math.random() * 2 + 3s
      math.random() * 30 + 1s
      infinite
      normal;
  }

  @keyframes scale-#{$i} {
    // something animation
  }
}
```

このようにして、sassの機能だけでランダムな値を生成することができ、ランダムなアニメーションを作ることができました。
実際に作ったアプリケーションがこれです。  
[https://dart-sass-bubble.rhyztech.net/](https://dart-sass-bubble.rhyztech.net/)

ソースコードはここです。  
[https://github.com/rhythm191/dart-sass-bubble](https://github.com/rhythm191/dart-sass-bubble)


