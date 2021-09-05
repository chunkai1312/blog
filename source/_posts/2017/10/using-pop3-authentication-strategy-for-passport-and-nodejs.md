---
title: 使用 Passport 和 Node.js 的 POP3 身份認證策略
tags: [NodeJS, Passport, POP3, Authentication]
categories: [技術分享]
index_img: /2017/10/16/using-pop3-authentication-strategy-for-passport-and-nodejs/cover.jpg
date: 2017-10-16 12:00:00
---

![](/2017/10/16/using-pop3-authentication-strategy-for-passport-and-nodejs/cover.jpg)

> 通常學校或公司組織都會給予學生或在職員工一組 email 信箱，筆者以前在學校協助系上及實驗室寫內部系統的時候，經常會使用到 POP3 認證。使用 POP3 認證的好處是不需要讓使用者另外設定或記憶一組密碼，也易於與既有內部系統做整合。

<!-- more -->

## Passport POP3 Strategy

[Passport](http://passportjs.org) 是實作 Node.js 應用程式認證機制常使用 Middleware。為了方便使用 POP3 authentication，我寫了一個 [passport-pop3](https://www.npmjs.com/package/passport-pop3) 的 Node.js 模組方便與 Passport 整合。


## Install

透過 [NPM](https://www.npmjs.com) 下載安裝模組：

```
$ npm install --save passport-pop3
```

或使用 [Yarn](https://yarnpkg.com)：

```
yarn add passport-pop3
```

## Usage

與其他 passport strategy 一樣，你需要將 `passport-pop3` strategy 整合至 passport，並提供 `host` 與 `port` 連線至你要認證的 POP3 Server：

```js
passport.use(new POP3Strategy({
  host: 'localhost',
  port: 995
}));
```

預設使用 SSL/TLS 安全連線，如果要關閉它請加入 `enabletls` 選項設為 `false`。

如果你有使用過 [passport-local](https://github.com/jaredhanson/passport-local) 的經驗，應該會知道這個模組預設接收 `req.body` 的 `username` 與 `passport` 這兩個參數。我在 `passport-pop3` 的設定亦是，但也提供可修改設定的選項。如：

```js
passport.use(new POP3Strategy({
  host: 'localhost',
  port: 110,
  enabletls: false,
  usernameField: 'email',
  passwordField: 'passwd',
}));
```

## Example

以 Gmail 為例，以下是一個比較完整的 [Express](http://expressjs.com) 應用程式範例：

```js
const express = require('express')
const bodyParser = require('body-parser')
const passport = require('passport')
const POP3Strategy = require('passport-pop3')

passport.use(new POP3Strategy({ host: 'pop.gmail.com', port: 995 }))

const app = express()

app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
app.use(passport.initialize())

app.post('/login', (req, res, next) => {
  passport.authenticate('pop3', (err, user, info) => {
    if (err) return res.status(500).send('Internal Server Error')
    if (!user) return res.status(400).send('Bad Request')
    return res.status(200).send('OK')
  })(req, res, next)
})

app.listen(3000, () => {
  console.log('Example app listening on port 3000!')
})
```

如果使用上有任何問題及想法，歡迎 pull request 或者聯繫我。