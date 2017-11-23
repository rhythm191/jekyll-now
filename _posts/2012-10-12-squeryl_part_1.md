---
layout: post
title: Play2.0と学ぶsqueryl (1) モデルの定義
categories: tech
---

1.2.5のときのチュートリアルにあったYABE(Yet Another Blog Engine)のモデル層の作成を通じて，

ScalaのORマッパーであるsquerylの使い方をまとめておきます

利用した環境は

* Play 2.0.5
* Scala 1.9
* squeryl 0.9.5-2

なお，squeryl 0.9.6以降では仕様が変わるようなので動かなくなりそうです．

(KeyedEntityがなくなりKeyedEntityDefで自動採番するらしい(()))
セットアップ

squerylを使うためにsbtのセットアップをします．

project/Build.scalaにsquerylの依存関係を記述します．

```scala
valappDependencies = Seq(
      "org.squeryl" %% "squeryl" % "0.9.5"
)
```

今回はインメモリのh2データベースを利用します．

conf/application.confに以下の記述をします．

```scala
db.default.driver=org.h2.Driver
db.default.url="jdbc:h2:mem:play"
```

最後にsquerylデータベースを接続の設定を行います．

app/Global.scalaを新規作成して，Playの起動時にDBの接続設定を行う処理をグローバルクラスに記述します．

```scala
import org.squeryl.Session
import org.squeryl.SessionFactory
import org.squeryl.adapters.H2Adapter
import org.squeryl.adapters.PostgreSqlAdapter
import org.squeryl.internals.DatabaseAdapter
import org.squeryl.PrimitiveTypeMode._

import play.api.Application
import play.api.GlobalSettings
import play.api.db.DB

import models._

object Global extends GlobalSettings {

  override def onStart(app: Application) {
    SessionFactory.concreteFactory = app.configuration.getString("db.default.driver") match {
      case Some("org.h2.Driver") => Some(() => getSession(new H2Adapter, app))
      case _ => sys.error("Database driver must be either org.h2.Driver")
    }

  }

  def getSession(adapter: DatabaseAdapter, app: Application) = Session.create(DB.getConnection()(app), adapter)

}
```

Userエンティティの作成

Playのチュートリアルと同様にUserクラスを作成するところからブログエンジンのコーディングを始めます．

app/models/User.scalaを作成し，以下のように記述します．

```scala
package models

import org.squeryl.KeyedEntity
import org.squeryl._
import org.squeryl.dsl._
import org.squeryl.PrimitiveTypeMode._

case class User(email: String, password: String, fullname: String, isAdmin: Boolean) extends KeyedEntity[Long] {
  val id: Long = 0
}
```

各フィールド + プライマリーキーとなるidを定義します．(なんでかidに自動採番された値が入る．きもい)

このクラスとは別に，UserクラスをsquerylがDBへマッピングを行うスキーマのオブジェクトを作成します．

app/models/YabeDB.scalaを作成して以下のように記述します．

```scala
package models

import org.squeryl.PrimitiveTypeMode._
import org.squeryl._
import org.squeryl.annotations.Column
import org.squeryl.dsl._

object YabeDB extends Schema {

  val users = table[User]("user_tb")
}
```

userはDBのキーワードとなる可能性があるのでここでは”user_tb”というテーブルにUserクラスをマッピングするように定義しました．

DBの作成はevolutionの機能を使った方がベターだろうが，ここでは作業を簡単にするためにsquerylの機能を使ってDBのテーブルを作成します．

そのために，app/Global.scalaを以下のように変更します．

```scala
import org.squeryl.Session
import org.squeryl.SessionFactory
import org.squeryl.adapters.H2Adapter
import org.squeryl.adapters.PostgreSqlAdapter
import org.squeryl.internals.DatabaseAdapter
import org.squeryl.PrimitiveTypeMode._

import play.api.Application
import play.api.GlobalSettings
import play.api.db.DB

import models._

object Global extends GlobalSettings {

  override def onStart(app: Application) {
    SessionFactory.concreteFactory = app.configuration.getString("db.default.driver") match {
      case Some("org.h2.Driver") => Some(() => getSession(new H2Adapter, app))
      case Some("org.postgresql.Driver") => Some(() => getSession(new PostgreSqlAdapter, app))
      case _ => sys.error("Database driver must be either org.h2.Driver or org.postgresql.Driver")
    }

    transaction {
      // テーブルの削除
      YabeDB.drop
      // テーブルの作成
      YabeDB.create
    }

  }

  def getSession(adapter: DatabaseAdapter, app: Application) = Session.create(DB.getConnection()(app), adapter)

}
```

squerylでトランザクションを利用したい場合はtransactionメソッドを利用します．

transactionメソッドに渡すブロック内に処理を書きます．

あと，チュートリアル通り，Userクラスに関する最初のテストも作成します．

test/models/UserSpec.scalaに以下のように記述します．

```scala
package models

import play.api.test._
import play.api.test.Helpers._
import org.specs2.mutable.Specification
import org.squeryl._
import org.squeryl.PrimitiveTypeMode._

class UserSpec extends Specification {

  "User" should {

    "creat new User" in {
      running(FakeApplication()) {

        transaction {
          val user = YabeDB.users.insert(User("bob@gmail.com", "secret", "Bob", false))

          user must not be null
          user.fullname must beEqualTo("Bob")
        }
      }
    }

  }

}
```

OneToManyなどのレーションシップの関係は次回紹介します．

### 参考文献

* [Squeryl – A Scala ORM for SQL Databases](http://squeryl.org/)
* [Squeryl の KeyedEntity がオワコンになっていた – tototoshiの日記](http://d.hatena.ne.jp/tototoshi/20120811/1344668593)
