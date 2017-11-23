---
layout: post
title: JavaScriptフレームワークFlightでtodoリストを作ってみた Flight編
categories: tech
---

[前回](/flight_part_1)， bowerにより必要なモジュールがそろったので， 今回から実際にTodoリストをくみ上げていきます．

で，できたのがこれ (ソースはこれ. Flightのデモ を参考にしました)

さっそく解説していきたいと思います．

まずは， JavaScriptフレームワークFlightの特徴について簡単に紹介しておきます．

Flightは”コンポーネント”をベースとしたフレームワークです．

この”コンポーネント”の動作は主に次の3つです．

* DOM または，documentオブジェクトにコンポーネントを登録する
* DOM または，documentオブジェクトにイベントを発火させる
* DOM または，documentオブジェクトで発火したイベントをハンドリングし，処理を実行する

というかこれだけ． (他にmixinの機能とか，Loggerとかもあるが…)

Flightでは， コンポーネント同士や画面要素とコンポーネント間の依存性をほとんど排除し，

それらの間でのイベントによるメッセージパッシングでアプリケーションを構築していきます．

これはBackbone.jsやSpine.jsに代表されるMVCフレームワークが明確に役割が定まったモジュールを組み合わせていく方法とはかなり様相が異なります．

では，実際にソースコードをみていきましょう．

まずは，コンポーネントを定義します．

コンポーネントはFlightのライブラリのcomponent.jsに依存します．

コンポーネントは機能を定義したコンストラクタを作成し，defineComponent関数に渡すことで作成します．

```js
define(['components/flight/lib/component'],
  function(defineComponent) {

  return defineComponent(todoItems);

  function todoItems() {}
})
```js

なにもしないコンポーネントができました．

ここからここに”画面描画イベントが発生したら画面にレンダリングする”機能を実装していきます．

まず，イベントが発生したらメソッドを呼び出す部分を作成します．

コンポーネントは初期化時に”initilize”イベントが発生するのでその後に， 上記の機能を持つイベントハンドリングを登録します．

そのとき， afterというメソッドを利用します．

イベントハンドラーの登録はjQueryと大体同じです．

```js
define(['components/flight/lib/component'],
  function(defineComponent) {
    return defineComponent(todoItems);
    function todoItems() {}
  }
});
```

コンポーネントをDOM要素に登録した場合， コンポーネントは登録ElementのjQueryオブジェクトを$nodeプロパティとして保持します．画面描画する際はこの$nodeに対してhtmlの書き換えを行います．

```js
define(['components/flight/lib/component'],
  function(defineComponent) {

  return defineComponent(todoItems);

  function todoItems() {
    // イベントハンドラー
    this.render = function(ev, data) {
      this.$node.html('hove');
    }
    this.after('initialize', function() {
      // uiItemsRender'イベントが発火したらrenderをよびだす．
      this.on(document, 'uiItemsRender', this.render);
    });
  }
});
```

最後にこのコンポーネントをDOMに登録します．

アプリケーションの初期化を行うモジュールで先ほど作ったコンポーネントの登録処理を行います．

登録にはattachToメソッドを使います．

```js
define(['app/components/todo_items'],
  function(TodoItems) {

  function initialize() {
    TodoItems.attachTo('#todo-list');
  }
  return initialize;
});
```

これで， 別のコンポーネントが’uiItemsRender’イベントを発火させると， 画面に描画を行うコンポーネントの作成ができました．

Flightでは，こんな感じで様々なコンポーネントを作成し，イベントによるメッセージパッシングを行っていくことでアプリケーションを作成していきます．

### まとめ， というか感想

Flightではコンポーネントベースの開発により，依存性の少ない再利用可能なパーツを作成できます．

ただ，チームで作成する場合は，コンポーネントの設計・分割方針，イベント名の規約などきっちり押さえておかないと，

BackboneやSpineに比べテキトーに作ると後で悲惨な末路をたどるような懸念を感じます．

その辺は，Flightでの実装例が増えれば解決する問題なのかも知れません．

あるいはMVCと組み合わせるといいのかも？

まだよくわからないのでもう少し，研究してみようと思います．
