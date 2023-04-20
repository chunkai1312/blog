---
title: HonKit：GitBook 的替代方案
tags: [GitBook, HonKit]
categories: [技術分享]
index_img: /2023/04/20/honkit-a-gitbook-alternative/cover.png
date: 2023-04-20 02:38:28
---

![](/2023/04/20/honkit-a-gitbook-alternative/cover.png)

> [HonKit](https://github.com/honkit/honkit) 是一個 GitBook 2 的分支版本，它保留了 GitBook 2 的大部分功能，同時增加了一些新功能，如插件系統、主題定制、圖表、自定義 CSS 等。因此，如果你已經熟悉了 GitBook 2 的使用，那麼轉換到 HonKit 可能會比較容易，因為它們的基礎架構是相似的。

<!-- more -->

## GitBook

[GitBook](https://www.gitbook.com/) 是一個基於 Git 的線上文件撰寫及發布平台，讓使用者可以建立、編輯、管理和發布電子書和文件。它支持多種文件格式，如 Markdown、Asciidoc、reStructuredText 等，也可以將文件轉換成多種輸出格式，如 HTML、PDF、ePub、mobi 等。

GitBook 提供了一個方便的方式讓使用者進行文件撰寫和版本控制，也可以透過與 GitHub 和 GitLab 的整合，實現文件與程式碼的一體化管理。[gitbook-cli](https://github.com/GitbookIO/gitbook-cli) 是 GitBook 的命令行工具，它可以用來安裝和管理 GitBook 插件和主題，以及建立和編譯 GitBook 書籍。如果你想要在本地使用 GitBook 建立和編譯書籍，就需要先安裝 `gitbook-cli`。

不過 `gitbook-cli` 最後一次版本更新是在 2017 年 7 月，而 GitBook 也宣布他們停止了對開放源碼版本 GitBook 2 的更新和維護，並將其轉移到了基於付費的 GitBook 3 平台上。雖然 GitBook 2 的開放原始碼仍然可以在 GitHub 上找到，而且仍然可以使用，但是不會再獲得更新和支持。因為版本已經過於老舊，在安裝過程中，你很可能會碰到這個有人在 [Stack Overflow](https://stackoverflow.com/questions/64211386/gitbook-cli-install-error-typeerror-cb-apply-is-not-a-function-inside-graceful) 上提問的這則錯誤。


## HonKit

[HonKit](https://github.com/honkit/honkit) 是一個 GitBook 2 的分支版本，它保留了 GitBook 2 的大部分功能，同時增加了一些新功能，如插件系統、主題定制、圖表、自定義 CSS 等。因此，如果你已經熟悉了 GitBook 2 的使用，那麼轉換到 HonKit 可能會比較容易，因為它們的基礎架構是相似的。

當你想要使用 HonKit 建立一本書時，需要先安裝 Node.js 和 Git，然後在命令行中運行以下命令安裝 HonKit：

```sh
$ npm install honkit -g
```

安裝完成後，在一個空的目錄中運行以下命令來建立一本書：

```sh
$ honkit init
```

這會生成一個標準的 GitBook 2 的目錄結構，包括 `README.md`、`SUMMARY.md` 等文件。你可以使用 Markdown 語法來編寫書中的內容，並透過 `SUMMARY.md` 文件來組織書的結構。

當你完成了書的編寫後，可以運行以下命令來生成 HTML 文件：

```sh
$ honkit build
```

生成的 HTML 文件會存放在 `_book` 目錄下，你可以透過瀏覽器打開 index.html 文件來預覽書的內容。

如果你想要在本地啟動一個 web server 來預覽書的內容，可以運行以下命令：

```sh
$ honkit serve
```

這會啟動一個 web server，你可以在瀏覽器中打開 http://localhost:4000 來預覽書的內容。

除了以上命令外，HonKit 還提供了一些插件和主題，可以透過配置文件來啟用這些插件和主題，以滿足不同的需求。詳細的使用方式可以參考 HonKit 的官方文件說明。