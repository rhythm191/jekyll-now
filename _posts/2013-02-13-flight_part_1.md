---
layout: post
title: JavaScriptフレームワークFlightでtodoリストを作ってみた bower編
categories: tech
---

TwitterのJavaScriptフレームワークFlightを利用してTodoリストを作成していこうと思います．ただ，その前にbowerというパッケージマネージャについて簡単な紹介をしておこうと思います．

BowerはWeb開発向けのパッケージマネージャーです．これもTwitter社が作成しています．

bowerを利用することで，画像・CSS・JavaScriptといったリソースのインストールや依存関係の解決だけでなく， リソースのバージョンの管理も簡単に行うことができるようになります．

bowerに関しては， この辺を見てもらえれば，インストールと使い方は分かります．

* [http://qiita.com/items/ba952bdade627af99e93](http://qiita.com/items/ba952bdade627af99e93)
* [http://blog.mach3.jp/2013/01/bower.html](http://blog.mach3.jp/2013/01/bower.html)

じゃあ，さっそくFlightをインストールしてみましょう．

```
bower install flight
```

ね．簡単でしょ．

実際にモジュールがインストールされているか確認しましょう．

```
$ bower ls
bower discover Please wait while newer package versions are being discovered
/Users/rhythm/work/ftodo
├── es5-shim#2.0.0 (2.0.5 now available)
├─┬ flight#1.0.1
│ ├── es5-shim#2.0.0 (2.0.5 now available)
│ └── jquery#1.8.3 (1.9.1 now available)
└── jquery#1.8.3 (1.9.1 now available)
```

Flightに必要なjqueryやes5-shimが一緒にインストールされていることが分かります．

インストールしたモジュールはデフォルトでcomponents以下に格納されます．確認してみましょう．

```
$ ls components
es5-shim flight jquery
```

この各モジュール名のディレクトリ配下に各モジュールのリソースとリソースの情報を表すcomponent.jsonが格納されています．

試しに，jqueryのcomponent.jsonを見てみましょう．

```
$ cat components/jquery/component.json
{
  "name": "jquery",
  "version": "1.8.3",
  "main": "./jquery.js",
  "dependencies": {},
  "gitHead": "7d6149dea8a6cdd0d1fbeca8a189f4d464d6f0cd",
  "_id": "jquery@1.8.3",
  "readme": "ERROR: No README.md file found!",
  "description": "ERROR: No README.md file found!",
  "repository": {
    "type": "git",
    "url": "git://github.com/components/jquery.git"
  }
}
```

このcomponent.jsonでリスースのバージョンや依存関係(dependencies)が管理されます．

だからファイル名にバージョン番号をつける必要がないです．

話がすこし脱線してしまいましたが，ついでにmustacheとbootstrap，requirejsを入れてしまいましょう．

```
$ bower install mustache bootstrap requirejs
```

これで，toodリストを作成するリソースがそろいました．

ただbowerでは，チーム全員がこのコマンドを律儀に打つ必要はありません．

component.jsonにプロジェクトに必要なリソースを記述し，プロジェクト直下に配置することでコマンド一発でリソースのインストールを行うことができます．

今回はこんな感じ

```json
{
  "name" : "Todo list",
  "version" : "1.0.0",
  "dependencies" : {
    "flight": null,
    "mustache": null,
    "bootstrap": null,
    "requirejs": null
  }
}
```

先ほど作ったcomponentsディレクトリを削除してもう一度次のコマンドをうってみてください．

```
$ bower install
```

全てのモジュールが一発でインストールできましたよね．

bowerを使うことで簡単にリソースの準備を行うことができました．

次回，実際にflightによるtodoリストの開発を行っていきます．
