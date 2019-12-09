---
layout: post
title: VeeValidateでVueのバリデーションをしよう
categories: tech
---

Vue.js でバリデーションを行うためのライブラリとして、[VeeValidate](https://logaretm.github.io/vee-validate/) があります。
使い方等を簡単に紹介します。
バージョンは v3.1.3 を対象としています。

## v2 から v3 へ

ひとつ重要な注意点があります。
この記事は 2019/12 に書かれていますが、多くの日本語記事では v2 を対象としていて、現在の v3 では動作しません。
それは、2019/8 の v3 への更新によって破壊的な変更が発生し、それまで `v-validate` ディレクティブを用いていましたが、v3 では `ValidationProvider` を使うようになったからです。

v2 ではメールの入力必須条件を `v-validate` を使い、以下のように書いていました。

```
<input v-validate="'required|email'" name="email" type="text">
<span>{{ errors.first('email') }}</span>
```

v3 では `ValidationProvider` を使い、次のように書きます。

```
<ValidationProvider rules="required|email" v-slot="{ errors }">
  <input v-model="email" type="text">
  <span>{{ errors[0] }}</span>
</ValidationProvider>
```

お使いのバージョンを確認して読んでください。

## Getting Started

VeeValidate をインストールします。

```
$ yarn add vee-validate
or
$ npm install vee-validate --save
```

実際に日本語で使用するためには、バリデーションのルールのロードとエラーメッセージの定義が必要です。
メッセージの定義は(こちら)[https://github.com/logaretm/vee-validate/blob/master/locale/ja.json] を参考にして、プロダクトごとに調整すれば良いです。

```
import { extend } from 'vee-validate';
import * as rules from 'vee-validate/dist/rules';
import ja from './locale/ja.json';


for (let rule in rules) {
  extend(rule, {
    ...rules[rule], // add the rule
    message: ja.messages[rule] // add its message
  });
}
```
