---
layout: post
title: vue.jsの単一コンポーネントでCSSを外出しする
categories: tech
---

vue.jsの単一コンポーネントファイルないのCSSやSCSSを外出しする方法を解説します。

CSSを別ファイルに分けるメリットは、エディタの補間（特にEmmetとか）が効きやすくなります。
やり方は簡単で単一コンポーネントファイルないの`style`タグの`src`属性でファイルを指定するだけです。
```html
<!-- CSSファイルの場合 -->
<style src="./style.css"></style>

<!-- SCSSファイルの場合 -->
<style lang="scss" src="./style.scss"></style>
```

もちろんスコープ付きCSSにも対応しています。

```html
<!-- CSSファイルの場合 -->
<style scoped src="./style.css"></style>

<!-- SCSSファイルの場合 -->
<style scoped lang="scss" src="./style.scss"></style>
```