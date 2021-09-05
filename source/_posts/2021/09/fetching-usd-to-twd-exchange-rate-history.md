---
title: 取得美元兌新台幣歷史匯率
tags: [新台幣, 美元, 匯率]
categories: 工具應用
date: 2021-09-05 16:12:22
index_img: /2021/09/05/fetching-usd-to-twd-exchange-rate-history/cover.jpg
---

> 美元兌新台幣歷史匯率除了可以透過中央銀行網站查訊，也可以從臺灣期貨交易所取得參考匯率。

![](/2021/09/05/fetching-usd-to-twd-exchange-rate-history/cover.jpg)

<!-- more -->

需要查詢新臺幣/美元匯率，一般會參考中央銀行每日公布的 [新臺幣/美元 銀行間收盤匯率](https://www.cbc.gov.tw/tw/lp-645-1.html)。

![圖片來源：中央銀行](/2021/09/05/fetching-usd-to-twd-exchange-rate-history/cbc.png)

如果需要抓大量數據做研究回測，由於央行網站結構設計的關係，要寫爬蟲取得大量數據可能不是那麼容易。需要一次性的取得大範圍日期的匯率數據，可以參考臺灣期貨交易所提供的 [每日外幣參考匯率查詢](https://www.taifex.com.tw/cht/3/dailyFXRate)。

![圖片來源：臺灣期貨交易所](/2021/09/05/fetching-usd-to-twd-exchange-rate-history/taifex.png)

雖然下方備註了這是期交所盤後洗價與保證金計算之匯率資料，與央行公告的收盤匯率之時點與匯率不同，但筆者對過基本上與央行公布的數據一致。如果不需要追求數據百分之百正確，做為研究分析之用應該已經足夠。