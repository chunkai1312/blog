---
title: 使用 Shell Script 建立命令列程式
tags: [Shell, Bash]
categories: [技術分享]
index_img: /2017/05/30/building-cli-program-with-shell-script/cover.jpg
date: 2017-05-30 15:12:48
---

![](/2017/05/30/building-cli-program-with-shell-script/cover.jpg)

> 最近開始需要撰寫一些 shell script 來執行 DevOps 自動化的工作。這篇主要記錄如何使用 shell script 來建立一個簡單的命令列程式。

<!-- more -->

## Writing a Shell Script

為了成功地建立與執行一個 shell script ，我們需要做三件事：

1. 寫一個 script
2. 使 script 可執行
3. 將 script 放置到 shell 能夠找到的地方

### Script File Format

以一個印出 `Hello World!` 的程式為例：

```bash
#!/bin/bash
# My first script
echo "Hello World!"
```

1. 第一行是 [Shebang](https://en.wikipedia.org/wiki/Shebang_%28Unix%29) ，告訴系統要用什麼樣的直譯器來執行該 script 。這裡我們使用的是 UNIX 系統最常見的 [Bash](https://en.wikipedia.org/wiki/Bash_%28Unix_shell%29) 。
2. 第二行是註解，在 `#` 符號之後的都會被忽略。
3. 第三行我們使用 `echo` 命令印出 `Hello World!` 。

### Executable Permissions

為了讓 script 可執行，我們需要給予 script file 可執行權限。以 `hello_world` 為例：

```bash
$ chmod 755 hello_world
```

對於 script file，有兩個常見的權限設置：

1. 為 script 指定 `755` 讓所有人都能執行
2. 為 script 指定 `700` 只有擁有者能夠執行。

注意為了能夠執行 script，script 必須是可讀取的。

### Script File Location

設定權限之後，就能執行 script ：

```bash
$ ./hello_world
Hello World!
```

為了執行 script ，必須將確切路徑置於 script 名稱前。若沒有，就會得到如下結果：

```bash
$ hello_world
-bash: hello_world: command not found
```

這是因為該 script 位置不在 PATH 變數之目錄清單中。可輸入以下指令檢視目前 PATH 變數內容：

```
$ echo $PAH
```

若 script 就在這個清單裡的任何一個目錄下，就可以在任何地方執行 scipt 。大部分 Linux 分支套件會將 PATH 變數包含使用者家目錄下的 `bin` 目錄，允許使用者執行自有程式。若 PATH 變數裡沒有該目錄，可以編輯以下內容到 `.bashrc` 裡：

```
export PATH="~bin:$PATH"
```

完成變更後它會影響每個新終端機的 session 。要將變動套用到現行終端機 session，必須讓 shell 重新讀取 `.bashrc` 檔，可透過 `sourcing` 來完成：

```bash
$ . .bashrc
```

確認設定好 PATH 環境變數後，我們在使用者的家目錄下建立一個 `bin` 目錄，然後將 script 置於此並執行：

```bash
$ cd ~
$ mkdir bin
$ mv hello_world bin
$ hello_world
Hello World!
```

#### Good Locations For Scripts

1. `~/bin` 目錄是存放為個人使用 script 的好地方。
2. 若寫了一個允許系統裡所有人使用的 script，傳統位置會是 `/usr/local/bin`。
3. 供系統管理員使用的 script 經常放到 `/usr/local/sbin` 目錄下。
4. 多數情況下，本機提供的軟體，不論是 script 還是編譯過的程式，應該都放在 `/usr/local` 目錄下，而非 `/bin` 或 `/usr/bin` 目錄。

## Building a Simple CLI Program

這裡提供一個簡單的 CLI (command-line interface) [程式範例]((https://github.com/chunkai1312/shell-template)：

```bash
#!/bin/bash

PROGRAM=$(basename $0)
VERSION="1.0.0"
DESCRIPTION="This is a script template."

usage () {
  echo "
  Usage: $PROGRAM [options]

  $DESCRIPTION

  Options:

    -h, --help     output usage information
    -v, --version  output the version number
    -p, --print    print \"Hello World!\"
  "
}

print () {
  echo 'Hello World!'
}

# process command line options

while [[ -n $1 ]]; do
  case $1 in
    -h | --help)      usage
                      exit
                      ;;
    -v | --version)   echo $VERSION
                      exit
                      ;;
    -p | --print)     print
                      exit
                      ;;
    *)                usage >&2
                      exit 1
                      ;;
  esac
  shift
done
```

這是一個不到 50 行的 script ，原理是使用 `while-do` 迴圈一個一個讀取命令列參數，若符合參數條件就執行對應的指令或函式。假設 script 名稱為 `program` ，並給予可執行權限且至於 PATH 環境變數之目錄清單下。

執行 `program -h` 顯示程式使用方式：

```bash
$ program -h

  Usage: program [options]

  This is a script template.

  Options:

    -h, --help     output usage information
    -v, --version  output the version number
    -p, --print    print "Hello World!"

```

執行 `program -v` 顯示程式版本：

```bash
$ program -v
1.0.0
```

執行 `program -p` 印出 `Hello World!` ：

```bash
$ program -p
Hello World!
```

## References

[Writing Your First Script And Getting It To Work](http://linuxcommand.org/lc3_wss0010.php)
[TLCL](http://billie66.github.io/TLCL/)