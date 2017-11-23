---
layout: post
title: Play2.0と学ぶsqueryl (4) Statelessな関連とStatefulな関連の違い
categories: tech
---

前回，前々回でsquerylでのOneToManyやManyToManyの関連について取り上げました．

今回は関連情報の取得方法に関わる話，statelessな関連とstatefulな関連を取り上げます．

statelessな関連とstatefulな関連では関連情報を参照する際，具体的には前々回のUserクラスにpostsフィールドにアクセスする際，そのときの挙動が異なります．

statelessな関連ではフィールドにアクセスした際に毎回クエリが発行されDBへのアクセスが発生します．

一方，statefulな関連では初めてフィールドにアクセスした際にDBからの取得し，それ以降，その状態を維持します．

statefulな関連は同じフィールドにアクセスする度にDBアクセスが発生しないが，逆に明示的にリフレッシュしないとDBの状態が反映されません．

具体的なプログラムで見ていきましょう．

まず，statelessな関連をもつUserクラスとPostクラスを定義していきます．

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

case class Post(title: String, postedAt: Date, content: String, author_id: Long = 0) extends KeyedEntity[Long] {
  valid: Long = 0
}

object YabeDB extends Schema {

  val users = table[User]("user_tb")

  val posts = table[Post]

  val usersPosts =
    oneToManyRelation(users, posts).via((u, p) => u.id === p.author_id)
}
```

関連情報を取得する検証を行います．

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

    "creat new Post stateless" in {
      running(FakeApplication()) {

        inTransaction {

          valuser: User = YabeDB.users.insert(User("bob@gmail.com", "secret", "Bob", false))

          YabeDB.posts.size must beEqualTo(0)

          valpost: Post = Post("My first post", new Date(), "Hellow world")

          user.posts.associate(post)

          // associateで明示的に関連づけた場合は関連情報を持つpostsフィールドが更新される
          YabeDB.posts.size must beEqualTo(1)
          user.posts.size must beEqualTo(1)

          // Userインスタンスのidを渡してインサートする
          YabeDB.posts.insert(Post("Hex", new Date(), "Hellow world", user.id))

          // statelessな関連はフィールドアクセスごとにクエリを発行するのでDBの状態が反映される
          YabeDB.posts.size must beEqualTo(2)
          user.posts.size must beEqualTo(2)


        }
      }
    }
  }
}
```

statelessな関連ではpostsフィールドにアクセスした際に必ずクエリが発行されるので，DBの状態が必ず反映される．

そのため，2つめのPostクラスをインサートした後も，postsフィールドにDBの状態が反映される．

続いてStatefulな関連を定義します．

statefulな関連ではleftではなく，leftStatefulを使って定義します．

```scala
package models

import org.squeryl.KeyedEntity
import org.squeryl._
import org.squeryl.dsl._
import org.squeryl.PrimitiveTypeMode._

case class User(email: String, password: String, fullname: String, isAdmin: Boolean) extends KeyedEntity[Long] {
  val id: Long = 0

  // Statefulな関連を定義するために left を leftStateful にする
  lazy val posts: StatefulOneToMany[Post] = YabeDB.usersPosts.leftStateful(this)

}
```

検証用のテストを書いてみます．

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

    "creat new Post stateful" in {
      running(FakeApplication()) {

        inTransaction {


          val user: User = YabeDB.users.insert(User("bob@gmail.com", "secret", "Bob", false))          
          YabeDB.posts.size must beEqualTo(0)

          val post: Post = Post("My first post", new Date(), "Hellow world")

          user.posts.associate(post)

          // associateで明示的に関連づけた場合は関連情報を持つpostsフィールドが更新される
          YabeDB.posts.size must beEqualTo(1)
          user.posts.size must beEqualTo(1)

          // Userインスタンスのidを渡してインサートする
          YabeDB.posts.insert(Post("Hex", new Date(), "Hellow world", user.id))

          // statefulな関連は初めてフィールドにアクセスした時の状態を保存するのでDBの状態と一致しない場合がある
          YabeDB.posts.size must beEqualTo(2)
          user.posts.size must beEqualTo(1)

          // 明示的にrefreshをした場合，DBの状態を再取得してフィールドに反映される．
          user.posts.refresh

          YabeDB.posts.size must beEqualTo(2)
          user.posts.size must beEqualTo(2)

        }
      }
    }

  }
}
```

statefulな関連の方は初めてpostsフィールドにアクセスしたときのDBの状態を保持していて，その後にDBが変更されてもpostsフィールドの状態が保持されていることが分かります．

その後，refreshメソッドを呼び出して明示的にDBの状態を再取得した場合，postsフィールドにDBの状態が反映されます．

以上．
