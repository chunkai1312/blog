---
title: Marp：寫 Markdown 製作投影片
tags:
  - Marp
  - Markdown
  - Slides
categories:
  - [工作筆記]
date: 2019-02-27 18:25:53
---


[Marp](https://yhatt.github.io/marp/) 是一套開源、使用 [Electron](https://electronjs.org/) 開發的桌面應用程式。提供所見即所得（What You See Is What You Get）編輯器，可以用 Markdown 語法產生 PDF 格式的投影片。

![](/2019/02/27/using-marp-to-create-slides-with-markdown/marp_banner.png)

<!-- more -->

## 特色

- 使用 Markdown 建立投影片
- 跨平台
- 支援 3 種即時預覽模式
- 支援主題 (目前提供 [Default](https://speakerdeck.com/yhatt/marp-basic-example) 和 [Gaia](https://speakerdeck.com/yhatt/introducing-marps-gaia-theme) 主題)
- 支援渲染表情符號，數學和背景圖像
- 以 PDF 格式匯出投影片

## 安裝

從 Marp 的 GitHub release page [下載](https://github.com/yhatt/marp/releases) 最新版本，支援 Windows、macOS、Linux 等平台。

## 怎麼寫投影片？

使用 Markdown、用 `---` 語法分割投影片，非常簡單。可以參考這份 [範例](https://raw.githubusercontent.com/yhatt/marp/master/example.md)。

```
# Slide 1

foobar

---

# Slide 2

foobar
```

---

如果你有快速產生投影片的需求，而剛好又對投影片的設計要求不高的話，那麼這套工具就非常值得一試。
