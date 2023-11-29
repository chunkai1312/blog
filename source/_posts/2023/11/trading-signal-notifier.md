---
title: NestJS x Fugle API x LINE Notify：打造股票交易訊號通知系統
tags: [NodeJS, NestJS, Fugle API, LINE Notify]
categories: [技術分享]
index_img: /2023/11/28/trading-signal-notifier/cover.png
date: 2023-11-28 00:00:00
---

![](/2023/11/28/trading-signal-notifier/cover.png)

## 前言

在瞬息萬變的金融市場中，抓住投資機會並應對風險至關重要，你可能已經擁有自己的交易策略，但要時刻關注市場動態並非易事，因此需要一套可靠行情監控工具，幫助你隨時掌握市場的脈動。

<!-- more -->

打造一個量身定制的行情監控系統，不僅能節省大量盯盤的時間，還能根據你的投資風格和目標，設定警示和提醒，更精準地捕捉符合策略的市場事件。這個系統能追蹤行情變化，即時地向你發送通知，讓你專注於更深入的投資研究，進而制定相應的交易策略。

許多投資者選擇運用技術指標來決定進出場時機，在坊間流行一種以 **KD 指標** 來操作 **元大台灣50**（0050） 的交易策略。KD（KDJ）是一種擺盪指標，它是由隨機指標（Stochastic Oscillator）演變而來，由三個線構成：K 線（快速隨機線）、D 線（慢速隨機線）和 J 線。KD 指標的主要功能在於判斷市場的超買和超賣狀態，並提供進出場的參考。例如：

- 當 K 值大於 80 時，表示市場處於超買狀態，投資者可以考慮賣出。
- 當 K 值小於 20 時，表示市場處於超賣狀態，投資者可以考慮買進。

本篇文章將以 NestJS 運用 Fugle API 及 LINE Notify 來打造股票進出場訊號通知系統，並以 **元大台灣50** 為例，使用 KD 指標作為進出場的參考，並在每個交易日的 `13:25` 時發送通知。這個時間點可供投資者運用收盤前 5 分鐘進行試撮期間，以便在考慮是否進行交易。

| WARNING: |
| :--- |
| 本篇文章僅作為範例說明，而非投資建議。 |

## 目錄

