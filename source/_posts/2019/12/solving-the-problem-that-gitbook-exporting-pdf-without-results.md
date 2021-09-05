---
title: 解決 Gitbook 匯出 PDF 檔案時沒有產出的問題
tags: [Gitbook, PDF, Calibre]
categories: [工具應用]
index_img: /2019/12/16/solving-the-problem-that-gitbook-exporting-pdf-without-results/cover.png
date: 2019-12-16 11:31:34
---

![](/2019/12/16/solving-the-problem-that-gitbook-exporting-pdf-without-results/cover.png)

> 先前嘗試使用 [Gitbook](https://github.com/GitbookIO/gitbook) 匯出 PDF 檔案時，發生沒有結果，也沒有回報任何錯誤的問題，經查發現是 Calibre 版本問題所致，在此記錄。

<!-- more -->

## 環境

- macOS Mojave 10.14.6
- calibre 4.6.0
- gitbook-cli 2.3.2
- GitBook version: 3.2.3

## 問題描述

按 [文件](https://github.com/GitbookIO/gitbook/blob/master/docs/ebook.md) 說明，在編寫 gitbook 的目錄下，以下指令應該產生一個 PDF 檔案：

```
$ gitbook pdf ./ ./mybook.pdf
```

在 Terminal 上顯示類似以下的輸出，持續很長時間無結果，也沒有任何的錯誤回報：

```
info: 7 plugins are installed
info: 6 explicitly listed
info: loading plugin "highlight"... OK
info: loading plugin "search"... OK
info: loading plugin "lunr"... OK
info: loading plugin "sharing"... OK
info: loading plugin "fontsettings"... OK
info: loading plugin "theme-default"... OK
info: found 3 pages
info: found 2 asset files
```

因此，再次執行以下指令後查看更詳細的 log 訊息：

```
$ gitbook pdf ./ ./mybook.pdf --log=debug —debug
```

然後在 Terminal 上顯示類似以下的輸出：

```
debug: Auto generated TOC with 3 entries.
debug: Flattening CSS and remapping font sizes...
debug: Source base font size is 12.00000pt
debug: Removing fake margins...
debug: Cleaning up manifest...
Trimming unused files from manifest...
debug: Creating PDF Output...
debug: 67% Running PDF Output plugin
debug: 68% Parsed all content for markup transformation
debug: 70% Completed markup transformation
debug: [1106/220730.806158:ERROR:icu_util.cc(177)] Invalid file descriptor to ICU data received.
[1106/220730.806170:ERROR:icu_util.cc(177)] Invalid file descriptor to ICU data received.
debug: [1106/220730.806054:ERROR:icu_util.cc(177)] Invalid file descriptor to ICU data received.
debug: [1106/220730.812668:ERROR:icu_util.cc(177)] Invalid file descriptor to ICU data received.
```

## 解決方法

在 [stackoverflow](https://stackoverflow.com/questions/58498643/no-result-or-error-when-using-gitbook-pdf) 上找到一則同樣問題的解決方法，總之就是將 `calibre-ebook` 版本從 4.X 降至 3.x 即可。可以在這個 [頁面](https://download.calibre-ebook.com/3.html) 上找到 `calibre-ebook` 3.x 的版本。

安裝完成後，重新執行以下指令後順利產生 PDF 檔案。

```
$ gitbook pdf ./ ./mybook.pdf
```

## 參考資料

- [gitbook で PDF を書き出そうと思ったら応答がなかった - かわろぐ](https://blog.kawa-xxx.jp/entry/2019/11/23/143439)
- [macos - No result or error when using `gitbook pdf` - Stack Overflow](https://stackoverflow.com/questions/58498643/no-result-or-error-when-using-gitbook-pdf)
