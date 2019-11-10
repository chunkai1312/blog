---
title: 使用 Docker Runner 建立 GitLab CI 為 Node.js 專案進行持續整合
tags: [GitLab CI, GitLab, Docker, NodeJS, Git, CI]
categories: [技術分享]
index_img: /2017/01/07/gitlab-ci-with-docker-runner-for-nodejs-projects/cover.jpg
date: 2017-01-07 19:02:44
---

![cover](/2017/01/07/gitlab-ci-with-docker-runner-for-nodejs-projects/cover.jpg)

> 本篇文章將介紹如何在 GitLab 上使用 GitLab CI 為 Node.js 應用程式進行持續整合。首先，必須建置好 GitLab 環境，並在一台主機上安裝好 Docker，作為 CI Server 。

<!-- more -->

## Install GitLab Runner

使用 GitLab 官方提供的 Docker 的映像檔 [gitlab/gitlab-runner](https://hub.docker.com/r/gitlab/gitlab-runner/) 安裝 GitLab Runner：

```bash
$ sudo docker run -d --name gitlab-runner --restart always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /srv/gitlab-runner/config:/etc/gitlab-runner \
  gitlab/gitlab-runner:latest
```

確認 Docker Container 是否正常運行：

```bash
$ sudo docker ps
```

## Register GitLab Runner

使用 Docker 安裝好 GitLab Runner 後，我們需要在 GitLab 上註冊這個 Runner 用來跑 CI：

```bash
$ sudo docker exec -it gitlab-runner gitlab-runner register
```

接著會要求輸入必要的配置訊息。其中，會要求輸入 **Registration Token** ， 這可以在 GitLab 的 project 設定下進入 Runners 頁面找到。 

或者，您也可以直接加入參數進行配置：

```bash
$ sudo docker exec -it gitlab-runner gitlab-runner register -n \
  --url https://gitlab.com/ci \
  --registration-token REGISTRATION_TOKEN \
  --executor docker \
  --description "My Docker Runner" \
  --docker-image "node:boron" \
  --docker-volumes /var/run/docker.sock:/var/run/docker.sock
```

這裡我們預設要進行 CI 的環境為 Node.js v6，並且使用官方提供的 Docker 映像檔 `node:boron` 。

配置完畢後，應會在 terminal 上顯示 `Registering runner... succeeded` 。然後在 GitLab 上的 project 底下的 Runners 頁面，可以在 `Available specific runners` 區塊看到啟動的 Runner 。

在該 runner 右側點選按鈕 `Enable for this project` ，我們就可以將此 runner 用在該 project。

## Create a Node.js Application

接著我們會建立一個名為 `gitlab-ci-demo` 的 Node.js 應用程式，來測試 GitLab CI 。首先，我們創建一個目錄，然後配置 `package.json` 檔案：

```json
{
  "name": "gitlab-ci-demo",
  "version": "1.0.0",
  "scripts": {
    "start": "node app.js",
    "test": "nyc --reporter=text-summary mocha test/**/*test.js"
  },
  "dependencies": {
    "express": "^4.14.0"
  },
  "devDependencies": {
    "mocha": "^3.2.0",
    "nyc": "^10.0.0",
    "supertest": "^2.0.1"
  }
}
```

接著安裝依賴套件：

```bash
$ npm install
```

在該目錄下，建立 `app.js` 檔案：

```js
'use strict'

const express = require('express')

const app = express()

app.get('/', (req, res) => {
  res.send('Hello World')
})

app.listen(8080, () => {
  console.log('Running on http://localhost:8080')  
})

module.exports = app
```

這是一個非常簡單的 [express](https://expressjs.com) 範例。完成後，我們試著執行啟動它：

```bash
$ npm start
```

命令列顯示 `Running on http://localhost:8080` 表示已經啟動該 server。

打開瀏覽器，鍵入網址 `http://localhost:8080` ，頁面上應會顯示 `Hello World` 。

## Write Test

在這裡，我們使用 [mocha](https://mochajs.org/) 作為測試框架，以 [supertest](https://github.com/visionmedia/supertest) 來測試 express 應用。

為了撰寫測試，在 project 根目錄下新增 `test` 目錄，然後建立 `app.test.js` 檔案：

```js
'use strict'

const request = require('supertest')
const app = require('../app')

describe('GET /', () => {
  it('should respond "Hello World"', (done) => {
    request(app)
      .get('/')
      .expect(200, 'Hello World', done)
  })
})

```

接著執行測試：

```bash
$ npm test
```

若無意外，應會顯示類似以下訊息，表示通過測試：

```
Running on http://localhost:8080
  GET /
    ✓ should respond "Hello World" (49ms)


  1 passing (59ms)


=============================== Coverage summary ===============================
Statements   : 100% ( 7/7 )
Branches     : 100% ( 0/0 )
Functions    : 100% ( 2/2 )
Lines        : 100% ( 7/7 )
================================================================================
```

## CI Configuration

在 project 根目錄下加入 `.gitlab-ci.yml` 檔案來定義 CI 流程。相關配置可參考[官方文件](https://docs.gitlab.com/ce/ci/yaml/README.html)，以下是一個簡單的範例：

```yml
cache:
  paths:
  - node_modules/

before_script:
  - npm install
  
test:
  script:
   - npm test
```

簡單說明一下這個配置。我們會在跑 script 前，執行 `npm install` 來安裝依賴套件，然後執行 `npm test` 運行測試。如果過程中沒有發生錯誤，並且通過測試，就視為 **build pass** 。

至此，我們已經準備好專案了。我們建立一個簡單的 Node.js 應用程式，並寫好測試，接著就將專案推送至 GitLab 。

如果有在 Runners 設定 `Enable for this project` ，此時就會開始跑 CI ， 然後可以在 GitLab 查看 build 狀態與結果。

![](/2017/01/07/gitlab-ci-with-docker-runner-for-nodejs-projects/build.png)

## Build Status & Coverage Report

最後，我們可以為 project 的 `README.md` 檔案加入 build status 和 test coverage 圖示。

在 GitLab 的 project 設定下進入 CI/CD Pipelines 頁面，在 **Test coverage parsing** 欄位填入 `^Lines\s*:\s*([^%]+)` 。然後將下面 `Build status` 與 `Coverage report` 的 Markdown 語法寫入 `README.md` 檔案。

![](/2017/01/07/gitlab-ci-with-docker-runner-for-nodejs-projects/readme.png)

大功告成。
