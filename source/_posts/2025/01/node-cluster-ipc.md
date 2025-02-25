---
title: Node Cluster IPC：讓 Node.js 行程間通訊更簡單
tags: [NodeJS, Cluster, IPC, Process]
categories: 技術分享
date: 2025-01-03 23:30:00
index_img: /2025/01/03/node-cluster-ipc/cover.png
---

[Node Cluster IPC] 是我開發的一個輕量級 Node.js 套件，目的在簡化使用 `cluster` 模組時的行程間通訊（IPC）。在 Node.js 應用中，當需要處理大量並發請求或計算密集型操作時，使用 `cluster` 模組來建立多個工作行程是一種常見的做法。隨著多行程的運行，如何高效且可靠地進行行程間的數據傳遞成為了開發中的一個挑戰。而 Node Cluster IPC 正是為了解決這些問題而誕生的。

<!-- more -->

## 什麼是 Node Cluster IPC？

`node-cluster-ipc` 是一個輕量級的 Node.js 套件，專門用於簡化在使用 `cluster` 模組時的行程間通訊（IPC）。它使得主行程（Primary）和工作行程（Worker）之間能夠輕鬆地發送訊息、發布訊息以及進行請求和回應。更重要的是，它內建支持請求超時處理和自動選擇工作行程，讓開發者可以專注於業務邏輯的實現，而不必擔心複雜的行程間通訊細節。

## Node Cluster IPC 解決了什麼問題？

### 簡化了行程間通訊

在傳統的 Node.js 集群應用中，主行程和工作行程之間的通訊經常需要用到 `process.send()` 和 `process.on()` 等 API，這些方法的使用方式比較繁瑣，且缺乏統一的管理。`node-cluster-ipc` 提供了一個更高層次的抽象，透過簡單的 API 來處理訊息的發送、接收和回應。

### 支持請求-回應模式

在許多應用場景中，主行程可能需要向某個工作行程發送請求，並等待回應。`node-cluster-ipc` 提供了請求-回應模式，並自動處理請求超時，這讓開發者可以更輕鬆地實現複雜的邏輯，而不需要自己管理每一個細節。

### 提升開發效率

透過簡化行程間的通訊流程，`node-cluster-ipc` 可以幫助開發者提高開發效率，減少錯誤並改善程式碼的可維護性。它的事件驅動架構使得訊息處理變得非常直觀。

## Node Cluster IPC 使用場景

### 高併發應用

在高併發的應用中，例如 Web 伺服器或 API 服務，`node-cluster-ipc` 可以幫助您輕鬆地與多個工作行程進行通訊，實現負載均衡和錯誤處理。您可以使用 `publish` 方法將訊息推送到所有工作行程，並利用 `request` 方法與特定工作行程進行數據處理。

### 計算密集型任務

當您的應用需要處理大量計算密集型任務時，將這些任務分配給多個工作行程進行處理能夠顯著提高效能。您可以透過 `request` 來向工作行程發送數據，並透過回應來獲取計算結果。這樣可以避免主行程被阻塞，提高應用的響應速度。

### 數據同步

在多行程的應用中，數據同步是常見的需求。`node-cluster-ipc` 支持的 `message` 和 `request` 事件可以幫助實現不同行程間的數據同步，讓數據流動更加高效且可靠。

## 如何安裝和使用

### 安裝

只需要運行以下命令，即可將 `node-cluster-ipc` 安裝到你的 Node.js 專案中：

```bash
$ npm install --save node-cluster-ipc
```

### 快速開始

以下是一個簡單的範例，展示了如何在 Node.js 應用程式使用 `node-cluster-ipc` 進行行程間通訊：

```js
const cluster = require("cluster");
const { ClusterIpc } = require("node-cluster-ipc");

const ipc = new ClusterIpc();

if (cluster.isPrimary) {
  cluster.fork();
  cluster.fork();

  ipc.publish("hello-channel", "Hello, worker!");

  ipc
    .request("compute-channel", 42)
    .then((response) => {
      console.log("[Primary] Worker response:", response);
    })
    .catch((err) => {
      console.error("[Primary] Error:", err);
    });

  ipc.on("message", (channel, data) => {
    console.log(`[Primary] Received message on ${channel}:`, data);
  });

  ipc.on("request", (channel, data, reply) => {
    console.log(`[Primary] Received request on ${channel}:`, data);
    reply(data * 2);
  });
} else {
  ipc.on("message", (channel, data) => {
    console.log(`[Worker] Received message on ${channel}:`, data);
  });

  ipc.on("request", (channel, data, reply) => {
    console.log(`[Worker] Received request on ${channel}:`, data);
    reply(data * 2);
  });
}
```

### 主要功能

#### 發送訊息到工作行程

使用 `send` 方法向指定的工作行程發送訊息。

```javascript
ipc.send("channel-name", { key: "value" }, workerId);
```

#### 向所有工作行程發送訊息

使用 `publish` 方法將訊息發布到所有工作行程。

```javascript
ipc.publish("channel-name", { key: "value" });
```

#### 請求-回應模式

使用 `request` 方法向工作行程發送請求，並獲取回應。

```javascript
ipc
  .request("channel-name", { key: "value" })
  .then((response) => {
    console.log("Response:", response);
  })
  .catch((error) => {
    console.error("Error:", error);
  });
```

#### 處理訊息和請求

使用 `message` 和 `request` 事件處理接收到的訊息和請求。

```javascript
ipc.on("message", (channel, data) => {
  console.log(`Received message on ${channel}:`, data);
});

ipc.on("request", (channel, data, reply) => {
  console.log(`Received request on ${channel}:`, data);
  reply({ responseKey: "responseValue" });
});
```

## 結語

[Node Cluster IPC]  是一個專為 Node.js cluster 應用設計的工具，它簡化了行程間通訊的複雜性，提升了開發者的工作效率。如果你正在開發一個需要使用多個工作行程的 Node.js 應用，這個套件無疑是非常值得考慮的選擇。透過它，你可以更輕鬆地進行訊息傳遞、請求回應，並且有效地處理請求超時等問題，讓你的應用更加健壯和可擴展。

[Node Cluster IPC]: https://github.com/chunkai1312/node-cluster-ipc