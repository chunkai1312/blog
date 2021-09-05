---
title: 回顧 2016 年
categories: [生活札記]
index_img: /2016/12/31/2016-year-in-review/cover.jpg
date: 2016-12-31 20:20:44
---

![](/2016/12/31/2016-year-in-review/cover.jpg)

> 又到了歲末年終的時刻。今年是我正式邁入職場工作完整的一年，覺得是應該寫些什麼，來留下一點記錄。

<!-- more -->

## Node.js

其實早在 2011 年，大學實務專題就已經接觸 Node.js，並使用它作為我們系統後端開發的主要程式語言。不過這一塊主要由另一個組員負責，那時候我主要著重在 Android app 開發上（當時還僅是 Android 2.2 版本，現在 7.0 都已經出來了 XD）。真正開始投入這個領域是在研究所碩二的時候。

學習 Node.js 與其他程式語言最大的差異，大概就是對其非同步思維的掌握。 [callback hell](http://callbackhell.com) 可能是所有學習 Node.js 初學者都曾有過的經驗。


2016 年以前（更正確來說應該碩士畢業前），個人常使用 [async](https://github.com/caolan/async) 套件來處理 callback hell 問題， 不過隨著 ES6 / ES2015 的推行，[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 已經成為處理非同步的事實標準，因此它也就成為 Node.js 開者者必須要會的基本能力。

而 ES6 加入的 generator 特性，使用 [co](https://github.com/tj/co) + yield 已經可以優雅地處理非同步邏輯。ES7 則進一步引入 async / await 語法，更容易處理非同步程式。不過 async / await 直到 Node.js v7 才被引入，並且需要加入 `--harmony-async-await` 開啟該功能。至於瀏覽器則尚未支援，不過借助 [Babel](https://babeljs.io) 的轉譯功能，已經可以使用該語法投入在產品開發上。

個人目前主要還是使用 [Express](http://expressjs.com/) 框架開發 Web 以及 API 服務，在 async / await 尚未被正式支援前，引入 [co-express](https://github.com/mciparelli/co-express)，作為處理非同步的解決方案。

順便提一下 Koa。Koa 1.x 使用 generator 撰寫 middleware，2.x 則使用 async /await 。不過在 Node.js 在 async / await 尚未被正式支援前，將無限期處於 unstable 的狀態（估計要等到 Node.js v8）。就現況而言，Express 已經是很完善的生態系，周邊套件比較豐富完整，Koa 雖然標榜是下一代 Node.js 的 web 框架，但感覺沒有立即使用的必要。

今年初，為了做實作服務授權以及 API Gateway，土炮了一個 OAuth 2.0 provider，對整體 OAuth 2.0 的規範有更進一步的了解。


## Front-end Engineering

### Angular, React and Vue

除了後端 API 的開發，我在公司更多的時間可能是在寫前端。三大主流框架 Angular、React、Vue 也都玩過一遍（Angular 2 & Vue 2 還尚未有應用到實際專案的例子），來講講對這三個框架的看法。

Angular 1 在 2016 年，很多人可能認為是比較過時的舊東西了，特別是 Angular 2 在中秋節推出後特別明顯。不過 Angular 作為一個大而全的框架，對於前端提供一套完整的解決方案，只要學習上手之後，便可以提供可觀的生產力。在 Angular 1.5 後新增 `angular.component()` 方法，component 有自己的生命週期，並引入單向綁定，使得 Angular 1 也可以寫成 [compoenent-based applicatoin](https://github.com/chunkai1312/blog/issues/1)，甚至考慮引入 Flux / Redux 來管理應用程式狀態。至於 Angular 2，個人接觸不深，目前不好評論，不過仍持續保持關注。

React 本身沒有像 Angular 這麼多複雜的概念，理解 `state` 和 `props` ，大致就能開始上工了。但是要建構一個完整的 application，很明顯只有 React 是不夠的，你可能需要 React Router 來管理路由，使用 Flux / Redux 來管理應用程式的狀態。這時候學習曲線就會會直線飆升。不過對於一個初學者來說，我覺得學會 `Babel` 與 `Webpack` 來搞定開發環境可能是一個比較大的門檻，不過好在現在已經有 [create-react-app](https://github.com/facebookincubator/create-react-app) 這一類的工具出現了，可以降低初期設置開發環境的痛苦指數。

2016 年可以說是 Vue 大紅大紫的一年。我在今年 7 月才接觸，用在一個相對短期的實驗性專案。作為 MVVM 的框架，許多語法對於寫過 Angular 的開發者幾乎可以無痛轉換。不過和 React 一樣， Vue 核心並沒有提供路由機制，建立中、大型應用可能需要搭配 [vue-router](https://github.com/vuejs/vue-router)，官方也有 [vuex](https://github.com/vuejs/vuex) 作為狀態管理的解決方案。Vue 2 個人也是接觸不多，主要多了 SSR (server-side rendering)，並支援 JSX。不過老實說，用上 JSX 的 Vue，個人覺得與 React 比起來倒已經有 87 分像了 XD。

從這裡便不難發現，前端元件化開發已是趨勢，框架之間的同質性也愈來愈高，撇除性能不談，選擇框架的原因可能在於偏好。就目前而言，我更偏好於擁抱 `functional programming` 的 React。


### Build Systems

再來談談構建工具的改變。短短一、兩年之內，從一開始使用 [Grunt](http://gruntjs.com) 做為構建工具，再到 [Gulp](http://gulpjs.com) ，乃至於現在使用 [NPM scripts](https://docs.npmjs.com/misc/scripts) + [Webpack](https://webpack.github.io/)，這一連串的更迭，正體現了前端工程這幾年來產生的劇烈變化。所以在 2015 年有了 [JavaScript Fatigue](https://medium.com/@ericclemmons/javascript-fatigue-48d4011b6fc4#.b1q90e1ed) 這個說法（簡單來說就是被新技術淹沒，感到暈眩不知所措 XD）又或者是這一篇 [How it feels to learn JavaScript in 2016](https://hackernoon.com/how-it-feels-to-learn-javascript-in-2016-d3a717dd577f#.ket2h8hxu)。

至於前端套件管理方面，在 2016 年初決定將公司產品導入 Webpack 後，就已經棄用 [Bower](https://bower.io) ，全面採用 [NPM](https://www.npmjs.com) 作為前端依賴管理的解決方案。


## DevOps

大概在今年 8 月左右開始接觸 Docker，會用上 Docker 的原因，主要是開始嘗試將系統從單體式架構走向微服務 (Microservice）架構，使用微服務意味著必須要將不同功能模組由各自的專案進行開發，當管理的專案變多，開發環境的建置與部署就變成不是一件簡單的事情。

Docker 容器化與隔離性的特質，使得它很適合應用在微服務架構上。除此之外，透過 Docker Compose 的設置，還可以迅速搞定 Developer 的開發環境，讓新進人員馬上投入專案開發（沒有蜜月期？XD），並統一開發與部署環境，達到 "Build, Ship and Run Any App, Anywhere" 的境界。


## Open Source Projects

這裡僅列比較個人覺得比較重要的幾項：

- [fqb](https://github.com/chunkai1312/fqb) - Facebook Graph API query builder for JavaScript
  這是一個方便建構 Facebook Query 字串的小工具，因為開發公司產品的一些功能需要不斷與 Facebook Graph API 打交道，所以用 JavaScript 寫了這個小工具，並把它發佈到 [NPM]((https://www.npmjs.com/package/fqb)) 上，方便日後再利用。 

- [api-seed](https://github.com/chunkai1312/api-seed)
  用於建構 RESTful API 的 starter project，主要使用 Node.js 的 Express 框架搭配 MonogoDB。

- [worker-seed](https://github.com/chunkai1312/worker-seed)
  用於建構 job queue worker 的 starter project，主要基於 Node.js 與 Redis，需要定時任務可以搭配 [node-cron](https://github.com/kelektiv/node-cron) 或 [node-schedule](https://github.com/node-schedule/node-schedule) 等 library 使用。

- [react-redux-boilerplate](https://github.com/chunkai1312/react-redux-boilerplate)
  其實 React 生態圈已經有很多各式各樣的 boilerplate（也可能稱之為 starter 或 seed project），但不一定令人滿意，或需稍加修改，於是就自己親手寫了一個符合自己需求的樣板。在實踐的過程中，最重要的收穫是掌握了各式工具的應用（Wepack, Babel, ESLint, etc）。

- [angular-redux-boilerplate](https://github.com/chunkai1312/angular-redux-boilerplate)
  這是一個使用 Angular 1.5+ 結合 Redux 的樣板。相較於 React，使用 Angular 結合 Redux 的例子並不多，但是使用 Angular 的 legacy project 相信是不少的。基於系統穩定等理由，也許沒辦法立即轉換到新框架，但是使用 Angular 1.5 的 angular.component() 重構是一個不錯的方法，結合 Redux 可以讓資料流更清楚可預測。最近在整理公司既有的專案，於是隨手造了這個 boilerplate。


## About 2017

身為一個 Node.js / JavaScript Developer，新的一年，除了對 JS 的發展持續保持關注外，希望可以投入更多時間在 DevOps 上。特別是 Docker clustering tools 如 Swarm, Kubernetes, Mesos 等都還沒有時間好好玩一玩，而最終目的還是希望能將公司產品導入完整的 CI / CD 流程。

無論如何，回顧這一年的成長還真不少，新的一年也繼續努力！
