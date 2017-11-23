---
layout: post
title: Play2.0と学ぶsqueryl (2) OneToManyの関係の定義
categories: tech
---

前回，Play1.xのチュートリアルにそってYABEのUserクラスを定義しました．

今回はPostクラスの実装を通じて，squerylでのOneToManyの関連の定義の仕方を学びます．

まずはPostクラスを定義します．app/models/Post.scalaを作成して以下のように記述します．

```scala
package models

import org.squeryl.KeyedEntity
import org.squeryl._
import org.squeryl.dsl._
import org.squeryl.PrimitiveTypeMode._
import java.util.Date

case class Post(title: String, postedAt: Date, content: String, author_id: Long = 0) extends KeyedEntity[Long] {
  val id: Long = 0
}
```

スキーマのインスタンスであるYabeDB.scalaも修正します．

```scala
package models

import org.squeryl.PrimitiveTypeMode._
import org.squeryl._
import org.squeryl.annotations.Column
import org.squeryl.dsl._

object YabeDB extends Schema {

  val users = table[User]("user_tb")

  val posts = table[Post]

  on(posts)(p => declare(
    p.content is (dbType("text"))))

}
```

onメソッドによってインデックスやユニークキー，DBの定義を細かく指定することができます．

ここでは，@Lob アノテーションを使っていた部分をdbType関数を利用して、投稿の内容を保持する大きな文字列型データベースを使用します．
OneToManyの関連

UserクラスとPostクラスの関連情報を定義します．

それぞれのPostは1つのUserによって所有され、各Userは複数のPostインスタンスを所有することができるという関連情報を持たせます．

squerylではstatelessな関連とstatefulな関連を持つことができます．ここでは関連情報の定義の方法だけに注力するためにstatelessな関連の定義だけやります．

statelessとstatefulの違いは次回以降で解説します(すると思います)．

まずはスキーマのインスタンスに関連情報を定義するためにYabeDB.scalaを修正します．

```scala
package models

import org.squeryl.PrimitiveTypeMode._
import org.squeryl._
import org.squeryl.annotations.Column
import org.squeryl.dsl._

object YabeDB extends Schema {

  val users = table[User]("user_tb")

  val posts = table[Post]

  on(posts)(p => declare(
    p.content is (dbType("text"))))

  val usersPosts =
    oneToManyRelation(users, posts).via((u, p) => u.id === p.author_id)

}
```

スキーマのインスタンスにoneToManyRelationメソッドを利用して，インスタンス同士のどのフィールドによって関連付けを行うかを定義します．

UserクラスとPostクラスからそれぞれの情報が取得できるようにフィールドを定義します．

```scala
package models

import org.squeryl.KeyedEntity
import org.squeryl._
import org.squeryl.dsl._
import org.squeryl.PrimitiveTypeMode._

case class User(email: String, password: String, fullname: String, isAdmin: Boolean) extends KeyedEntity[Long] {
  val id: Long = 0

  lazy val posts: OneToMany[Post] = YabeDB.usersPosts.left(this)

}
```

```scala
package models

import org.squeryl.KeyedEntity
import org.squeryl._
import org.squeryl.dsl._
import org.squeryl.PrimitiveTypeMode._
import java.util.Date

case class Post(title: String, postedAt: Date, content: String, author_id: Long = 0) extends KeyedEntity[Long] {
  val id: Long = 0

  lazy val user: ManyToOne[User] = YabeDB.usersPosts.right(this)

}
```

もちろんテストも書きます．

UserとPostとの関連付けはassociateメソッドを使います．

associateメソッドを使うことにより，PostインスタンスへUserインスタンスの関連するidを設定できるとともにDBへの保存も行われます．

```scala
package models

import play.api.test._
import play.api.test.Helpers._
import org.specs2.mutable.Specification
import org.squeryl._
import org.squeryl.PrimitiveTypeMode._
import java.util.Date

class PostSpec extends Specification {

  "Post" should {

    "creat new Post" in {
      running(FakeApplication()) {

        inTransaction {

          val user: User = YabeDB.users.insert(User("bob@gmail.com", "secret", "Bob", false))

          YabeDB.posts.size must beEqualTo(0)

          val post: Post = Post("My first post", new Date(), "Hellow world")

          // UserとPostを関連づける
          user.posts.associate(post)

          YabeDB.posts.size must beEqualTo(1)

          val bobPosts: List[Post] = YabeDB.posts.where(p => p.author_id === user.id).toList

          bobPosts.size must beEqualTo(1)

          // Postインスタンスを直接とってきたものの検証
          val firstPost = bobPosts(0)

          firstPost must not be null
          firstPost.author_id must beEqualTo(user.id)
          firstPost.title must beEqualTo("My first post")
          firstPost.content must beEqualTo("Hellow world")

          // Userインスタンスから取得したPostインスタンスの検証
          val bob: User = YabeDB.users.where(u => u.id === user.id).head
          val firstPost2: Post = bob.posts.toList(0)

          firstPost2 must not be null
          firstPost2.author_id must beEqualTo(user.id)
          firstPost2.title must beEqualTo("My first post")
          firstPost2.content must beEqualTo("Hellow world")

        }
      }
    }
  }
}
```

次回はManyToManyとかstateless, statefulな関係の違いとかをまとめる予定．
