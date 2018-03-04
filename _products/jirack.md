---
layout: product
title: jirack(jira and slack manipulate tool)
date: 2018-03-04
categories: product
---


JIRAをコンソール上から操作しつつ、更新した情報をslackに投げるツールを作りました。

[jirack](https://github.com/rhythm191/jirack)


## なぜ作ったし

JIRAの更新が面倒になったからです。
なんでわざわざマウスでチケットの移動させないといけないんだぁ？と思ったので作りました。
slackのメッセージ投稿は社内で需要がありそうなので作りました。

## 使い方

    $ gem install
    $ jira config # ここでjiraの設定とかslackのwebhookアドレスとかを入力

設定が完了したら

    $ jira list
    $ jira forward 9999 -m "リリースしたよ！"


## 言い訳

config時に聞かれるworkflowのstatus IDのリストなんですが、
現状JIRA REST APIにはworkflowのtransitionsのリストを取得する方法がないため、
ユーザーが気合いでstatusのIDリストを登録してもらう必要があります。

本当に申し訳ない。
