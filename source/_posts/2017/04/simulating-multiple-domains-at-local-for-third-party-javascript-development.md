---
title: 在本地端模擬多個網域名稱以用於第三方 JavaScript 開發
tags: [Third-Party JavaScript, Node.js, JavaScript]
categories: [技術分享]
index_img: /2017/04/03/simulating-multiple-domains-at-local-for-third-party-javascript-development/cover.jpg
date: 2017-04-03 20:00:00
---

![](/2017/04/03/simulating-multiple-domains-at-local-for-third-party-javascript-development/cover.jpg)

> 由於 [Same Origin Policy](https://en.wikipedia.org/wiki/Same-origin_policy) 的安全性考量，在瀏覽器上運行的客戶端 JavaScript 通常會存在跨網域存取資料的限制。當我們在開發第三方 JavaScript (Third-party JavaScript) 應用時，最好能在本地開發環境模擬跨網域 (cross-domain)，避免將來在正式環境上發生無法預期的錯誤。

<!-- more -->

## What is Third-Party JavaScript?

按 *Third-Party JavaScript* 一書的定義，第三方 JavaScript 應有以下特徵：

1. 並非由網站內容提供者所編寫
2. 由不受網站內容提供者控制的外部伺服器提供
3. 程式的目的是作為內容提供者網站的一部分被執行

一言以蔽之，第三方 JavaScript 是從遠端 web server 取得服務，並在網頁發布者 (publisher) 頁面上獨立運行的客戶端程式。以下是第三方 JavaScript 應用的實際例子：

1. Embedded widgets - 嵌入在發布者網頁中的小型交互式應用 (Disqus, Google Maps, Facebook Widget)
2. Analytics and metrics - 收集訪客在發布者網站上的交互數據 (Google Analytics, Flurry, Mixpanel)
3. Web service API wrappers - 用於與外部 Web 服務通信的客戶端應用程式 (Facebook Graph API)

## Configuring Environment for Third-Party JavaScript Development

我們的目標是在伺服器上提供 script files ，並將其加載到發布者 (publisher) 的網站上。為了建立這個環境，我們需要有一個測試頁面來模擬發布者的網站和一個 web server 放置 script files ，然後將測試頁面和 script files 放置在不同的網域名稱下。

### Publisher Test Page

一個發布者的測試頁面會載入並執行第三方 script ，以下是一個簡單的範例：

```html
<!DOCTYPE html>
<html>
<head>
  <title>Publisher Test Page</title>
</head>
<body>
  <h1>Publisher Test Page</h1>
  <!-- script include snippet here -->
</body>
</html>
```

`<script>` 放置的位置會影響網頁載入的速度。一般而言，我們會將第三方 script 放置在 `</body>` 結束標籤之前。

### The Web Server

為了讓測試頁面和第三方 script files 能夠被瀏覽器存取，因此我們需要在本地端運行 web server。可作為網頁伺服器的選擇相當多，您可使用 [Apache](https://httpd.apache.org)、[Nginx](https://www.nginx.com) 這類專門的 web server，或是其他程式語言內置的 web server 作為本地開發環境。

稍後我們會以 **Node.js** 作為 web server 實現一個範例。

### Simulating Multiple Domains

有了網站發布者的測試頁面與網頁伺服器，我們還需要多個不同的網域來提供測試頁面及第三方 script files 的存取。為了模擬多個網域名稱，我們需要修改作業系統的 `hosts` 檔案並建立兩個別名指向本地主機。

在 OS X 與 Unix-based 作業系統上，可以在 `/etc/hosts` 文件找到本機 host 配置；在 Windows 作業系統，嘗試打開 `C:/windows/system32/drivers/etc/hosts` 文件，然後加入以下兩行：

```
127.0.0.1       publisher.dev
127.0.0.1       widget.dev
```

其中，`publisher.dev` 是作為模擬網站提供者的頁面的位址，`widget.dev` 則是放置第三方 script files 的伺服器位址。

## Using Node.js for Example

對於 JavaScript 開發者來說，Node.js 應是相對熟悉的平台。我們使用 [express](https://expressjs.com) 框架作為 web server ，搭建我們的第三方 JavaScript 開發環境。

### Structure and Files

首先，創建一個資料夾 `third-party-js-dev` 作為專案目錄，並建立以下目錄檔案：

```
.
├── static
│   ├── publisher
│   │   └── index.html
│   └── widget
│       └── widget.js
├── test
│   └── server.test.js 
├── server.js
└── package.json
```

`third-party-js-dev/package.json`:
```json
{
  "name": "third-party-js-dev",
  "version": "1.0.0",
  "scripts": {
    "start": "node server.js",
    "test": "mocha test"
  },
  "dependencies": {
    "express": "^4.15.2",
    "vhost": "^3.0.2"
  },
  "devDependencies": {
    "mocha": "^3.2.0",
    "superagent": "^3.5.2",
    "supertest": "^3.0.0"
  }
}
```

請執行 `npm install` 安裝依賴套件。

`third-party-js-dev/server.js`:
```js
const path = require('path')
const express = require('express')
const vhost = require('vhost')

const app = express()

app.use(vhost('publisher.dev', express.static(path.join(__dirname, 'static', 'publisher'))))
app.use(vhost('widget.dev', express.static(path.join(__dirname, 'static', 'widget'))))

app.listen(8080, () => console.log('Server is running'))
```

這裡是程式進入點，也設定了 express 的 middlewares ，主要是針對不同的網域名稱指向不同的靜態資源目錄。

`third-party-js-dev/static/publisher` 目錄下的檔案可以透過 `http://publisher.dev:8080` 被存取；同時在 `third-party-js-dev/static/widget` 目錄下的檔案也可以透過 `http://widget.dev:8080` 被訪問。

`third-party-js-dev/static/publisher/index.html`:
```html
<!DOCTYPE html>
<html>
<head>
  <title>Publisher Test Page</title>
</head>
<body>
  <h1>Publisher Test Page</h1>
  <script src="http://widget.dev:8080/widget.js"></script>
</body>
</html>
```

此為用來模擬 publisher 的測試頁面。

`third-party-js-dev/static/widget/widget.js`:
```js
console.log('Hello World!')
```

`widget.js` 是我們提供的第三方 script file 。 如果在 publisher 的頁面上載入成功，打開瀏覽器 Console 應會顯示 `Hello World!` 。

### Writing Test

我們可以撰寫簡單的測試來驗證我們的環境是否建立成功。

`third-party-js-dev/test/server.test.js`:
```js
const request = require('supertest')
const app = require('../server')

describe('Simulating multiple domains at local', () => {
  it('should respond index.html', () => {
    return request('http://publisher.dev:8080')
      .get('/')
      .expect('Content-Type', /html/)
      .expect(200)
  })

  it('should respond widget.js', () => {
    return request('http://widget.dev:8080')
      .get('/widget.js')
      .expect('Content-Type', /javascript/)
      .expect(200)
  })
})
```

請執行 `npm test` 跑測試。如果通過測試，表示您的設定沒有問題。

### Starting the Server and Opening Your Browser

執行 `npm start` 啟動 web server ，打開瀏覽器並在網址列上輸入 `http://publisher.dev:8080` 進入測試頁面。

![](/2017/04/03/simulating-multiple-domains-at-local-for-third-party-javascript-development/publisher_test_page.png)

以 Google Chrome 為例，開啟**開發人員工具**，Console 上顯示 `Hello World!` 則大功告成。

您可以在這個 [repo](https://github.com/chunkai1312/third-party-js-dev) 找到完整的程式範例。

## Reference

[Third-Party JavaScript](https://www.manning.com/books/third-party-javascript)
