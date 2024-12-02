---
title: 《個資風暴：劍橋分析事件》：數據權是人權
tags: [劍橋分析事件]
categories: 影片賞析
index_img: /2019/11/24/the-great-hack/cover.jpg
date: 2019-11-24 11:50:48
---

> 「個資風暴：劍橋分析事件」 (The Great Hack) 是一部紀錄片，探究名叫劍橋分析 (Cambridge Analytica) 的數據公司，如何非法運用 Facebook 平台收集用戶個資進行政治操作，進一步影響英國脫歐 (Brexit) 與 2016 年美國總統大選。

<!-- more -->

![](/2019/11/24/the-great-hack/cover.jpg)

## 劍橋分析事件

紀錄片中 Christopher Wylie 是一位數據科學家，也是劍橋分析公司的前員工，他口中描述的劍橋分析並不是一間數據公司或演算法公司，而是全方位的政宣機器。為了取得大量使用者資料並進一步分析使用者輪廓 (User Profile)，他找上了劍橋大學心理學家 Aleksandr Kogan 弄來一個名為 "This is Your Digital Life" 的 Facebook 心理測驗程式。該程式擁有特殊的權限，不僅能採集使用或加入應用程式者的數據，還能拉出所有朋友的數據。也就是說，你如果有一個朋友用了這個應用程式，你的資料就可以在非經你同意的情況下被取得。

透過這種方式，劍橋分析公司在未經許可的情況下獲取了約 5000 萬名 Facebook 使用者的個人數據，並運用這些個人數據，使用基於資料探勘等技術得出的心理和人格特徵。以這個所建構出來的選民模型資料為基礎，為 2016 年美國總統選舉進行了有針對性的政治活動。例如利用 Facebook 廣告中的類似廣告受眾、興趣標籤等方式，精準地投放廣告到可能左右大選勝負的幾個關鍵搖擺州，影響選民的投票意向，進一步影響大選結果。

## 反思

身為一位多年與 Facebook Graph API 打交道的開發者，很清楚地知道這個脈絡歷史。事實上，在 Facebook 釋出 API 的早期，權限控制相當寬鬆，申請 Facebook App 也沒有什麼限制，透過開發的 Facebook 應用程式只要經由使用者授權同意，就可以取得使用者個人資料、貼文訊息、按讚資料等資訊，甚至取得使用者朋友的相關資訊，可以進一步建構出個完整的社交網絡資訊。

劍橋事件爆發，迫使 Facebook 提出更嚴謹的 [App Review](https://developers.facebook.com/docs/apps/review/) 機制，只有通過審查的應用程式才能上線，授權使用 API 取得相關資料。

隨著「數據是世界上最有價值的資產，數據權是人權」的觀念開始受到重視，歐盟在 2016 年 4 月 14 日通過 GDPR (General Data Protection Regulation)，這個號稱最嚴格的《一般資料保護規範》，在 2018 年 5 月 25 日 正式上路。

## 結語

這部在 Netflix 上的紀錄片相當值得一看，尤其身處在地緣政治複雜的台灣，許多證據顯示資訊戰確實存在，在 2018 年的九合一選舉就可以觀察到大量的假新聞與謠言在網路上充斥傳遞，造成人民與人民之間的對立、分化彼此。如同記者 Carole Cadwalladr 所說：「全球有道黑暗的逆流連結著我們，這條逆流靠著科技平台流動。這跟左派右派、留歐脫歐和川普不川普無關，這跟能不能自由公平選舉有關。」個資數據被濫用將帶來的災難性後果，對自由民主的社會造成極大的威脅。