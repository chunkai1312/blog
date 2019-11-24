---
title: 使用 React Context API 實現 i18n 多國語言
tags: [React, F2E]
categories: [技術分享]
index_img: /2019/11/15/using-react-context-api-to-support-for-i18n/cover.jpg
date: 2019-11-15 21:51:46
---

![cover](/2019/11/15/using-react-context-api-to-support-for-i18n/cover.jpg)

> 本文透過實現一個簡易的 i18n (internationalization) 功能來演示 React Context API 的基本應用。

<!-- more -->

## React Context API

React Context 提供 component tree 傳遞資料的方法，而不必在每個級別傳遞 props。這很適合用在對於 React 應用程式中是屬於全域的資料，例如當前認證的用戶、主題或語言偏好。

較早版本的 React 提供的 Context API 官方並不建議使用。React 16.3 發布的 New Context API 使用 [Render Props](https://reactjs.org/docs/render-props.html) pattern 實現將資料傳遞給 children。

React 16.3+ Context API 核心包含：`React.createContext`、`Context.Provider`、`Context.Consumer`。

### React.createContext

```js
const MyContext = React.createContext(defaultValue);
```

`React.createContext` 用於建立一個 Context 物件，該物件包含 `Provider` 和 `Consumer` 兩個 component。

### Context.Provider

```js
<MyContext.Provider value={/* some value */}>
```

`Context.Provider` 提供 context 的值，通常會放在 component tree 接近最上層的位置。

### Context.Consumer

```js
<MyContext.Consumer>
  {value => /* render something based on the context value */}
</MyContext.Consumer>
```

`Context.Consumer` 接收 context 的值，可以在屬於 Provider 子元件的任何一層使用。

## 使用 Context API 實現 i18n 功能

接下來會以實作 i18n 功能的例子說明如何使用 React Context API。我們以 [Create React App](https://create-react-app.dev) 快速建立一個 React 應用程式範例，並建立 `src/i18n` 目錄。

### I18nContext

我們使用 `React.createContext` 建立一個 `I18nContext` 及定義其初始狀態。這裡我們預計會提供三種方法來預計來實現多國語言轉換功能：
  - `t(key: string): string`: 將定義的鍵值翻譯成對應的語系內容
  - `getLocale()`: 取得當前語系
  - `setLocale()`: 設置/變更當前語系

```js
import { createContext } from 'react';

const I18nContext = createContext({
  t: () => '',
  getLocale: () => {},
  setLocale: () => {},
});

export default I18nContext;
```

### I18nProvider

所以我們需要實作一個 `I18nProvider` 在 tree 的上層使用來提供 context 的值：

```js
import React, { useState } from 'react';
import I18nContext from './I18nContext';

const I18nProvider ＝ (props) => {
  const [ locale, setLocale ] = useState('en-US');

  const i18n = { 
    t: key => props.translations[locale][key],
    getLocale: () => locale,
    setLocale: locale => setLocale(locale),
  };

  return (
    <I18nContext.Provider value={i18n}>
      {props.children}
    </I18nContext.Provider>
  );
};

export default I18nProvider;
```

### useI18n

為了讀取 context 及訂閱變更的內容，我們實作一個 `useI18n` 的 React hook 方便我們在其他 functional component 使用多國語言。我們使用 `useContext` 接收 `I18nContext` 取得當前 context 的狀態：

```js
import { useContext } from 'react';
import I18nContext from './I18nContext';

function useI18n () {
  const i18n = useContext(I18nContext);
  return i18n;
}

export default useI18n;
```

### withI18n

自 React 16.8 [Hooks](https://reactjs.org/docs/hooks-intro.html) 功能釋出後，functional component 已經可以藉用 `useState` 與 `useEffect` 等 hooks 滿足大部分情境。如果要使用傳統的 class component 時，我們可以實作一個 [HOC (higher-order component)](https://reactjs.org/docs/higher-order-components.html) 來包裝 Context API 來使用 i18n：

```js
import React from 'react';
import I18nContext, { I18nContextProps } from './I18nContext';

export const withI18n = () => {
  return WrappedComponent => {
    const ComponentWithI18n = props => (
      <I18nContext.Consumer>
        {i18n => <WrappedComponent {...i18n} {...props} />}
      </I18nContext.Consumer>
    )
    return ComponentWithI18n
  }
};

export default withI18n;
```

最後在 `src/i18n/index.js` 將各個模組導出，方便在其他檔案中 import 它們。

```js
export { default as I18nProvider } from './I18nProvider';
export { default as useI18n } from './useI18n';
export { default as withI18n } from './withI18n';
```

## 在 React 中使用 I18nContext

接著可以在 React 應用程式使用我們建立好的 I18nContext 提供多國語言功能了。我們需要建立多個語系檔來決定轉譯的內容，在 `src/locales` 是我們要放置各語系檔案的目錄。以下以英文及中文為例：

`src/locales/en-US.js`
```js
export default {
  'getting_started': 'Edit <code>src/App.tsx</code> and save to reload.',
  'learn_react': 'Learn React',
};
```

`src/locales/zh-TW.js`
```js
export default {
  'getting_started': '編輯 <code>src/App.tsx</code> 並儲存以重新載入。',
  'learn_react': '學習 React',
};
```

在 React 應用程式入口 `src/index.js`，在 component tree 的上層引入 `I18nProvider`，並將語言包內容注入 Provider：

```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';
import { I18nProvider } from './i18n';

const locales = [ 'en-US', 'zh-TW' ];
const translations = {
  'en-US': require('./locales/en-US').default,
  'zh-TW': require('./locales/zh-TW').default,
}

ReactDOM.render(
  <I18nProvider locales={locales} translations={translations}>
    <App />
  </I18nProvider>,
  document.getElementById('root')
);
```

然後在需要使用多國語言的 functional component 引入 `useI18n` hook 使用 `t` 函式來轉換對應的語系內容，`setLocale` 用來設置轉換語系：

```js
import React from 'react';
import logo from './logo.svg';
import './App.css';
import { useI18n } from './i18n';

const App: React.FC = () => {
  const { t, setLocale } = useI18n();

  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p dangerouslySetInnerHTML={{ __html: t('getting_started') }} />
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          {t('learn_react')}
        </a>
      </header>
      <footer className="App-Footer">
        <span className="App-Locale" onClick={() => setLocale('en-US')}>English</span>
        {' | '}
        <span className="App-Locale" onClick={() => setLocale('zh-TW')}>正體中文</span>
      </footer>
    </div>
  );
}

export default App;
```

最後執行 `npm start` 查看結果。

![](/2019/11/15/using-react-context-api-to-support-for-i18n/react-i18n-example.jpg)

## 總結

透過 React Context API 可以在 component 之間共享數據，結合 React Hook 與 HOC 可以很容易實現可複用的第三方 library。我們經由一個簡易的 i18n 功能展示如何使用它們，完整的範例可以參考 [react-i18n-example](https://github.com/chunkai1312/react-i18n-example)。

