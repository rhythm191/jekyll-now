---
layout: post
title: redmineの移行記録
---

自宅のサーバをさくらVPSに移行中(※暑いから

redmineの移行の仕方をメモっておく．

基本はここの通りにしておけばOK
新サーバにRedmineをインストール

こことか見ておいてよ．
新サーバに旧サーバのデータを移行する

まずは，mysqlからデータを吸い出し．

redmineという名前のデータベースからデータを吸い出し，redmine.sqlに保存する

```
mysqldump -u root -p redmine &amp;#62; redmine.sql
```

このファイルを新サーバに持っていき，次のコマンドでデータを適用する

(redmineという名前のデータベースへ適応する事を想定)

```
mysql -u root -p redmine &amp;#60; redmine.sql
```

データベース更新の通知

あとは，redmineにデータベース更新を促してやる．

```
rake config/initializers/session_store.rb
rake db:migrate RAILS_ENV=&amp;#34;production&amp;#34;
```

これで，データが移行されているはずだ．
