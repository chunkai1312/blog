---
title: 數位廣告基礎入門：即時競價 Real-Time Bidding
tags: [Digital Advertising, Real-Time Bidding]
categories: [數位廣告]
index_img: /2020/01/17/real-time-bidding/cover.jpg
date: 2020-01-17 00:00:00
---

> RTB（Real Time Bidding）即時競價機制，是一個網路廣告的競價機制。相比於之前廣告主投放廣告較常採用的廣告聯播網，無法確定真正點擊廣告的是誰，而浪費預算。即時競價機制是利用資料分析能力，得出使用者的習慣跟興趣，再讓相關的廣告商互相競價廣告位，這個競價方式可以讓廣告商可以用合理的價錢購買到最適合的廣告。

![cover](/2020/01/17/real-time-bidding/cover.jpg)

<!-- more -->

## 何謂即時競價

RTB（Real Time Bidding）即時競價機制，是一個網路廣告的競價機制。相比於之前廣告主投放廣告較常採用的廣告聯播網，無法確定真正點擊廣告的是誰，而浪費預算。即時競價機制是利用資料分析能力，得出使用者的習慣跟興趣，再讓相關的廣告商互相競價廣告位，這個競價方式可以讓廣告商可以用合理的價錢購買到最適合的廣告。

## 即時競價市場中的關鍵角色

RTB 市場的關鍵角色包括廣告主（Advertiser）、廣告需求方平台（DSP）、廣告交易平台（AdX）、廣告供應方平台（SSP）、數據管理平台（DMP）和出版商（Publisher）。

![](./rtb-ecosystem.png)

### 廣告主 Advertiser

廣告主是廣告曝光（impressions）和相關受眾（audiences）的購買者。在 RTB 出價拍賣中，廣告主根據其行銷目標、預算、策略等為廣告曝光出價。出價最高的廣告主會贏得廣告曝光。

### 廣告需求方平台 Demand Side Platform (DSP)

DSP 是一個全面的代理平台，可幫助廣告主優化廣告管理和投放策略。基於大數據分析和受眾群體定位技術 (audience targeting technolo­gies) 以及 RTB 架構和演算法，DSP 幫助廣告主以簡單，便捷和統一的方式從 AdX 購買最匹配的廣告曝光。

### 廣告交易平台 Ad Exchange (AdX)
  
AdX 是一個廣告交易市場，它針對每次的廣告曝光匹配買方和賣方，就像股票交易市場一樣。AdX 使用標準化協定在 RTB 市場中的其他角色之間傳遞廣告請求和用戶資訊，旨在找到廣告主及其目標受眾（target audiences）的最佳匹配。因此，它在 RTB 市場中起著至關重要的作用。

### 廣告供應方平台 Supply Side Platform (SSP)

SSP 是一個代理平台，可幫助出版商優化廣告庫存（ad inventory）的管理和定價策略，包括設置最優底價、將廣告曝光分配到不同的 channels 等。

### 數據管理平台 Data Management Platform (DMP)

DMP 是一個數據管理平台，用於收集，儲存和分析 Internet 用戶的 cookie 數據。DMP 通常為 DSPs 和 AdXs 提供識別目標受眾的付費服務。

### 出版商 Publisher

出版商是網站的擁有者。每次訪問出版商網頁的用戶都會觸發一次曝光，並且在此次曝光中贏得 RTB 競價的廣告主的廣告將顯示在該出版商的網頁上。

## 即時競價廣告的投放流程

![](./rtb-process.png)

① Internet 用戶訪問出版商的網頁。

② 如果在 RTB 市場上有一個或多個廣告版位要出售，則出版商會將廣告請求發送到 SSP 和 AdX，包含用戶資訊、廣告版位和底價。

③ 收到來自 SSP 的廣告請求後，AdX 會將所有訊息轉發給符合條件的 DSP。

④ 然後，每個 DSP 解析廣告請求中的訊息，向 DMP 詢問該用戶的必要資訊（例如其地理位置、性別、年齡、歷史行為、購物興趣和意圖等），並開始拍賣以匹配廣告主。獲勝的廣告主廣告將反饋給 AdX，每個 DSP 必須在特定時間內做出回應。

⑤ AdX 開始競標每個 DSP 獲勝的廣告主，並確定這些獲勝的廣告主中的最高出價。如果此出價低於出版商的底價，則此 RTB 流程將終止，廣告版位為空。或重新分配給非 RTB channels。否則，出價最高的廣告主將最終贏得廣告曝光。

⑥ AdX 向所有 DSP 宣布競價結果，並將最終獲勝的廣告主的廣告發送給 SSP。

⑦ SSP 協助將廣告顯示出版商網頁上的廣告版位，以呈現在用戶面前。

## 參考資料

- [A Survey on Real Time Bidding Advertising](http://wnzhang.net/share/rtb-papers/rtb-survey.pdf)
- [【硬塞科技字典】什麼是 RTB 即時競價廣告？](https://www.inside.com.tw/article/6673-what-is-rtb)
- [一張圖教你看懂什麼是RTB實時競價廣告｜數位時代](https://www.bnext.com.tw/article/32464/BN-ARTICLE-32464)