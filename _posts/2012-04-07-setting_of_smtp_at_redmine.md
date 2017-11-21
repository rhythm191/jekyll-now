---
layout: post
title: RedmineでPOP before SMTP で通知メールを送るための設定
---

Redmineは標準でPOP before SMTPに対応していない？し，

ググってみたけど，結局綺麗にヒットするのがなかったので，やり方を保存．

解決方法は，”Mailerモデルを改変してメールを送る前に認証をする”

具体的には，

まず，”app\models\Mailer.rb”の19行目～28行目で接続先のSMTPサーバの設定を行う。

```ruby
require 'net/pop'

ActionMailer::Base.delivery_method = :smtp # デフォルトSMTPらしいのでいらないかも
ActionMailer::Base.smtp_settings = {
  :address => 'smtp.hoge.fuga.com', # SMTPサーバのアドレス
  :port => 25,
  :domain => 'localhost', # メールを送信する自身のドメイン
  :authentication => 'none' # 認証はなし
}
```

さらに，95行目からのreminderメソッド内でPOP認証を行う。

### POP before SMTPの設定
`Net::POP3.auth_only('pop-b.css.fujitsu.com', 110, 'ユーザ名', 'パスワード')``

おわり．
