---
title: 使用 React 作為 Express 的樣板引擎建構 Universal 應用程式
tags: [React, Express, NodeJS, Universal JavaScript]
categories: [技術分享]
index_img: /2017/05/01/using-react-as-template-engine-for-universal-express-applications/cover.jpg
date: 2017-05-01 00:09:26
---

![](/2017/05/01/using-react-as-template-engine-for-universal-express-applications/cover.jpg)

> 使用 [Express](https://expressjs.com) 建構頁面時，可能會搭配各式的 [template engines](https://github.com/expressjs/express/wiki?&_ga=1.57492066.918787517.1490850939#template-engines) 來協助渲染視圖 (views) 。 在 React 社區中有 [express-react-views](https://github.com/reactjs/express-react-views) 使用 React 作為 template engine 的伺服端渲染 (server-side rendering) 例子。不過該方案僅用在伺服端輸出靜態頁面，不支援 client side mounting 。相較之下，Paypal 的 [react-engine](https://github.com/paypal/react-engine) 提供一個 universal 的解決方案，並可搭配 [react-router](https://github.com/reactjs/react-router) 一起使用。

<!-- more -->

## Creating an Express Application

為了簡單示例，這邊直接使用 `express-generator` 建立一個名為 `express-react-engine-example` 的專案：

```
$ npm install -g express-generator
$ exresss express-react-engine-example
```

接著進入建立的專案目錄，並安裝預設依賴套件：

```
$ cd express-react-engine-example && npm install
```

然後執行：

```
$ npm start
```

此時 Express 應用程式會跑在 `http://localhost:3000/` 。

## Set up Babel and Browserify

### Babel

在開發模式下，我們使用 [Babel](https://babeljs.io) 進行轉碼，請安裝以下依賴套件：

```
$ npm install --save-dev babel-register babel-preset-es2015 babel-preset-react
```

[.babelrc](http://babeljs.io/docs/usage/babelrc/) 是 Babel 用來設置轉碼規則與定義 plugins 的文件。我們在根目錄下新增檔案 `.babelrc` ：

```json
{
  "presets": ["es2015", "react"]
}
```

`express-react-engine-example/bin/www` 是 `express-generator` 預設產生的程式的進入點，我們需要在該文件頂部引入 [babel-register](https://babeljs.io/docs/usage/babel-register)：

```sh
#!/usr/bin/env node

require('babel-register');
```

如此，只要使用 `require` 載入副檔名為 `.es6`、`.es`、`.jsx`、`.js` 的文件，即會使用 babel 進行轉碼。

### Browserify

如果要開發複雜的 Single Page Spplication ，使用 [Webpack](https://webpack.js.org) 仍是目前前端模組化方案的首選。不過在我們的場景下，使用 [Browserify](http://browserify.org) 即可滿足需求。除了 `browserify` 外，我們還需要透過 `babelify` 將 ES6 程式碼轉換成 ES5 以兼容瀏覽器：

```
$ npm browserify babelify
```

安裝好依賴檔案後，我們需要改寫 `package.json` 中的 `scripts` 指令：

```json
"scripts": {
  "start": "browserify -t babelify views/index.js -o public/javascripts/bundle.js && node ./bin/www"
},
```

我們將 browserify 的 entry 定義在 `express-react-engine-example/views/index.js` ，並將打包的檔案輸出至 `express-react-engine-example/public/javascripts/bundle.js` 。

## Using React as Template Engine

`express-generator` 預設使用 `jade` (現已更名為 [pug](https://github.com/pugjs/pug)) 作為 template engine，我們將使 `react-engine` 取代之。請安裝以下依賴：

```
$ npm install --save react-engine react react-dom react-router@3
```

### Setting React Engine on Server Side

開啟根目錄下 `app.js` 檔案，引入 `react-engine` 模組：

```js
var ReactEngine = require('react-engine');
```

在設置 `view engine` 部分，改寫設定如下：

```js
// view engine setup
var engine = ReactEngine.server.create({
  routes: require('./views/routes'),
  routesFilePath: path.join(__dirname, 'views/routes.js'),
});

app.engine('js', engine);
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'js');
app.set('view', require('react-engine/lib/expressView'));
```

`react-engine` 整合了 `react-router` ，為了匹配路由規則渲染出正確的 component ，請在 express 的 render function 改寫為 `res.render(req.url, [,locals])` 。如果有傳入 locals 物件，則會以 `PropTypes` 的形式讓 React Component 接收。

### Setting React Engine on Client Side

由於我們使用 `react-engine` 取代 `jade`，因此可以移除預設存在的檔案 `layout.jade`、`index.jade`、`error.jade`。

請在 `express-react-engine-example/views` 目錄下建立 `index.js` 檔案：

```js
'use strict';

// import the react-router routes
var Routes = require('./routes.jsx');

// import the react-engine's client side booter
var ReactEngineClient = require('react-engine/lib/client');

// boot options
var options = {
  routes: Routes,

  // supply a function that can be called
  // to resolve the file that was rendered.
  viewResolver: function(viewName) {
    return require('./views/' + viewName);
  }
};

document.addEventListener('DOMContentLoaded', function onLoad() {
  // boot the app when the DOM is ready
  ReactEngineClient.boot(options);
});
```

然後在 `express-react-engine-example/views` 目錄下建立 `routes.js` 檔案：

```js
import React from 'react';
import { Router, Route, IndexRoute, browserHistory } from 'react-router';
import Layout from './Layout';
import IndexPage from './IndexPage';
import ErrorPage from './ErrorPage';

export default (
  <Router history={browserHistory}>
    <Route path='/' component={Layout}>
      <IndexRoute component={IndexPage} />
      <Route path='/*' component={ErrorPage} />
    </Route>
  </Router>
);
```

### Creating React Components

接下來，我們要撰寫 React Components 對應原先的 `jade` templates。在 `express-react-engine-example/views` 目錄下新增檔案 `Layout.js`、`IndexPage.js`、`ErrorPage.js`。

`express-react-engine-example/views/Layout.js`：

```js
import React, { Component } from 'react'

class Layout extends Component {
  render () {
    return (
      <html>
        <head>
          <title>{this.props.title}</title>
          <link rel="stylesheet" href="/stylesheets/style.css" />
        </head>
        <body>
          {this.props.children}
        </body>
      </html>
    )
  }
}

export default Layout
```

`express-react-engine-example/views/IndexPage.js`：

```js
import React, { Component } from 'react'

class IndexPage extends Component {
  render () {
    return (
      <div>
        <h1>{this.props.title}</h1>
        <p>Welcome to {this.props.title}</p>
      </div>
    )
  }
}

export default IndexPage
```

`express-react-engine-example/views/ErrorPage.js`：

```js
import React, { Component } from 'react'

class ErrorPage extends Component {
  render () {
    return (
      <div>
        <h1>{this.props.message}</h1>
        <h2>{this.props.error.status}</h2>
      </div>
    )
  }
}

export default ErrorPage
```

## Run Your Express Application

執行：

```
$ npm start
```

此時， browserify & babelify 將 `views` 目錄下的檔案打包成一份 `bundle.js`，然後再啟動 Express 應用程式。打開瀏覽器，進入網址 `http://localhost:3000/`，顯示 `Welcome to Express` 即完成 `react-engine` 的基本設定。

範例程式[在此](https://github.com/chunkai1312/express-react-engine-example)。

另外，我建立一個名為 [express-react-engine-boilerplate](https://github.com/chunkai1312/express-react-engine-boilerplate) 的專案，這是一個功能更豐富的樣板，整合了 [watchify](https://github.com/substack/watchify) 以及 [uglify-js](https://github.com/mishoo/UglifyJS2) 等 workflow ，並使用 [browserify-hmr](https://github.com/AgentME/browserify-hmr) 實現模組熱替換 (Hot Module Replacement)，針對程式碼修改或調整的部份，不必重新整理瀏覽器而達到即時刷新頁面的效果，讓開發過程更為快速流暢。

順便一提，[Next.js](https://github.com/zeit/next.js) 是一個新興的框架，基於 React 的 Universal JavaScript Framework ，目前已有超過一萬個 stars 的關注，不妨可以參考看看。
