---
title: 使用 AngularJS v1.5+ 建立 component-based 應用程式
tags: [AngularJS, 前端工程]
categories: [技術分享]
index_img: /2016/12/24/component-based-application-using-angularjs-v1.5+/cover.jpg
date: 2016-12-24 02:02:30
---

![cover](/2016/12/24/component-based-application-using-angularjs-v1.5+/cover.jpg)

> `angular.component()` 是在 Angular 1.5 版本中新增的方法，它是基於 `angular.directive()`，更簡單地說，即是 **controller + template** 。有了 `angular.component()` 這個新利器，我們可以很容易地將 Angular 寫成像 React 一樣的元件化應用程式 (component-based application)。

<!-- more -->

## Code Example

直接來看看實際的例子，以下是 [React](https://facebook.github.io/react) 官方網站上一個簡單的 todo app 範例：

```js
class TodoApp extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.state = {items: [], text: ''};
  }

  render() {
    return (
      <div>
        <h3>TODO</h3>
        <TodoList items={this.state.items} />
        <form onSubmit={this.handleSubmit}>
          <input onChange={this.handleChange} value={this.state.text} />
          <button>{'Add #' + (this.state.items.length + 1)}</button>
        </form>
      </div>
    );
  }

  handleChange(e) {
    this.setState({text: e.target.value});
  }

  handleSubmit(e) {
    e.preventDefault();
    var newItem = {
      text: this.state.text,
      id: Date.now()
    };
    this.setState((prevState) => ({
      items: prevState.items.concat(newItem),
      text: ''
    }));
  }
}

class TodoList extends React.Component {
  render() {
    return (
      <ul>
        {this.props.items.map(item => (
          <li key={item.id}>{item.text}</li>
        ))}
      </ul>
    );
  }
}

ReactDOM.render(<TodoApp />, mountNode);
```

這個範例定義的 2 個 component。 `TodoApp` 為 root component，包含一個 input text field 用來輸入 todo；`TodoList` 則用來展示 todo list。

接著我們來看看使用 Angular 1.5 提供的 `angular.component()` 來寫這段範例會是什麼樣子。以下是我將上面的 React 範例寫成 Angular 版本：

```js
class TodoAppController {
  constructor() {
    this.items = [];
  }

  handleSubmit(e) {
    e.preventDefault();
    var newItem = {
      text: this.text,
      id: Date.now()
    };
    this.items = this.items.concat(newItem);
    this.text = '';
  }
}

const todoAppComponent = {
  controller: TodoAppController,
  template: `
    <div>
      <h3>TODO</h3>
      <todo-list items="$ctrl.items"></todo-list>
      <form ng-submit="$ctrl.handleSubmit($event)">
        <input ng-model="$ctrl.text" />
        <button>Add # {{$ctrl.items.length + 1}}</button>
      </form>
    </div>
  `
}

class TodoListController { }

const todoListComponent = {
  bindings: { items: '<' },
  contorller: TodoListController,
  template: `
    <ul>
      <li ng-repeat="item in $ctrl.items">{{item.text}}</li>
    </ul>
  `
}

angular
  .module('todoApp', [])
  .component('todoApp', todoAppComponent)
  .component('todoList', todoListComponent)
```

這裡我們使用 ES6/ES2015 的 `class` 來定義 controller，template的 `$ctrl` 會指向 controller 的 `this`，此為 angular 所預設。您也可以在 `component` 加上 `controllerAs` 選項將 `$ctrl` 命名為您想指定的變數名稱。

`bindings` 的前身其實就是 `angular.directive()` 選項的 `scope`，如果需要外部的資料引入 component，我們可以定義於此。換作 React 的說法，大概就是 `props`。`bindings` 的屬性定義 `<` 符號表示單向綁定，有別於 `=` 符號的雙向綁定，它只作用於該 component，不會去改變外部資料。

我們可以發現，用上 `angular.component()` 後，除了 React 用 JSX，而 Angular 用 template 以外，我們還在 Angular 版本用了 `ngModel` 讓程式少寫了一點，其他看起來其實已經跟 React 版本相去不遠。

將 Angular 應用程式元件化之後，我們還可以結合在 React 生態圈被廣泛使用的 [Redux](http://redux.js.org) 做狀態管理，讓整個程式資料流變得可預測、更清楚。

TodoMVC 是一個經典範例，也是比上面稍微複雜一點的例子，這裡我用 [ng-redux](https://github.com/angular-redux/ng-redux) 寫了一個 [demo](https://ng-redux-todomvc.herokuapp.com)，並附上 Github [repo](https://github.com/chunkai1312/ng-redux-todomvc) 供參考。


## Summary
Angular 1 曾經紅極一時，但在 2016 年，可能相對被認為過時了一點。話雖如此，許多使用 Angular 1 開發的舊專案還是要繼續維護。從目前幾個熱門的前端框架，如 [React](https://facebook.github.io/react)、[Angular 2](https://angular.io) 、[Vue](https://vuejs.org) 來看，不難發現前端應用程式元件化已經是大勢所趨，將既有程式使用 `angular.component()` 重構，除了讓程式變得好維護外，日後若要升級 Angular 2，甚至遷移到 React 或 Vue，都會比較容易一些。


## References
- [Understanding Components](https://docs.angularjs.org/guide/component)
- [Angular 1 using redux architecture](http://blog.grossman.io/angular-1-using-redux-architecture)
