---
layout: post
title: 実行スクリプト入りのgemを作成する方法
---

実行スクリプト入りのgemを作成する手順を紹介する。

まず、bundle gemでgemのテンプレートを作成する。

```
bundle gem test_gem -b
```

作成されるディレクトリの使い分け
* bin: 開発用の実行ファイル
* exe: 公開用の実行ファイル
* lib: ライブラリ。作成するクラスやモジュールを入れる


次に、開発用に動かしたいのでパスを解決した開発用実行ファイルを作成する。

bin/test_gem

```ruby
#!/usr/bin/env ruby
# frozen_string_literal: true

require "bundler/setup"
load File.expand_path("../../exe/test_gem", __FILE__)
```
