---
layout: post
title: Sassの@use, @forwardについて
categories: tech
---

Sassはこれまでの`@import`を将来的に廃止し、新たに`@use`、`@forward`のモジュールシステムを使うように動こうとしています。
現在（2020/01)ではDart版のSassのみ実装されていますが、LibSassにも実装される予定です。
`@use`、`@forward`を簡単に紹介したいと思います。


## @useとは？

`@use`は`@import`と同様に別のファイルの情報を読み取ります。
スタイルの定義は今まで通り、`@use`を使ったファイルに結合されることになります。
ただし、変数や関数、mixinはファイル単位で名前空間を持ちます。

例えば、次のファイルのようにヘッダー要素を別のファイルに定義し、style.scssでmixinを呼び出す場合、デフォルトでファイル名と同じ名前空間を持つことになります。
そのため、変数やmixinを呼び出す際は`<namespace>.<variable>` や `<namespace>.<function>` 、`<namespace>.<mixin>` という形式で呼び出すこと必要があります。



```scss
// heading.scss
$color: red !default;

@mixin wrapper {
  font-size: 36px;
  color: $color;
}

// 普通のスタイルを定義
h1 {
  color: blue;
}
```


```scss
// style.scss
@use 'heading';


h2 {
  // mixinは名前空間が必要なので、<namespace>.<mixin>で指定する
  @include heading.wrapper;
}
```

```css
/* 出力されるCSS */

/* 普通のスタイルの定義はそのまま出力される */
h1 {
  color: blue;
}

h2 {
  font-size: 36px;
  color: red;
}
```

変数やmixinにファイル固有の名前空間が作られるため、グローバルで変数名やmixin名の重複を心配する必要はなくなります。
そのため、役割ごとにファイルを分けることによって、シンプルな変数名やmixin名を使うことができるようになります。
`@use`で呼び出される際の名前空間の名前はデフォルトではファイル名ですが、指定することも可能です。


```scss
@use 'src/heading';    // これは heading.hgoehoge の名前空間が生成される
@use 'button' as b;    // これは b.hgoehoge の名前空間が生成される
@use 'corners' as *;   // 名前空間が生成されず、hogehoge で使える（非推奨
```

`@use`を使用した際に変数の値を書き換えることもできます。


```scss
// heading.scss
$color: red !default;
$height: 30px !default;
```

```scss
// withを使って変数を書き換えることができます。リストなのでカンマ区切りになります。
@use 'heading' with (
  $color: blue,
  $height: 50px
);
```


## @forwardとは？

`@use`を使うことで名前空間を生成し、ロードしたファイルにスコープが生成され、そのファイルでのみ使用することができます。
しかし、そうなるとCSSフレームワークなどで変数や関数が見えなくなる状況が生まれます。

```scss
// heading.scss
$color: red !default;

@mixin wrapper {
  font-size: 36px;
  color: $color;
}
```

```scss
// bootstrap.scss
@use 'heading';
```


```scss
// style.css
@use 'bootstrap';

h1 {
  // Error!!
  // bootstrap.scssないで@useでロードしているため、他のファイルからは見えないためエラーになる
  @include bootstrap.wrapper;
}

```

そのファイルをロードしたファイルにも変数や関数が見えるようにするためには `@forward`を使う必要があります。

```scss
// heading.scss
$color: red !default;

@mixin wrapper {
  font-size: 36px;
  color: $color;
}
```

```scss
// bootstrap.scss
@forward 'heading';
```


```scss
// style.css
@use 'bootstrap';

h1 {
  // Success!!
  // bootstrap.scssないで@forwardでロードしているため見える
  @include bootstrap.wrapper;
}
```

`@forward`する際にプレフィックスをつけることもできます。

```scss
// heading.scss
$color: red !default;

@mixin wrapper {
  font-size: 36px;
  color: $color;
}
```

```scss
// bootstrap.scss
@forward 'heading' as heading-*;
```


```scss
// style.css
@use 'bootstrap';

h1 {
  // "heading-"のプレフィックスが必要
  @include bootstrap.heading-wrapper;
}
```
