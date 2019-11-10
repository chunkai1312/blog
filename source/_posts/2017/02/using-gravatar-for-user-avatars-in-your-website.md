---
title: 在網站中使用 Gravatar 顯示使用者頭像
tags: [Gravatar, NodeJS]
categories: [技術分享]
index_img: /2017/02/01/using-gravatar-for-user-avatars-in-your-website/cover.jpg
date: 2017-02-01 21:32:55
---

![cover](/2017/02/01/using-gravatar-for-user-avatars-in-your-website/cover.jpg)

> [Gravatar](http://gravatar.com) (Globally Recognized Avatar) 是一個提供跨站台頭像服務的平台，只要您在 [Gravatar](http://gravatar.com) 上註冊並上傳圖片，就可以在所有支援 Gravatar 的網站中顯示你的專屬大頭照。

<!-- more -->

## Set Your Avatar on Gravatar

使用 [Gravatar](http://gravatar.com) 的大頭照服務非常簡單，只需要幾個步驟：

1. 至 [Gravatar](http://gravatar.com) 註冊您的帳號
2. 新增要連結大頭照的 Email
3. 上傳大頭照並選擇要連結的 Email

設定好之後，就可以在所有支援 Gravatar 的網站中顯示你的專屬大頭照了。

## Integrating Gravatar into Your Website

許多網站都擁有會員系統，意謂使用者必須在網站上註冊帳號才能使用服務。一般網站的會員系統，`email` 幾乎是必填的選項，如果您的網站有提供使用者註冊的服務，那麼就可以考慮使用 Gravatar 來顯示使用者的大頭照。整合 Gravatar 優點是顯而易見的，不必自己實作讓使用者上傳圖片的功能，也不用騰出空間來管理這些上傳後的圖片。

如何使用 Gravatar 來顯示使用者的大頭照呢？如果你已經按照上面步驟設置好自己在 Gravatar 上的大頭照，便會發現其實使用者的大頭照與 email 是連結在一起的。我們要找出使用者在 Gravatar 上設置的大頭照，只需要使用者註冊的 email 使用 `MD5` 雜湊函數所產生的雜湊碼，或稱訊息摘要 (message digest) ，然後向以下網址請求即可得到使用者的大頭照：

```
https://www.gravatar.com/avatar/HASH
```

上述網址， `HASH` 就是將 email 字串 hash 後所得到的雜湊碼。如果該使用者已經為該 email 設置大頭照，就會被顯示出來。

![](https://s.gravatar.com/avatar/a770595a74d872ac85e9ae1b12bb67a3)

如果找不到 email 對應的大頭照，則會顯示一張預設的圖片：

![](https://www.gravatar.com/avatar/00000000000000000000000000000000)

如果需要取得不同大小的大頭照圖片，則可加上 `s` 參數：

```
https://www.gravatar.com/avatar/HASH?s=SIZE
```

`SIZE` 即代表大頭照圖片的 `pixel` 大小，這個值可以被設定為 `1 ~ 2048` 。例如，我想要取得 `240px * 240px` 大小的大頭照：

![](https://s.gravatar.com/avatar/a770595a74d872ac85e9ae1b12bb67a3?s=240)

另外需要注意的是，使用 MD5 前的 email 字串首尾不可有空白，並且需要將字元轉換成小寫。否則經過 hash 後會產生出不一樣的結果。因為所有雜湊函式都有一個基本特性，就是如果兩個雜湊碼是不相同的，那麼這兩個雜湊碼的原始輸入也是不相同的，亦即使用者的 email 不相同。

關於 `MD5` 的使用方式，您可以參考 Gravatar 上的 [Developer Resources](https://www.gravatar.com/site/implement/) 頁面提供的各語言產生 MD5 的範例程式碼。以 `Node.js` 為例，我們需要載入 `crypto` 函式庫來使用 `MD5` 雜湊函數：

```js
const crypto = require('crypto')
crypto.createHash('md5').update('user@example.com').digest('hex')
// => 'b58996c504c5638798eb6b511e6f49af'
```

整合 Gravatar 服務非常簡單，您只要在需要顯示使用者大頭照的地方，利用使用者的 email 取得圖片來源，就可以顯示使用者的大頭照了！是不是非常容易呢？

更多用法可以參考 Gravatar 上的 [Image Requests](https://www.gravatar.com/site/implement/images) 說明。
