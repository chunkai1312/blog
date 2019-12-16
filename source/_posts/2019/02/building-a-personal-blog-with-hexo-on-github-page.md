---
title: 使用 Hexo 及 GitHub Page 建立個人部落格
tags: [Hexo, GitHub Pages, Blog]
categories: [技術分享]
index_img: /2019/02/12/building-a-personal-blog-with-hexo-on-github-page/cover.png
date: 2019-02-12 02:43:55
---

![cover](/2019/02/12/building-a-personal-blog-with-hexo-on-github-page/cover.png)

> 事實上有段時間也嘗試用 [Meduim](https://medium.com/) 寫部落格。不過自己還是比較習慣直接用 [Markdown](https://markdown.tw/) 語法寫部落格。另外還有一點就是 Medium 上的文章曝光度比較高，一些寫於工作上的隨筆記錄不是那麼適合大眾化，加上 Hexo 的自訂性與自由度都更高一點，比較符合我的使用需求。

<!-- more -->

因為之前申請網域即將到期了，新的網域就是現在網址列所看到的，更短、更好記、也更個人化。隨著網域更新，順手也將本站升級了版本，趁著記憶猶新，寫篇文章記錄一下過程。

## 安裝 Hexo

確認電腦環境已經安裝 [Node.js](http://nodejs.org/) 與 [Git](http://git-scm.com/)，然後透過 NPM 安裝 Hexo 命令列程式：

```bash
$ npm install -g hexo-cli
```

安裝需求可以參考 [官方文件](https://hexo.io/zh-tw/docs/index.html) 的說明。

## 建立 Hexo 專案

安裝 Hexo 完成後，執行以下指令：

```bash
$ hexo init <folder>
$ cd <folder>
$ npm install
```

其中 `<folder>` 就是指定安裝專案的資料夾，完成後會有以下檔案：

```
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

* _config.yml：網站配置檔案，在此配置大部分的設定。
* package.json：Node.js 應用程式的描述文件。
* scaffolds：[鷹架](https://hexo.io/zh-tw/docs/writing.html#%E9%B7%B9%E6%9E%B6%EF%BC%88Scaffold%EF%BC%89) 資料夾。建立新文章時，Hexo 會根據 scaffold 來建立檔案。
* source：原始檔案資料夾。`_drafts` 目錄下存放草稿文章；`_posts` 目錄下存放發佈文章。
* themes：[主題](https://hexo.io/zh-tw/docs/themes) 資料夾。Hexo 會根據主題來產生靜態檔案。

詳細的配置說明請參考 Hexo [官方文件](https://hexo.io/zh-tw/docs/configuration) 。

## 套用佈景主題

Hexo 預設使用 [landscape](https://github.com/hexojs/hexo-theme-landscape) 主題。可以換成 [其他](https://hexo.io/themes/) 喜歡的主題，或者自己打造一個。

[NexT](https://github.com/theme-next/hexo-theme-next) 這套主題應該是目前最多人使用的。以 NexT 主題為例，最簡單的安裝方法是 clone 該 repo 至 `themes` 目錄下： 

```bash
$ cd <folder>
$ git clone https://github.com/theme-next/hexo-theme-next themes/next
```

然後可以啟用 NexT 佈景主題：

```yml
theme: next
```

NexT 預設提供 4 個樣式可選：

```yml
# Schemes
#scheme: Muse
#scheme: Mist
#scheme: Pisces
scheme: Gemini
```

NexT 佈景主題配置在 `<folder>/themes/next/config.yml` 檔案。其他詳細設定請參考 NexT [文件](https://theme-next.org/) 說明。

## 文章的撰寫與發佈

撰寫草稿：

```bash
$ hexo new draft <title>
```

建立一個草稿會在 `<folder>/source/_drafts` 目錄下根據給定的 `<title>` 建立檔案。

發佈文章：

```bash
$ hexo publish <title>
```

發佈文章則會將 `_drafts` 目錄下的 `<title>` 檔案移動到 `_posts` 資料夾。

預覽結果：

```basg
$ hexo serve --draft
```

打開瀏覽器進入 `http//:localhost:4000` 可看預覽結果。

每篇文章的 Markdown 檔案需要定義 [Front-matter](https://hexo.io/zh-tw/docs/front-matter) ，用來指定文章標題、標籤、類別等。如果需要建立**標籤**及**分類**頁，分別建立以下檔案：

標籤頁：

```markdown
---
type: categories
comments: false
---
```

分類頁：

```markdown
---
type: categories
comments: false
---
```

## 部署至 GitHub Pages

部署的方法很多種，因人而異。個人習慣是在 GitHub 上建立 `blog` repo 放置 Hexo 部落格專案；另外再建立 `<username>.github.io` repo 用於部署網站。以 Git 部署為例，安裝 [hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)：

```
$ npm install hexo-deployer-git --save
```

修改部署設定：

```yml
deploy:
  type: git
  repo: <repository url>
  branch: [branch]
  message: [message]
```

部署之前建議先刪除已產生的檔案和快取：

```bash
$ hexo clean
```

然後生成的靜態檔案：

```bash
$ hexo generate
```

部署網站：

```bash
$ hexo deploy
```

在 Local 端就可完成部署。當然如果想要「搞剛」一點，透過 [Travis CI](http://travis-ci.org/) 或 [CircleCI](https://circleci.com/) 之類的 CI/CD 工具做自動化部署也是可以的。

## References

* [Hexo](https://hexo.io/zh-tw/)
* [GitHub Pages Basics](https://help.github.com/categories/github-pages-basics/)