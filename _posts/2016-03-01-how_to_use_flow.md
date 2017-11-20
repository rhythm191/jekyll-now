---
layout: post
title: flowの覚書(flow 0.19.0)
---

flowのバイナリ自体は型チェッカーだが、本質はJavaScriptに静的型付けなコーディングスタイルを提供すること。
JavaScriptの拡張言語と考えてもらえばよい。
実際の開発ではbabelの[プラグイン](https://www.npmjs.com/package/babel-plugin-transform-flow-strip-types)として設定し、babelのコンパイル時に型情報は消える。

flowはJavaScriptの拡張言語として、静的型付けのコーディングスタイルを提供する。
そして、チェックする対象のソースコードに`/* @flow */`をつければ良い。
例えば、関数doubleはnumber型の値を引数に取ることを宣言できる。

```js
/* @flow */

function double(x: number) {
  return x * 2;
}

double(2);
```


関数doubleの引数をstring型にした場合、flowの型チェックでエラーが出力される。

```js
/* @flow */

function double(x: string) {
  return x * 2;
}

double('Hello world');

>flow check
  7:   return x * 2;
              ^ string. This type is incompatible with
  7:   return x * 2;
              ^^^^^ number
```


flowのチェック自体は型情報を付けなくても賢くチェックする。
例えば次の場合は、number型にlengthプロパティがないとエラーが出力される。

```js
/* @flow */

function double(x) {
  return x.length * 2;
}

double(3);

>flow check
  7:   return x.length * 2;
                ^^^^^^ property `length`. Property not found in
  7:   return x.length * 2;
              ^ Number
```


その他、flowでできること

* 変数、関数の引数、戻り値、クラス変数に型をつける
* 型情報をパラメータ化してクラス渡せるジェネリクス(Arrayとか
* 複雑な型情報を定義するタイプエイリアス
* 型チェック、nullチェックによる静的解析を行う

あとは、flowのホームページを読んでよ。
検索するとミュージシャンの方が出るのでリンクを貼っておくよ。

[http://flowtype.org/](http://flowtype.org/)
