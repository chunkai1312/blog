---
title: 將 Travis CI 和 Codecov 整合至 Node.js 專案
tags: [Travis CI, Codecov, NodeJS, GitHub, Code Coverage, CI]
categories: [技術分享]
index_img: /2017/01/15/integrating-travis-ci-and-codecov-into-your-nodejs-projects/cover.jpg
date: 2017-01-15 00:00:00
---

![cover](/2017/01/15/integrating-travis-ci-and-codecov-into-your-nodejs-projects/cover.jpg)

> 最近試著將自己的在 GitHub 上 open source projects 整合 Travis CI 與 Codecob 服務，在此記錄一下實踐的過程。

<!-- more -->

## Travis CI

Travis CI 是許多被託管在 GitHub 上的開源專案使用的持續整合服務。其設定非常簡單，只需要幾個步驟：

- 使用 GitHub 帳號登入 [Travis CI](https://travis-ci.org)。
- Travis CI 會同步你在 GitHub 上的 public 專案，請選擇要使用的 Travis CI 的 repository 。
- 在選擇的專案根目錄下新增 `.travis.yml` 檔案，來定義 CI 流程。

完成以上步驟後，只要有新的 commit 提交到 GitHub 上，就會觸發 Travis CI，以下是一個簡單的例子：

```yml
language: node_js
node_js:
  - "7"
  - "6"
  - "5"
  - "4"
install:
  - npm install
script:
  - npm test
after_success:
  - npm run coverage
```

更多的設定內容可以參考[官方文件](https://docs.travis-ci.com)。

這份設定檔聲明我們測試的環境為 Node.js，並指定版本 v4 ~ v7 。在執行 build 前，會運行 `npm install` 來安裝該專案的依賴模組，然後執行 `npm test` 跑測試腳本。在定義的各版本環境皆需通過測試，否則即 build 失敗。build 完成後，則執行 `npm run coverage` ，整合測試覆蓋率服務。

## Codecov

代碼覆蓋率 (code coverage) 是評估程式原始碼被測試的比例和程度的指標。[Coveralls](https://coveralls.io) 是許多 GitHub 上的開源專案使用的測試覆蓋率服務，不過這裡要介紹的是另外一套 [Codecov](https://codecov.io) ，更為簡單易用：

- 使用 GitHub 帳號登入 [Codecov](https://codecov.io)。
- Codecov 會同步你在 GitHub 上的 public 專案，請選擇要使用的 Codecov 的 repository 。
- 將測試結果上傳至 Codecov 。

在 Node.js 環境下我們需要安裝該模組至專案：

```
$ npm install codecov --save-dev
```

我們使用 [nyc](https://github.com/istanbuljs/nyc) 提供測試報告，這是基於 [istanbul](https://istanbul.js.org) 的代碼覆蓋率工具，支援 [tap](https://github.com/tapjs/node-tap) 、 [mocha](https://github.com/mochajs/mocha) 、 [AVA](https://github.com/avajs/ava) 等常用的 JavaScript 測試框架。 

在 `package.json` 中定義：

```json
{
   "scripts": {
     "test": "nyc mocha test/**/*.js",
     "coverage": "nyc report --reporter=text-lcov > coverage.lcov && codecov"
   }
}
```

我們定義了兩個 script ，執行 `npm test` 使用 mocha 可運行測試並顯示測試報告， `npm run coverage` 則將測試報告上傳至 Codecov。然後在 `.travis.yml` 檔案中加入：

```yml
after_success: npm run coverage
```

如此，當 Travis CI 完成測試腳本並 build 成功後，就會將代碼覆蓋率報告上傳至 Codecov。

## Adding Badges to Your Project

Travis CI 與 Codecov 都有提供狀態圖示 (badge) ，我們可以在該平台上找到提供的語法加入 `README.md` 檔案。不過 Codecov 的提供的 badge 顯示為 `codecov`，想要顯示 `coverage` 可另使用 [Shields.io](http://shields.io) 提供的服務。

以下是使用 [Shields.io](http://shields.io) 提供的 Travus CI 與 Codecov 狀態圖示，將 `USER` 與 `REPO` 換成您的 GitHub 帳號與 repository 名稱，加入 `README.md` 檔案中即可：

```md
[![Build Status][travis-image]][travis-url]
[![Coverage Status][codecov-image]][codecov-url]

[travis-image]: https://img.shields.io/travis/USER/REPO.svg
[travis-url]: https://travis-ci.org/USER/REPO
[codecov-image]: https://img.shields.io/codecov/c/github/USER/REPO.svg
[codecov-url]: https://codecov.io/gh/USER/REPO
```

然後我們就可以在專案的 GitHub 頁面上看到顯示的 `build` 與 `coverage` 狀態：

![](/2017/01/15/integrating-travis-ci-and-codecov-into-your-nodejs-projects/badges.png)
