---
title: MongoDB 備份與還原
tags: [MongoDB, mongodump, mongorestore]
categories: 工具應用
index_img: /2020/09/28/mongodump-and-mongorestore/cover.jpg
date: 2020-09-28 16:03:36
---

> 以往習慣用 `db.copyDatabase()`，來備份 MongoDB 資料庫，不過從 MongoDB 4.2 版開始，官方已經移除 **copydb** 命令，包含 `db.copyDatabase()` 在內，僅支援 MongoDB 4.0 或更早的版本。MongoDB 4.2+ 版本則必須使用 `mongodump` 及 `mongorestore` 來 Copy/Clone 資料庫。

![](/2020/09/28/mongodump-and-mongorestore/cover.jpg)

<!-- more -->

## 備份資料庫

假設在本地端有一個 `test` 資料庫運行在預設 27017 port，可以使用 [mongodump](https://docs.mongodb.com/database-tools/mongodump/#bin.mongodump) 指令打包備份：

```
$ mongodump --archive="mongodump-test-db" --db=test
```

以上將備份資料庫打包成 `mongodump-test-db` 檔案。

## 還原資料庫

假設要將打包好的 `test` 資料庫還原到名為 `examples` 資料庫，可以使用 [mongorestore](https://docs.mongodb.com/database-tools/mongorestore/#bin.mongorestore) 進行資料還原：

```
$ mongorestore --archive="mongodump-test-db" --nsFrom='test.*' --nsTo='examples.*'
```

可以根據環境需求加入其他選項，例如指定 uri 或 host、username、password 進行身份驗證。使用 `mongodump --help` 及 `mongorestore --help` 查看其他可用選項。

## 參考資料 

[Copy/Clone a Database](https://docs.mongodb.com/database-tools/mongodump/#mongodump-example-copy-clone-database)