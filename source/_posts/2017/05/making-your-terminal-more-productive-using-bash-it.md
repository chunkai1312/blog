---
title: 使用 Bash-it 讓 Terminal 更具生產力
tags: [Bash-it, Bash, Shell]
categories: [工具應用]
index_img: /2017/05/27/making-your-terminal-more-productive-using-bash-it/cover.jpg
date: 2017-05-27 21:50:35
---

![cover](/2017/05/27/making-your-terminal-more-productive-using-bash-it/cover.jpg)

> Bash 是絕大多數 Unix-like OS 預設的 Shell ，在 zsh 上有 [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) 這樣的神器，Bash 也有 [Bash-it](https://github.com/Bash-it/bash-it) 可以讓您的 terminal 更容易使用。

<!-- more -->

## Install

安裝 `Bash-it` 的最簡單的方法是執行以下命令：

```bash
git clone --depth=1 https://github.com/Bash-it/bash-it ~/.bash-it
cd ~/.bash-it
./install.sh
```

`Bash-it` 的設定檔案於 `~/.bash_profile` 或 `~/.bashrc`，可以自定義修改配置。

## Update

更新 `Bash-it` 版本只需要執行：

```
bash-it update
```

就這樣。

## Help Screens

```
bash-it show aliases        # shows installed and available aliases
bash-it show completions    # shows installed and available completions
bash-it show plugins        # shows installed and available plugins
bash-it help aliases        # shows help for installed aliases
bash-it help completions    # shows help for installed completions
bash-it help plugins        # shows help for installed plugins
```

## Custom Theme

目前 `Bash-it` 有 50+ 個 [Themes](https://github.com/Bash-it/bash-it/wiki/Themes) 可供選擇，在路徑 `.bash_it/themes` 中可以找到這些主題配置。預設的 theme 是 `bobby` ，可在 `~/.bash_profile` 或 `~/.bashrc` 更改設定換成你想要的：

```
# Use the "powerline-multiline" theme
export BASH_IT_THEME="powerline-multiline"

# Use a theme outside of the Bash-it folder
export BASH_IT_THEME="/home/foo/my_theme/my_theme.theme.bash"
```

執行 `BASH_PREVIEW=true reload` 可以預覽這些 themes 。

## Uninstall

進入 `Bash-it` 安裝目錄下執行 `uninstall.sh` 即可：

```
cd $BASH_IT
./uninstall.sh
```

這將恢復您以前的 Bash profile，並且在腳本執行完成後，刪除 `Bash-it` 目錄 (`rm -rf $BASH_IT`) 並啟動新的 shell 。

## References

[Bash-it : Bash Framework To Make Your Terminal Cool](https://itsfoss.com/bash-it-terminal-tool)
[Bash-it](https://github.com/Bash-it/bash-it)