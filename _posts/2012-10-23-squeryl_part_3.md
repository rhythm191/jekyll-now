---
layout: post
title: Play2.0と学ぶsqueryl (3) ManyToMany
categories: tech
---

前回，前々回とUserクラス，Postクラス,を作成しました．

今回はガイド6にあるCommentクラスを作成することでManyToManyの関連の定義の方法をまとめていきます．

ManyToManyではOneToManyの関連と異なり，PostクラスとCommentクラスとの関連を保持するクラスを生成する必要があります．

それ以外はだいたい一緒

まずはTagクラスを定義します．app/models/Tag.scalaを作成して以下のように記述します．

```scala
package models

import org.squeryl._
import org.squeryl.KeyedEntity
import org.squeryl.PrimitiveTypeMode._

caseclass Tag(name: String) extends KeyedEntity[Long] with Ordered[Tag] {
  val id: Long = 0

  overridedef compare(that: Tag) = this.name.compareTo(that.name)
}
```

次にスキーマのインスタンスにタグのテーブルの定義を行います．

```scala
object YabeDB extends Schema {

  val users = table[User]("user_tb")

  val posts = table[Post]

  val tags = table[Tag]
  // 割愛
```

PostクラスとTagクラスのManyToManyの関連を定義します．

ManyToManyの関連を定義するために，両者のidを保持する関連テーブルのクラスPost2TagAssociationsdを定義して，スキーマのインスタンスにManyToManyの設定を追加します．

```scala
class Post2TagAssociation(val post_id: Long, val tag_id: Long) extends KeyedEntity[CompositeKey2[Long, Long]] {
  def id = compositeKey(post_id, tag_id)
}

object YabeDB extends Schema {

  val postsTags =
    manyToManyRelation(posts, tags).via[Post2TagAssociation]((p, t, pt) => (p.id === pt.post_id, t.id === pt.tag_id))

  // 割愛
```

遅延タグ生成のようなものが欲しいので，タグの取得は常にfindOrCreateByName(String name)ファクトリメソッドを使うことにします．

これをコンパニオンオブジェクトに追加します．

```scala
object Tag {

  def findOrCreateByName(name: String): Tag = inTransaction {
    val tag: Option[Tag] = YabeDB.tags.where(t => t.name === name).headOption
    tag match {
      case Some(t) => t
      case None => YabeDB.tags.insert(Tag(name))
    }
  }
}
```

いよいよPostクラスにTagクラスへの参照を追加します．単方向の関連にしたいのでPostクラスにのみつけます．

ついでに．Postクラスにタグ付けを行うヘルパメソッドtagItWith(name: String)と

指定したタグですべての投稿を検索するメソッドを追加します．

```scala
case class Post(title: String, postedAt: Date, content: String, author_id: Long = 0) extends KeyedEntity[Long] {
  val id: Long = 0

  lazy val tags = YabeDB.postsTags.left(this)

  def tagItWith(name: String): Post = inTransaction {
    this.tags.associate(Tag.findOrCreateByName(name))
    this
  }
}

object Post {

  def findTaggedWith(tag: String): List[Post] = inTransaction {
    import YabeDB._
    from(posts)(p =>
      where(p.id in
        from(postsTags, tags)((pt, t) =>
          where(t.name === tag)
            select (pt.post_id)))
        select (p)).toList
  }

}
```

これらのテストを行う新しいテストケースを作成します．

```scala
package models

import play.api.test._
import play.api.test.Helpers._
import org.specs2.mutable.Specification
import org.squeryl._
import org.squeryl.PrimitiveTypeMode._
import java.util.Date

class TagSpec extends Specification {

  "Tag" should {

    "creat new Tag" in {
      running(FakeApplication()) {

        inTransaction {

          val user: User = YabeDB.users.insert(User("bob@gmail.com", "secret", "Bob", false))

          val bobPost: Post = Post("My first post", new Date(), "Hellow world")
          val anotherBobPost: Post = Post("Hop", new Date(), "Hello world")
          user.posts.associate(bobPost)
          user.posts.associate(anotherBobPost)

          // Well
          YabeDB.tags.size must beEqualTo(0)

          // Tag it now
          bobPost.tagItWith("Red").tagItWith("Blue")
          anotherBobPost.tagItWith("Red").tagItWith("Green")

          // Check
          Post.findTaggedWith("Red").size must beEqualTo(2)
          Post.findTaggedWith("Blue").size must beEqualTo(2)
          Post.findTaggedWith("Green").size must beEqualTo(2)

        }
      }
    }
  }
}
```

Commentクラスは犠牲になったのだ．．．
