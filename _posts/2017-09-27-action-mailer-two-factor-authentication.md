---
layout: post
title: "ActionMailer Two-Factor Authentication"
tags: [web, rails]
---

紀錄設定使用 Gmail 寄信時，因為寄信帳號使用二階段驗證，需要的額外設定步驟。

<!--more-->

## 在 development.rb 裡加入寄信的設定

``` ruby
  config.action_mailer.delivery_method = :smtp
  config.action_mailer.default_url_options = { host:"http://localhost:3000" }
  config.action_mailer.smtp_settings = {
    :address => "smtp.gmail.com",
    :port => "587",
    :domain => "gmail.com",
    :authentication => "plain",
    :user_name => "我的帳號@gmail.com",
    :password => "我的密碼",
    :enable_starttls_auto => true
  }
```

## 建立Mailer寄信程式，並嘗試寄給自己

`rails generate mailer UserMailer notify_comment`

``` ruby
# user_maiiler.rb
class UserMailer < ActionMailer::Base
  default :from => "我的帳號@gmail.com"

  def notify_comment
    mail(:to => "我的帳號@gmail.com", :subject => "New Comment")
  end
end
```

## 寄信
``` shell
rails c
UserMailer.notify_comment.deliver_now!
```

出現錯誤 `Net::SMTPAuthenticationError: 534-5.7.9 Application-specific password required.`

## 解決方式

這是該帳號使用了二階段驗證。如果要用 Gmail 寄信，必須先從 Google 設定 App Password。

以下是解法網址  [Sign in using App Passwords](https://support.google.com/accounts/answer/185833?hl=en)  和  [設定App Password的網址](https://security.google.com/settings/security/apppasswords)。

得到密碼後，直接取代掉 development.rb 中的 `:password => "我的密碼"` 即可。
