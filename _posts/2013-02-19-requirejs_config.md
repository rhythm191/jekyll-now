---
layout: post
title: requirejsのconfigを調べてみた
categories: tech
---

毎回見直しにいくのもめんどいのでrequirejsのconfigオプションで指定できるものをまとめてみました．この記事ではバージョン2.1のrequirejsを対象としています．

requirejsのconfigオプションの指定する方法は2つあります．

ひとつはrequirejsを読み込んだ後に，require.configメソッドを呼び出す方法．

もうひとつは requrejsを読み込む前に，”var requre = {…}”でrequireオブジェクトを作成する方法

configオプションで指定できるものは次の6つです．

* baseUrl : 依存解決のルートディレクトリを指定
* paths: baseUrl配下にないモジュールを個別に指定できる
* shim: AMDの形式でないモジュールをAMDの形式に定義する
* map: 特定のモジュールが読み込むモジュールに
* config: モジュールに注入する設定
* packages: CommonJSパッケージから設定を読み出す
* waitSecconds: スクリプトロードのタイムアウト
* context: コンテキスト名(同じモジュールの複数のバージョンを呼び出す際に使用する)
* deps: 依存関係の配列
* callback: depsを読み込んだ後に実行するコールバック
* enforceDefine: trueならAMD形式でないスクリプトを読み込んだときにエラーを投げる
* xhtml: trueならスクリプトエレメントをdocument.createNS()を利用する
* urlArgs: リソースを取得するurlにつけるパラメータ(キャッシュ切りに利用する)
* scriptType: スクリプトエレメントに記載するtypeを定義

以下ではいくつかのパラメータの補足をしておきます．


### baseUrl

baseUrlはモジュールのルートディレクトリを定義します．

例えば，baseUrlで”path/to”を指定した場合に define([ “my/module” ] …” と定義した場合は，”path/to/my/module.js”をロードします．

requirejsを読み込む際に何も指定しない場合は，読み出したhtmlのディレクトリをbaseUrlとします．

requirejsを読み込むスクリプトにdata-mainを指定した場合は，そのスクリプトのディレクトリがルートディレクトリになります．

baseUrlは絶対パス．相対パスのどちらでも指定できますが，相対パスを利用した場合は，読み出したhtmlのディレクトリを起点としたパスになります．

### pahts

pathsはbaseUrl配下にないモジュールのマッピングを定義します．

pathsにはディレクトリ，またはモジュール単体を定義することができます．

ディレクトリを定義した場合は，モジュール名の先頭にpathsのキーを指定します．

pathsのバリューは絶対パス，またはbaseUrlからの相対パスを指定することができます．

例えば，

```js
require.config({
  baseUrl: 'some',
  paths: {
    'lib': 'js/lib',
    'jquery': '/hoge/jquery'
  }
});
```

のように定義すると，

“some/js/lib/backbone”を参照したい場合は”lib/backbone”と指定し，

“jquery”を指定した場合は，”/home/jquery”を参照します．


### shim

shimはAMDの形式にないJavascriptをAMDの形式(依存関係とモジュールの返り値が定まった形式)に再定義します．

例えばBackboen.jsならば次のように記述します．

depsに記述するモジュールはbaseUrl等のパスに影響します．

```js
requirejs.config({
  shim: {
    'backbone': {
      // deps にはモジュールの依存関係を記述
      deps: ['underscore', 'jquery'],
      // exports にはモジュールの返り値を記載
      exports: 'Backbone',
      // init にはモジュールの初期化を記載(省略可能)
      // noConflictメソッドを呼び出すときに利用する
      //init: function(backbone){}
    }
  }
});
```

jqueryのプラグインなど出力がなく，jqueryオブジェクトを拡張するのみのモジュールなどは次のように記述します．

requirejs.config({
  shim: {
    'jquery.scroll': ['jquery']
  }
});

### map

mapはある特定パスのモジュールが読み込むモジュールを再定義することができます．
例えば，“some/old/a.js”が読み込むjqueryをバージョン1.7にし，その他のモジュールが読み込むjqueryを1.8にしたい場合，
mapオプションを利用して次のように記述します．

```js
requirejs.config({
  map: {
    '*': {
      'jquery': 'lib/jquery-1.8'
    },
    'some/old/a': {
      'jquery': 'lib/jquery-1.7'
    }
  }
});
```

### config

configオプションを使うことでモジュールに対して設定値を定義することができる
configオプションはハッシュ形式でモジュール名をキー，バリーに設定値を記述する．
例えば，bar.jsに対して設定値を定義したい場合は

```js
requirejs.config({
  config: {
    'bar': {
      size: 'large'
    }
  }
});

//bar.js, 特別なモジュール名として"module"を呼び出す
define(['module'], function (module) {
  var size = module.config().size;
});
```

### context

同じモジュールの複数のバージョンを呼び出す際に，名前かぶりをさけるために利用する．

下の例の通り，require.configの戻り値を利用してそれぞれのモジュールを呼び出す．

```js
var reqOne = require.config({
  context: "version1",
  baseUrl: "version1"
});

reqOne(["require", "alpha", "beta",], function(require, alpha, beta) {
  log("alpha version is: " + alpha.version); //prints 1
  log("beta version is: " + beta.version); //prints 1
  setTimeout(function() {
    require(["omega"], function(omega) {
      log("version1 omega loaded with version: " + omega.version); //prints 1
    });
  }, 100);
});

var reqTwo = require.config({
  context: "version2",
  baseUrl: "version2"
});

reqTwo(["require", "alpha", "beta"], function(require, alpha, beta) {
  log("alpha version is: " + alpha.version); //prints 2
  log("beta version is: " + beta.version); //prints 2
  setTimeout(function() {
    require(["omega"], function(omega) {
      log("version2 omega loaded with version: " + omega.version); //prints 2
    });
  }, 100);
});
```