- [事前準備](#事前準備)
  - [安裝 Node.js](#安裝-nodejs)
  - [安裝 Nest CLI](#安裝-nest-cli)
  - [取得 Fugle API 金鑰](#取得-fugle-api-金鑰)
  - [取得 LINE Notify 存取權杖](#取得-line-notify-存取權杖)
- [設定應用程式](#設定應用程式)
  - [建立 Nest 應用程式](#建立-nest-應用程式)
  - [安裝依賴模組](#安裝依賴模組)
  - [設定環境變數](#設定環境變數)
- [實作通知服務](#實作通知服務)
  - [匯入模組與依賴注入](#匯入模組與依賴注入)
  - [初始化應用程式與 K 線數據](#初始化應用程式與-k-線數據)
  - [定時取得即時報價](#定時取得即時報價)
  - [發送 LINE Notify 通知](#發送-line-notify-通知)
  - [執行應用程式](#執行應用程式)
- [Bonus：容器化應用程式部署](#bonus容器化應用程式部署)
  - [容器化技術與 Docker](#容器化技術與-docker)
  - [準備 Docker 環境](#準備-docker-環境)
  - [建構 Docker 映像檔](#建構-docker-映像檔)
  - [使用 Docker Compose 執行容器化應用程式](#使用-docker-compose-執行容器化應用程式)

## 事前準備

在開始實作前，請先準備好你的開發環境，正如俗話說：「工欲善其事，必先利其器。」

安裝開發環境時，請留意不同作業系統和版本之間的差異，為確保安裝工具能順利運作，請先確認你的本機執行環境，詳細閱讀官方文件和教學後，再進行安裝及相關設定。

### 安裝 Node.js

**Node.js** 是基於 Chrome V8 JavaScript 引擎的開放原始碼、跨平台、可用於伺服器端應用程式的執行環境，它提供事件驅動、非阻塞的I/O 模型，讓你能有效率地建立可擴展的網路應用程式。

<figure>
  <img src="./nodejs.png" width="320" alt="圖 1：Node.js" align="center">
  <figcaption><em>圖 1：Node.js</em></figcaption>
  <br><br>
</figure>

首先，你需要安裝 Node.js 環境，請前往 Node.js [官方網站](https://nodejs.org) 下載適合你作業系統的安裝檔，通常建議選擇 LTS（Long Term Support）版本，這是官方提供長期支援的穩定版本。當然，如果你想體驗 Node.js 最新功能，也可以選擇下載最新版。

<figure>
  <img src="./nodejs-official-site.png" alt="圖 2：Node.js 官方網站" align="center">
  <figcaption><em>圖 2：Node.js 官方網站</em></figcaption>
  <br><br>
</figure>

| TIP: |
| :--- |
| 你還可以使用 [NVM](https://github.com/nvm-sh/nvm)（Node Version Manager）這個版本管理工具來安裝 Node.js，它能讓你輕鬆切換不同版本的 Node.js。NVM 也有 [Windows](https://github.com/coreybutler/nvm-windows) 版本，你可以根據作業系統環境來進行安裝。 |

### 安裝 Nest CLI

**Nest**（NestJS）是基於 Node.js 和 TypeScript 開發的框架，能幫助你打造高效、可靠且易於擴展的應用程式，它提供了多種實用功能，支援常用的伺服端技術。透過模組化的結構，你能更方便地管理和組織程式碼。

<figure>
  <img src="./nestjs.png" width="248" alt="圖 3：NestJS" align="center">
  <figcaption><em>圖 3：NestJS</em></figcaption>
  <br><br>
</figure>

Nest CLI 是由 NestJS 提供的命令列工具，能讓你輕鬆地建立、執行和管理 Nest 應用程式的各種操作。只要你已安裝好 Node.js，打開終端機並執行以下指令，即可安裝 Nest CLI：

```sh
$ npm install -g @nestjs/cli
```

安裝完成後，你可以輸入以下指令，查看 Nest CLI 提供的指令及其使用方式：

```sh
$ nest -h
```

<figure>
  <img src="./nest-cli.png" alt="圖 4：Nest CLI 指令列表" align="center">
  <figcaption><em>圖 4：Nest CLI 指令列表</em></figcaption>
  <br><br>
</figure>

### 取得 Fugle API 金鑰

在使用 Fugle API 之前，你必須註冊成為富果會員。請至富果網站完成會員註冊並且登入後 ，然後進行以下步驟。

**STEP 1**：前往富果帳戶開發者網站首頁（developer.fugle.tw），點選「文件」→「行情」。

<figure>
  <img src="./fugle-api-1.png" alt="圖 5" align="center">
  <figcaption><em>圖 5</em></figcaption>
  <br><br>
</figure>

**STEP 2**：跳轉頁面後，在右上方點選「金鑰申請」。

<figure>
  <img src="./fugle-api-2.png" alt="圖 6" align="center">
  <figcaption><em>圖 6</em></figcaption>
  <br><br>
</figure>

**STEP 3**：「金鑰申請及管理」頁面下，即可新增行情 API 金鑰。

<figure>
  <img src="./fugle-api-3.png" alt="圖 7" align="center">
  <figcaption><em>圖 7</em></figcaption>
  <br><br>
</figure>

取得 API 金鑰之後，即可開始使用富果行情 API。不同的 API 方案下，有不同的存取限制，請參考官方網站的 [說明](https://developer.fugle.tw/docs/pricing)。

### 取得 LINE Notify 存取權杖

請確認你已經註冊並認證了你的 LINE 帳號。如果沒有，請先在你的行動裝置下載 LINE App 來完成註冊和認證。

**STEP 1**：前往 LINE Notify 首頁（notify-bot.line.me），登入你的 LINE 帳號後，點選「個人頁面」。
 
<figure>
  <img src="./line-notify-1.png" alt="圖 8" align="center">
  <figcaption><em>圖 8</em></figcaption>
  <br><br>
</figure>

**STEP 2**：跳轉頁面後，選擇「發行權杖」。
 
<figure>
  <img src="./line-notify-2.png" alt="圖 9" align="center">
  <figcaption><em>圖 9</em></figcaption>
  <br><br>
</figure>

| NOTE: |
| :--- |
| LINE Notify 授權是基於 OAuth 2.0 的授權碼（Authorization Code）模式。這種授權機制能讓你的應用程式能夠安全地取得其他使用者的同意。如果你只需要將訊息透過 LINE Notify 推播給自己，則直接選擇「發行權杖」即可。 |

**STEP 3**：接著會跳出一個表單視窗。請填寫權杖名稱，然後接收通知的聊天室請選擇「透過1對1聊天接收Line Notify的通知」，然後點選「發行」。
 
<figure>
  <img src="./line-notify-3.png" alt="圖 10" align="center">
  <figcaption><em>圖 10</em></figcaption>
  <br><br>
</figure>

**STEP 4**：LINE Notify 將產生你的個人存取權杖（Access Token）。因為這段代碼只會出現一次，請務必記住這組權杖代碼。
 
<figure>
  <img src="./line-notify-4.png" alt="圖 11" align="center">
  <figcaption><em>圖 11</em></figcaption>
  <br><br>
</figure>

**STEP 5**：完成後，在「連動的服務」清單裡，就會出現我們剛剛所設定的服務。

<figure>
  <img src="./line-notify-5.png" alt="圖 12" align="center">
  <figcaption><em>圖 12</em></figcaption>
  <br><br>
</figure>

## 設定應用程式

### 建立 Nest 應用程式

首先，請打開終端機，使用 Nest CLI 建立一個名為 `trading-signal-notifier` 的 Nest 應用程式：

```sh
$ nest new trading-signal-notifier
```

應用程式建立後，我們需要調整 Nest CLI 預設產生的內容。請將應用程式 `AppModule` 修改如下：

```ts
import { Module } from '@nestjs/common';

@Module({})
export class AppModule {}
```

| NOTE: |
| :--- |
| 我們不會用上預設建立的 `AppController` 與 `AppService`，你可以移除相關檔案。 |

### 安裝依賴模組

請在終端機輸入以下指令安裝相關套件：

```sh
$ npm install --save @fugle/marketdata @fugle/marketdata-nest @nestjs/config @nestjs/schedule kdj luxon nest-line-notify numeral
$ npm install --save-dev @types/luxon @types/numeral
```

以下是各個套件的簡要說明： 

- `@fugle/marketdata`: 富果行情 API 客戶端函式庫。
- `@fugle/marketdata-nest`: 提供在 NestJS 應用程式中整合 `@fugle/marketdata` 的模組。 
- `@nestjs/config`: NestJS 的配置模組，可用於管理應用程式中的配置參數。 
- `@nestjs/schedule`: NestJS 中的任務調度模組，允許你在應用程式中定義和管理定期執行的任務，例如排程任務、定時執行等。 
- `nest-line-notify`: 在 NestJS 應用程式中整合 Line Notify 服務的套件，可用於發送 Line 通知。 
- `kdj`: 用於計算 KDJ 指標。你也可以使用其他技術指標套件，例如 `technicalindicators` 或 `tulind`。
- `luxon`: 用於處理和解析日期和時間的工具。 
- `numeral`: 用於格式化數值型態資料。 
- `@types/luxon`: 這是 `luxon` 的 TypeScript 類型定義檔。 
- `@types/numeral`: 這是 `numeral` 的 TypeScript 類型定義檔。

安裝完成後，請在 `AppModule` 中匯入相關模組：

```ts
import { Module } from '@nestjs/common';
import { ConfigModule } from '@nestjs/config';
import { ScheduleModule } from '@nestjs/schedule';
import { FugleMarketDataModule } from '@fugle/marketdata-nest';
import { LineNotifyModule } from 'nest-line-notify';

@Module({
  imports: [
    ConfigModule.forRoot(),
    ScheduleModule.forRoot(),
    FugleMarketDataModule.forRoot({
      apiKey: process.env.FUGLE_MARKETDATA_API_KEY,
    }),
    LineNotifyModule.forRoot({
      accessToken: process.env.LINE_NOTIFY_ACCESS_TOKEN,
    }),
  ],
})
export class AppModule {}
```

### 設定環境變數

請在專案目錄下建立 `.env` 檔案，新增以下內容：

```
FUGLE_MARKETDATA_API_KEY=
LINE_NOTIFY_ACCESS_TOKEN=
```

這裡解釋上述變數的意義與用途：

- `FUGLE_MARKETDATA_API_KEY`：你的富果行情 API 金鑰。
- `LINE_NOTIFY_ACCESS_TOKEN`：你的 LINE Notify 存取權杖。

應用程式將透過環境變數來讀取富果行情 API 金鑰以及 LINE Notify 存取權杖。

## 實作通知服務

完成應用程式的設定後，我們要實作應用程式的核心功能。首先，建立一個模組來實作 LINE Notifier 通知功能。請使用 Nest CLI 執行以下指令來建立 `NotifierModule`：

```sh
$ nest g module notifier
```

執行上述指令後，Nest CLI 會在專案的 `src` 目錄下新增一個名為 `notifier` 的資料夾，並在其中建立 `notifier.module.ts` 檔案。

接下來，請在 `NotifierModule` 下新增 `NotifierService`，這是用於實現 LINE Notifier 通知的核心服務，你可以使用以下 Nest CLI 指令來完成這個步驟：

```sh
$ nest g service notifier --no-spec
```

執行後，請開啟建立的檔案，並完成以下的實作。

### 匯入模組與依賴注入

請在 `NotifierService` 建構式中注入 `@fugle/marketdata` 提供的 `RestClient` 和 `nest-line-notify` 提供的 `LineNotify`。這樣我們就可以使用這兩個服務來獲取股票行情資料並且發送 LINE Notify 訊息。

```ts
import * as numeral from 'numeral';
import * as kdj from 'kdj';
import { DateTime } from 'luxon';
import { Injectable, Logger } from '@nestjs/common';
import { Cron } from '@nestjs/schedule';
import { RestClient } from '@fugle/marketdata';
import { InjectRestClient } from '@fugle/marketdata-nest';
import { InjectLineNotify, LineNotify } from 'nest-line-notify';

@Injectable()
export class NotifierService {
  private readonly symbol = '0050';   // 股票代號
  private candles: Record<string, any>;

  constructor(
    @InjectRestClient() private readonly client: RestClient,
    @InjectLineNotify() private readonly lineNotify: LineNotify,
  ) { }
}
```

### 初始化應用程式與 K 線數據

然後，我們在應用啟動時調用 `onApplicationBootstrap()` 方法，該方法會執行 `initCandles()` 方法，用於初始化歷史 K 線數據。

當 `initCandles()` 方法被呼叫時，使用富果行情 API 獲取指定股票代號的最近三個月的歷史 K 線數據，然後將其保存在 `this.candles` 屬性中。

此外，`initCandles()` 使用 `@Cron()` 裝飾器設定為定時任務，這個方法將會在每日上午 `08:00` 時自動執行。

```ts
...
@Injectable()
export class NotifierService {
  ...
  async onApplicationBootstrap() {
    await this.initCandles();
  }

  @Cron('0 0 8 * * *')
  async initCandles() {
    const symbol = this.symbol;
    const to = DateTime.local().toISODate();
    const from = DateTime.local().minus({ month: 3 }).toISODate();
    const candles = await this.client.stock.historical.candles({
      symbol, from, to,
    });

    this.candles = candles.data.reverse().reduce((candles, candle) => ({
      ...candles,
      date: [...candles.date, candle.date],
      open: [...candles.open, candle.open],
      high: [...candles.high, candle.high],
      low: [...candles.low, candle.low],
      close: [...candles.close, candle.close],
      volume: [...candles.volume, candle.volume],
    }), { date: [], open: [], high: [], low: [], close: [], volume: [] });

    Logger.log(`${symbol} candles data initialized`, NotifierService.name);
  }
}
```

### 定時取得即時報價

下一步實作 `fetchQuote()` 方法使用富果行情 API 獲取即時報價，並檢查報價日期是否與當前日期相符。如果符合，表示當天為交易日，則更新歷史 K 線數據，計算 KDJ 指標，並根據條件發送 LINE Notify 通知。

我們在 `fetchQuote()` 聲明 `@Cron()` 裝飾器設定為定時任務，這個方法將會在每日下午 `13:25` 時自動執行。

```ts
...
@Injectable()
export class NotifierService {
  ...
  @Cron('00 25 13 * * *')
  async fetchQuote() {
    const symbol = this.symbol;
    const quote = await this.client.stock.intraday.quote({ symbol });
    if (quote.date !== DateTime.local().toISODate()) return;  // 確認當天是否為交易日

    const index = this.candles.date.indexOf(quote.date);
    if (index === -1) {
      this.candles.date.push(quote.date);
      this.candles.open.push(quote.openPrice);
      this.candles.high.push(quote.highPrice);
      this.candles.low.push(quote.lowPrice);
      this.candles.close.push(quote.closePrice);
      this.candles.volume.push(quote.total?.tradeVolume * 1000);
    } else {
      this.candles.date[index] = quote.date;
      this.candles.open[index] = quote.openPrice;
      this.candles.high[index] = quote.highPrice;
      this.candles.low[index] = quote.lowPrice;
      this.candles.close[index] = quote.closePrice;
      this.candles.volume[index] = quote.total.tradeVolume * 1000;
    }

    const { close, low, high } = this.candles;
    const indicator = kdj(close, low, high);
    const k = indicator.K.slice(-1)[0];
    const d = indicator.D.slice(-1)[0];
    const j = indicator.J.slice(-1)[0];

    await this.sendNotification({
      symbol: quote.symbol,
      name: quote.name,
      price: numeral(quote.closePrice).format('0.00'),
      volume: numeral(quote.total.tradeVolume).format('0'),
      change: numeral(quote.change).format('+0.00'),
      changePercent: numeral(quote.changePercent).format('+0.00'),
      time: DateTime.fromMillis(Math.floor(quote.lastUpdated / 1000)).toFormat('yyyy/MM/dd HH:mm:ss'),
      k: numeral(k).format('0.00'),
      d: numeral(d).format('0.00'),
      j: numeral(j).format('0.00'),
    });
  }
}
```

### 發送 LINE Notify 通知

最後，實作 `sendNotification()` 方法用於建構 LINE Notify 訊息內容並發送通知。

```ts
...
@Injectable()
export class NotifierService {
  ...
  async sendNotification(payload: Record<string, any>) {
    const { symbol, name, price, change, changePercent, time, k, d, j } = payload;

    const message = [''].concat([
      `${name} (${symbol})`,
      `---`,
      `成交: ${price}`,
      `漲跌: ${change} (${changePercent})`,
      `K: ${k} D: ${d} J: ${j}`,
      `---`,
      `時間: ${time}`,
    ]).join('\n');

    await this.lineNotify.send({ message })
      .then(() => Logger.log(message, NotifierService.name))
      .catch(err => Logger.error(err.message, err.stack, NotifierService.name));
  }
}
```

### 執行應用程式

完成上述實作後，你可以使用以下指令來執行應用程式：

```sh
$ npm start
```

應用程式會執行後或每天的早上 8 點初始化歷史 K 線數據，並在下午 1 點 25 分每天更新即時報價，檢查 KDJ 指標是否符合條件，如果符合則發送 LINE Notify 通知（圖 13）。

<figure>
  <img src="./notification.png" width="480" alt="圖 13：LINE Notify 訊息推播" align="center">
  <figcaption><em>圖 13：LINE Notify 訊息推播</em></figcaption>
  <br><br>
</figure>

在這個範例中，我們使用 NestJS 透過 Fugle API 結合 LINE Notify 服務，實作出股票進出場訊號通知系統。你可以根據自己的交易策略，決定進出場訊號，打造屬於你的投資利器。

## Bonus：容器化應用程式部署

當應用程式完成後，接下來是考慮如何部署及上線。我們會將開發完成的程式 dockerize，將應用程式與執行環境打包成為映像檔，只要部署的環境支援 Docker，就可以執行我們的應用程式。

### 容器化技術與 Docker

部署應用程式是一項複雜而重要的任務，需要考量環境一致性、依賴管理和擴展性等議題。為了解決這些挑戰，容器化技術應運而生，這種技術允許開發者將應用程式及其所有相關的依賴項目打包成一個獨立的單元，稱為容器。這些容器具有輕量、可攜性以及一致的環境，能確保在不同的平台保持相同的運作方式。

<figure style="text-align: center;">
  <img src="./docker.png" width="320" alt="圖 14：Docker" align="center">
  <figcaption><em>圖 14：Docker</em></figcaption>
  <br><br>
</figure>

**Docker** 是容器化技術的一個代表性實現，它提供了一個平台，你可以將應用程式與執行環境打包成**映像檔**（image），並以**容器**（container）方式執行。每個容器都是相互隔離的，具有自己的檔案系統、執行環境和資源，可以在任何支援 Docker 的環境中運行。

### 準備 Docker 環境

我們要將應用程式打包成為映像檔，請確認好已經安裝 Docker 環境。

- **Docker Engine**：Docker 的安裝方式，可參考官方文件 [Install Docker Engine](https://docs.docker.com/engine/install/) 的說明。在開發環境下，可以選用 Docker Desktop 版本，支援 Linux、Mac (macOS)、Windows 作業系統環境。

- **Docker Compose**：[Docker Compose](https://docs.docker.com/compose/) 是用於定義多容器 Docker 應用程式的工具，透過 YAML 檔設定要運行的服務，並且使用 `docker-compose` 指令啟動配置檔中定義的服務。

| NOTE: |
| :--- |
| 如果安裝的 Docker Engine 是 Desktop 版本，它已經內置了 Docker Compose；如果 Docker Engine 安裝的是 Server 版本，則需要另安裝 Docker Compose。Docker Compose 的安裝方式，可參考官方文件 [Install Docker Compose](https://docs.docker.com/compose/install/) 的說明。 |

### 建構 Docker 映像檔

[Dockerfile](https://docs.docker.com/engine/reference/builder/) 是 Docker 用來建構 Docker 映像檔的檔案，它包含了一系列的指令，告訴 Docker 如何從基礎映像檔開始，安裝所需的軟體和套件，複製程式碼和資源，設定執行環境和參數等。

請在專案目錄下新增 `Dockerfile` 檔案，並且編寫以下的指令：

```dockerfile
FROM node:18-alpine as builder

ENV NODE_ENV build

USER node
WORKDIR /home/node

COPY --chown=node:node . /home/node

RUN npm install \
    && npm run build

# ---

FROM node:18-alpine

ENV NODE_ENV production
ENV TZ Asia/Taipei

USER node
WORKDIR /home/node

COPY --from=builder /home/node/package.json /home/node/
COPY --from=builder /home/node/package-lock.json /home/node/
COPY --from=builder /home/node/dist/ /home/node/dist/

RUN npm install --production

CMD ["node", "dist/main.js"]
```

這些指令會告訴 Docker 從 `node:18-alpine` 這個基礎映像檔開始，設定環境變數、使用者和工作目錄，複製程式碼和資源，安裝套件和建置程式，然後從新的 `node:18-alpine` 映像檔開始，設定環境變數、使用者和工作目錄，複製套件和程式碼，安裝生產環境所需的套件，最後執行應用程式。

建立 Dockerfile 後，可在專案根目錄使用以下 `docker` 指令建構映像檔：

```sh
$ docker build -t trading-signal-notifier:latest .
```

| NOTE: |
| :--- |
| 在 `docker build` 指令中，`-t` 選項可指定映像檔名稱和標籤。 |

### 使用 Docker Compose 執行容器化應用程式

為了管理應用程式，你可以使用 [Docker Compose](https://docs.docker.com/compose/compose-file/) 配置容器服務。請在專案根目錄下新增 `docker-compose.yml` 檔案，並加入以下內容：

```yml
version: "3"

services:
  trading-signal-notifier:
    build:
      context: .
      dockerfile: ./Dockerfile
    image: trading-signal-notifier:latest
    environment:
      - FUGLE_MARKETDATA_API_KEY=${FUGLE_MARKETDATA_API_KEY}
      - LINE_NOTIFY_ACCESS_TOKEN=${LINE_NOTIFY_ACCESS_TOKEN}
    restart: always
```

完成配置設定後，在包含 `docker-compose.yml` 檔案的目錄中，使用以下指令，以啟動配置文件中定義的容器：

```sh
$ docker-compose up -d
```

這個指令會讓 Docker Compose 啟動所有容器，指定 `-d` 選項代表容器在背景執行。如果服務的映像檔不存在，Docker Compose 會先建構或下載映像檔。

要停止運行的容器，可以使用以下指令：

```sh
$ docker-compose down
```

這個指令會讓 Docker Compose 停止服務，並刪除所有容器。

以上我們說明了如何建構應用程式映像檔與啟動容器服務的流程，你可以根據自己的需求自行設計 CI/CD pipeline，將應用程式以 Docker 映像檔形式發布，然後就可以在各種部署環境中，啟動容器執行應用程式。

欲查看完整的程式碼，請參考我們的 GitHub [repo](https://github.com/chunkai1312/trading-signal-notifier)。

## 工商時間

筆者著作《Node.js 量化投資全攻略：從資料收集到自動化交易系統建構實戰》已出版，這本書將是市面上第一本以 Node.js 為例的量化投資專書，並使用 NestJS 和 JS 生態圈工具來打造屬於自己的投資利器！本書的內容專為台灣股市特有的環境量身而寫，提供了豐富內容與實用範例，歡迎參考選購，開始你的量化投資之旅！

**購書連結：**
[天瓏網路書店](https://www.tenlong.com.tw/products/9786263336070) | [博客來網路書店](https://www.books.com.tw/products/0010970613) | [誠品書店](https://www.eslite.com/product/10012011762682463156005) | [蝦皮購物](https://shopee.tw/product/728783014/22357449944/) | [momo購物網](https://www.momoshop.com.tw/goods/GoodsDetail.jsp?i_code=11954040)

![](./MP22331_banner_1800-410.jpg)
