---
title: 使用 Git 提交訊息產生 Change Log
tags: [Git, Conventional Commits, Semantic Versioning]
categories: [技術分享]
index_img: /2019/03/04/generating-change-log-with-git-commit-messages/cover.jpg
date: 2019-03-04 23:17:43
---

![cover](/2019/03/04/generating-change-log-with-git-commit-messages/cover.jpg)

> [Conventional Commits](https://www.conventionalcommits.org) 是一種編寫 git commit messages 的規範約定，它提供了一組用於建立提交歷史的簡單規則，可以透過自動化工具來追蹤版本變動紀錄（Change Log），這個約定與 [Semantic Versioning](http://semver.org/) 相吻合，可以在提交訊息中描述新功能（features）、bug 修復（fixes）和破壞性變更（breaking changes）。有一個共同約定的版本控制提交歷史，這在開源專案和團隊開發中尤為重要。

<!-- more -->

## Conventional Commits

遵循 Conventional Commits 的 commmit message 結構應如下所示：

```
<type>[optional scope]: <description>

[optional body]

[optional footer]
```

1. **fix**: type 為 `fix` 的提交表示在 codebase 中修復了一個 bug（這和語義化版本中的 [`PATCH`](http://semver.org/#summary) 相對應）。
2. **feat**: type 為 `feat` 的提交表示在 codebase 中新增了一個功能（這和語義化版本中的 [`MINOR`](http://semver.org/#summary) 相對應）。
3. **BREAKING CHANGE**: 在 optional body 或 footer 的起始位置帶有 `BREAKING CHANGE:` 的提交，表示引入了破壞性 API 變更（這和語義化版本中的 [`MAJOR`]((http://semver.org/#summary) 相對應）。破壞性變更可以是任意 type 的提交。
4. 其它情況: 除 `fix:` 和 `feat:` 之外的提交類型也是被允許的，例如 [@commitlint/config-conventional](https://github.com/conventional-changelog/commitlint/tree/master/%40commitlint/config-conventional) （基於 [the Angular convention](https://github.com/angular/angular/blob/22b96b9/CONTRIBUTING.md#-commit-message-guidelines)）中推薦的 `chore:`、`docs:`、`style:`、`refactor:`、`perf:`、`test:` 及其他。

## Commitizen

[Commitizen](https://github.com/commitizen/cz-cli) 可以用來協助建立符合 Conventional Commits 規範的提交訊息（commit messages）。

### Install

假設已經有一個專案目錄，在這個目錄下執行：

```
$ npm install commitizen --save-dev
```

這裡採用本地安裝方式，確保開發人員在每台電腦上運行相同的 Commitizen 版本。然後執行：

```
$ npx commitizen init cz-conventional-changelog --save-dev --save-exact
```

完成後，會在 `package.json` 檔案下加入：

```json package.json
...
  "config": {
    "commitizen": {
      "path": "./node_modules/cz-conventional-changelog"
    }
  }
```

然後，你可以在 `package.json` 檔案加入一條 npm script 用來運行 commitizen：

```json package.json
...
  "scripts": {
    "commit": "git-cz"
  }
```

如此，可使用 `npm run commit` 取代 `git commit` 協助建立符合規範的 git 提交訊息。

## Commitlint

[Commitlint](https://github.com/conventional-changelog/commitlint) 用來檢查你的 git 提交訊息。是否符合規範；若沒有通過，則該次提交不成功。

### Install

在專案目錄下執行：

```
$ npm install --save-dev @commitlint/config-conventional @commitlint/cli
```

然後在 `package.json` 檔案下加入：

```json package.json
...
  "commitlint": {
    "extends": ["@commitlint/config-conventional"]
  }
```

或者，透過以下指令建立一份配置檔案 `commitlint.config.js`：

```
$ echo "module.exports = {extends: ['@commitlint/config-conventional']}" > commitlint.config.js
```

接著，安裝 [Husky](https://github.com/typicode/husky) 以使用 `commit-msg` hook：

```
$ npm install --save-dev husky
```

安裝完成後，在 `package.json` 檔案下加入：

```json package.json
...
  "husky": {
    "hooks": {
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }  
  }
```

或者，建立 `.huskyrc` 配置檔案：

```json .huskyrc
{
  "hooks": {
    "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
  }  
}
```

如此，每次進行 git 提交就會確認訊息格式是否符合規範。

## Standard Version

如果你的提交歷史都符合規範，那麼就可以使用 [Standard Version](https://github.com/conventional-changelog/standard-version) 建立 Change Log 並釋出符合 [Semantic Versioning](http://semver.org/) 的版本號。

### Install

```
$ npm install --save-dev standard-version
```

然後在 `package.json` 檔案加入一條 npm script：

```json
  ...
  "scripts": {
    "release": "standard-version"
  }
```

當你要建立 change log 並釋出版本時，執行 `npm run release`。以下是常用的附加參數：

- 第一次釋出版本：`npm run release -- --first-release`
- 釋出預發行版本：`npm run release -- --prerelease <name>`
- 釋出特定版本號：`npm run release -- --release-as <version>`
- 釋出 major 版本：`npm run release -- --release-as major`
- 釋出 minor 版本：`npm run release -- --release-as minor`
- 釋出 patch 版本：`npm run release -- --release-as patch`

## References

[Conventional Commits](https://www.conventionalcommits.org)
[Semantic Versioning](http://semver.org/)
[Commitizen](https://github.com/commitizen/cz-cli)
[Commitlint](https://github.com/conventional-changelog/commitlint)
[Husky](https://github.com/typicode/husky)
[Standard Version](https://github.com/conventional-changelog/standard-version)
