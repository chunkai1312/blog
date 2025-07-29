---
title: 使用 Firecrawl 產生網站的 llms.txt
date: 2025-07-29 16:10:02
tags: [llms.txt, firecrawl]
categories: 技術分享
index_img: /2025/07/29/firecrawl-generate-llms-txt/cover.png
---

> 當 AI 模型愈來愈常被應用於搜尋、問答與內容生成等場景，網站經營者也需思考：該如何讓 AI 更精確地理解網站內容？`llms.txt` 正是一種針對大型語言模型設計的標準格式，能以簡潔、結構化的方式提供網站資訊，協助模型快速掌握網站架構與重點。透過 Firecrawl 所提供的開源工具與 API，產生這類檔案變得快速又直覺，無需撰寫爬蟲程式，即可將網站轉換成 AI 可理解的知識資源。本文將介紹 `llms.txt` 的格式、用途，以及如何利用 Firecrawl 自動產生高品質的 AI 導向網站摘要檔。

![](/2025/07/29/firecrawl-generate-llms-txt/cover.png)

<!-- more -->

## 什麼是 llms.txt？

`llms.txt` 是由 [Jeremy Howard](https://www.fast.ai/) 提出的一種標準化 Markdown 檔案格式，目的是讓 LLM 在推論時更容易理解網站的內容。不同於為人類設計的網頁內容，`llms.txt` 採用簡潔、結構化的格式，便於模型快速讀取與解析。它主要包含兩種類型：

* `/llms.txt`：簡化版的網站導覽檔，提供清晰的頁面結構與摘要資訊，協助 AI 快速掌握網站架構與重點內容。
* `/llms-full.txt`：完整版本，包含網站所有頁面與內容的詳細整理，適合用於語義搜尋、知識建構或模型訓練。

這種格式特別適用於：

* 開發文件與程式庫說明
* 公司或組織網站的簡介與資源
* 教育機構或個人作品集網站

`llms.txt` 檔案通常放置於網站根目錄的 `/llms.txt` 路徑，內容包含下列區段（依固定順序）：

1. **專案名稱**
2. **摘要說明**
3. **詳細描述**
4. **檔案清單**（包含各個頁面的 URL 以供延伸查閱）

這樣的格式讓 LLM 能迅速掌握網站的重點內容與結構。

## 如何使用 Firecrawl 產生 llms.txt？

Firecrawl 是一款由 Mendable.ai 與社群共同開發的開源網站爬蟲工具，專門設計用來自動爬取整個網站內容，並將其轉換為適合大型語言模型（LLM）使用的乾淨、結構化資料格式（如 Markdown 或 /llms.txt）。

用 Firecrawl 產生網站的 `llms.txt` 檔案主要有以下幾種簡單方法：

1. **使用線上產生器工具**  
   你可以直接打開 Firecrawl 的 [LLMs.txt generator](https://llmstxt.firecrawl.dev/) 網站，在輸入框貼上你想爬取的網站網址，按「Generate」按鈕。系統會自動爬取該網站及其子頁面，並生成整合網站內容的 `/llms.txt` 或更詳盡的 `/llms-full.txt` 檔案，下載後即可使用。

2. **使用 Firecrawl API**  
   Firecrawl 提供方便的 HTTP GET API，只需送出類似以下請求即可得到 llms.txt 內容：  

   精簡版：
   ```
   GET http://llmstxt.firecrawl.dev/{YOUR_URL}
   ```

   完整版：
   ```
   GET http://llmstxt.firecrawl.dev/{YOUR_URL}/full
   ```
   若你擁有 Firecrawl 的 API 金鑰，可加入參數以移除限制並取得完整內容：

   精簡版（含 API 金鑰）：
   ```
   GET http://llmstxt.firecrawl.dev/{YOUR_URL}?FIRECRAWL_API_KEY=YOUR_API_KEY
   ```

   完整版（含 API 金鑰）：
   ```
   GET http://llmstxt.firecrawl.dev/{YOUR_URL}/full?FIRECRAWL_API_KEY=YOUR_API_KEY
   ```

3. **本地部署 llms.txt 產生器**  
   使用者可將 Firecrawl [llmstxt-generator](https://github.com/mendableai/llmstxt-generator) 開源專案 clone 下來，設置 API key，透過 `npm install` 與 `npm run dev` 來本地啟動服務，自行爬取網站並生成 `llms.txt`，方便更靈活控制和確保資料安全。

4. **使用 llms.txt 命令列工具**
   Firecrawl 提供 Python SDK 實作的 [create-llmstxt-py](https://github.com/mendableai/create-llmstxt-py) 與 Node.js SDK 實作的 [npx-generate-llmstxt](https://github.com/mendableai/npx-generate-llmstxt)。可以在取得 Firecrawl API Key 後，以指令方式產生 `llms.txt` 檔案。

5. **透過程式碼整合 Firecrawl API**  
   Firecrawl 提供多語言 [SDK](https://docs.firecrawl.dev/sdks/overview)，開發者可使用 SDK 呼叫 Firecrawl API，自動爬取網站內容並提取成 LLM 友好的結構化 Markdown 或 `llms.txt` 格式

## 結語：讓 AI 真正讀懂你的網站

隨著 AI 技術進入產品化階段，網站內容不再只是給人讀，也該考慮「怎麼讓 AI 也看得懂」。`/llms.txt` 正是為此而生的橋樑，而 Firecrawl 則是最省時、省力的建構工具。

無論你是想強化 RAG 系統、打造內部知識庫，還是提升 AI 搜尋品質，Firecrawl 都能讓你快速完成高品質資料準備，讓網站成為 AI 可理解、可引用的知識來源。

現在就試試 Firecrawl，從一行網址開始，讓你的網站走進 AI 的世界。

## References

* [llms.txt 官方網站](http://llmstxt.org)
* [Firecrawl LLMs.txt Generator](http://llmstxt.firecrawl.dev)
* [mendableai/llmstxt-generator](https://github.com/mendableai/llmstxt-generator)
* [mendableai/npx-generate-llmstxt](https://github.com/mendableai/npx-generate-llmstxt)
* [mendableai/create-llmstxt-py](https://github.com/mendableai/create-llmstxt-py)