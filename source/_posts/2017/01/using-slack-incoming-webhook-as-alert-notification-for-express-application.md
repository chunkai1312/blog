---
title: 使用 Slack Incoming Webhook 為 Express 應用程式建立警報通知
date: 2017-01-21 21:11:40
tags:
  - Slack
  - NodeJS
  - Express
categories:
  - 工作筆記
---

有時候我們可能需要檢查應用程式的錯誤日誌，來確認錯誤的發生原因。[Slack](https://slack.com) 作為團隊內部的溝通工具非常方便，其 [Incoming Webhooks](https://punwave.slack.com/apps/A0F7XDUAZ-incoming-webhooks) 提供 API 可以直接向 Slack Channel 發送訊息。本文將介紹如何使用 [Slack Incoming Webhooks API](https://api.slack.com/incoming-webhooks) 為 Express 應用程式提供即時的錯誤警告通知服務。

<!-- more -->

## Using Slack's Incoming Webhooks API

為了使用 Slack 的 Incoming Webhooks API，我們需要以下幾個步驟：

- 請至 [Incoming WebHooks](https://slack.com/apps/A0F7XDUAZ-incoming-webhooks) 頁面，如果尚未登入 Slack ，請點選 `Sign in to install` 。
- 點選 `Add Configuration` ，可以選擇既有的 Channel，也可以選擇 **create a new channel** 。然後按下 `Add Incoming WebHooks integration` 。
- 設置 Incoming WebHooks ，選項很多，可依個人喜好進行配置。最重要的是取得 `Webhook URL` 。完成後請點選 `Save Settings` 。

到這裡，我們的設定就已經完成了，您可以從官方的 [Slack API](https://api.slack.com/incoming-webhooks) 文件查看詳細的使用說明。

## Sending Express Server Error to Slack

[Express](http://expressjs.com) 目前是 Node.js 使用最廣泛的 Web 開發框架，這裡就不多做介紹。為求方便，我已經實作 [express-error-slack](https://github.com/chunkai1312/express-error-slack) 模組，使用方式非常簡單，只要掛上 middleware ，設定剛剛從 Slack 上取得的 `Webhook URI` 即可，以下是一個簡單的範例：

```js
const express = require('express')
const errorToSlack = require('express-error-slack')

const app = express()
// some middlewares here
app.use(errorToSlack({ webhookUri: 'YOUR_SLACK_WEBHOOK_URI' }))
// final middleware here

app.listen(3000, function () {
  console.log('Example app listening on port 3000!')
})
```

啟動 Express Server 後，只要發生 Http Status 4xx 或 5xx 的錯誤，就會將錯誤的 call stack 以及 request log，立即發送至所設定的 Slack Channel。以下為結果範例：

- 4xx
![](/2017/01/21/using-slack-incoming-webhook-as-alert-notification-for-express-application/4xx.png)

- 5xx
![](/2017/01/21/using-slack-incoming-webhook-as-alert-notification-for-express-application/5xx.png)

如果使用上有任何問題及想法，歡迎 Pull Request 或者聯繫我。

更新：新增一個 `option.skip` 的可選自訂函式，以便決定是否忽略錯誤或將錯誤發送至 Slack。

## References
- [Slack API - Incoming Webhooks](https://api.slack.com/incoming-webhooks)
- [express-error-slack](https://github.com/chunkai1312/express-error-slack#express-error-slack)