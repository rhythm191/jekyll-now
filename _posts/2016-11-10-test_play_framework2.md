---
layout: post
title: play2.5のtestに関するtips
categories: tech
---

play2.5 でテストを書く場合の細かなtipsを記載する。

### CSRFを使ったフォームをテストする場合

CSRFのtokenをフォームに埋め込んでいる場合、リクエストのtagsに代わりのトークンに入れる

```scala
FakeRequest().copyFakeRequest(tags = Map("CSRF_TOKEN" -> "FakeCSRFToken", "CSRF_TOKEN_NAME" -> "csrfToken")
```

### ExcecutionContextが必要な場合

ExcecutionContextは実際のアプリで利用するものにあわせないとmockを作るときに問題が発生する

```scala
// こっちを使う
import play.api.libs.concurrent.Execution.Implicits.defaultContext
// こっちはだめだ
//import scala.concurrent.ExecutionContext.Implicits.global
```

### org.mockito.exceptions.misusing.InvalidUseOfMatchersException がでる。

mockで設定したメソッドのパラメータの形態がおかしい時にでる。
たいていはimplicit paramterを渡していないとか、変なimplicit paramaterが分かっているかのどちらか

### DIする場合

DIする場合は次のようにする。

```scala
val mockMessageApi = (new GuiceApplicationBuilder).injector.instanceOf[MessagesApi]

// または

  "hogehoge" in new WithApplication {
    val mockMessageApi = app.injector.instanceOf[MessagesApi]
```

### mockitoのスタブメソッド

mockitoのスタブメソッドはin の中で定義した場合inの外には影響しない
