---
title: Redbird：適用於 Node.js 的反向代理伺服器
tags: [NodeJS, Reverse Proxy]
categories: [工具應用]
index_img: /2019/12/11/redbird-reverse-proxy/cover.jpg
date: 2019-12-11 11:20:57
---

![](/2019/12/11/redbird-reverse-proxy/cover.jpg)

> [Redbird](https://github.com/OptimalBits/redbird) 一個以 Node.js 開發的輕量級反向代理 (Reverse Proxy) 伺服器，並支援 [LetsEncrypt ](https://letsencrypt.org) 及 [Docker](https://www.docker.com)。可以很方便地對應用程式進行反向路由，適合在一台主機的處理來自不同網域的應用程式、處理 SSL 等。

<!-- more -->

## 安裝

```
$ npm install --save redbird
```

## 使用

以下是簡單的使用例子：

```js
var proxy = require('redbird')({port: 80});

// Route to any global ip
proxy.register("optimalbits.com", "http://167.23.42.67:8000");

// Route to any local ip, for example from docker containers.
proxy.register("example.com", "http://172.17.42.1:8001");

// Route from hostnames as well as paths
proxy.register("example.com/static", "http://172.17.42.1:8002");
proxy.register("example.com/media", "http://172.17.42.1:8003");

// Subdomains, paths, everything just works as expected
proxy.register("abc.example.com", "http://172.17.42.4:8080");
proxy.register("abc.example.com/media", "http://172.17.42.5:8080");

// Route to any href including a target path
proxy.register("foobar.example.com", "http://172.17.42.6:8080/foobar");
```

其他進階設定請參考 [Redbird](https://github.com/OptimalBits/redbird) 文件說明。

## 結語

[Redbird](https://github.com/OptimalBits/redbird) 用法相當直覺且容易，如果你是一個 Node.js 開發者，我認為非常適合使用在開發環境或者需要[在本地端模擬多個網域名稱以用於第三方 JavaScript 開發](/2017/04/03/simulating-multiple-domains-at-local-for-third-party-javascript-development/)的情境。

如果要使用在生產環境，現階段建議使用 [Nginx](https://www.nginx.com/) 或 [Traefik](https://docs.traefik.io) 依然會是比較穩定可靠的選擇。
