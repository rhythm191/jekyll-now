---
layout: post
title: Yeomanのsub-generatorが強力という話
categories: tech
---

Yeomanについて勘違いをしていたのだが、
Yeomanの真の力はボイラープレートの生成ではなく、subgeneratorのファイル生成機能にある。

Railsで例えるなら、
rails new で生成されるボイラープレートよりも
rails generate で生成するファイル生成のワークフローが実際スゴイ。

rails generate では、ファイル名、パス、クラス名、関連のファイルなど機能を追加するために必要なファイル生成をまとめて行ってくれる。
ここで生成されるものは”けっして少なくない”作業だし、規約によって束縛するべき対象だ。
それをやるのがYeomanというわけだ。

試しに、[FLOCSSスタイル](https://github.com/hiloki/flocss)でSassを書く [generator-flocss](https://github.com/rhythm191/generator-flocss) を作成してみた。
このgeneratorはFLOCSSでのファイル生成のワークフローをコントロールする。

例えば、buttonのコンポーネントを作成する場合

```
yo flocss:component button
```

で、`object/compoennt/_button.scss` を生成する。

Sassファイルをまとめてインポートするインポートファイルを生成するジェネレータも用意した。

```
yo flocss:import # _import.scss を生成する
```
