---
title: 使用 Rollup 打包 JavaScript 模組
tags: [Rollup, Webpack, JavaScript]
categories: [工具應用]
index_img: /2020/03/11/using-rollup-to-bundle-javascript-modules/cover.jpg
date: 2020-03-11 00:00:00
---

> Rollup 是一個 JavaScript 的 module bundler，並提供豐富的插件 (plugins) 將程式碼進行處理，適合用來建構函式庫或應用程式。

![](./cover.jpg)

<!-- more -->

## 打包 JavaScript 模組

這裡假設已經建立好一個 JavaScript 專案，如下結構：

```
example-lib/
├── dist/
├── src/
│   └── index.js
└── package.json
```

### 安裝依賴 

使用以下命令安裝 Rollup：

```sh
$ npm install --save-dev rollup
```

### 配置檔案

然後在專案目錄下建立一個名為 `rollup.config.js` 的檔案，並加入以下內容：

```js
export default {
  input: 'src/index.js',
  output: {
    name: 'example',
    file: 'dist/bundle.js',
    format: 'umd',
  },
};
```

- `input` - 定義函式庫或應用程式的進入點
- `output.name` - 輸出打包模組的全域變數名稱
- `output.file` - 輸出打包模組的檔案名稱
- `output.format` - 輸出打包模組的檔案格式，適用瀏覽器環境可用 `iife`；適用 Node.js 環境可用 `cjs`；瀏覽器與 Node.js 環境通用可用 `umd`

### 編譯模組

在專案下的 `package.json` 內容中的 `scripts` 的加入：

```json
{
  "scripts": {
    "build": "rollup -c"
  }
}
```

上述 `-c` 為選項 `--config` 的簡寫，代表使用 `rollup.config.js` 的配置進行打包處理。然後執行以下命令即可進行打包程序：

```sh
$ npm run build
```

打包好的模組會輸出至 `dist/bundle.js`。

### 使用插件

Rollup 提供了許多豐富的 [plugins](https://github.com/rollup/plugins) 來處理文件，以下列舉比較常用的幾項：

- [rollup-plugin-node-resolve](https://github.com/rollup/rollup-plugin-node-resolve) - 允許在 `node_modules` 中載入第三方模組
- [rollup-plugin-commonjs](https://github.com/rollup/rollup-plugin-commonjs) - 將 CommonJS 模組轉換至 ES6 以讓 Rollup 進行處理
- [rollup-plugin-bable](https://github.com/rollup/rollup-plugin-babel) - 支援 Babel 進行編譯處理
- [rollup-plugin-typescript2](https://github.com/ezolenko/rollup-plugin-typescript2) - 支援 TypeScript 進行編譯處理
- [rollup-plugin-terser](https://github.com/TrySound/rollup-plugin-terser) - 將打包的檔案進行 minify 處理

### 官方範例

Rollup 官方提供了建構函式庫與應用程式的範例可作為配置上參考：

- [rollup-starter-lib](https://github.com/rollup/rollup-starter-lib)
- [rollup-starter-app](https://github.com/rollup/rollup-starter-app)

## Rollup 與 Webpack 有什麼不同？

Rollup 與 Webpack 同樣作為打包器，有相同也有不相同的特性。

Webpack 幾乎可以說是開發 SPA 的標準配備，對於程式碼分割 (Code-splitting) 和靜態資源 (Static assets) 導入具有先天優勢，並且支持熱模組替換 (HMR)；而 Rollup 對於程式碼的 Tree-shaking 和 ES6 模組擁有演算法優勢上的支持。它們都有不同的用途。結論一句話就是：

> Use webpack for apps, and Rollup for libraries

這不是一個絕對的規則，實際上有很多網站和應用程式使用 Rollup 構建，也有大量的函式庫使用 Webpack。但是，對於應用程式使用 Webpack，而對於 函式庫使用 Rollup 是一個很好的經驗法則。

## 參考資料

- [rollup.js](https://rollupjs.org/)
- [Webpack and Rollup: the same but different](https://medium.com/webpack/webpack-and-rollup-the-same-but-different-a41ad427058c)
