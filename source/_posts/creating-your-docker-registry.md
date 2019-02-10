---
title: 建立自己的 Docker Registry
date: 2017-05-24 17:13:03
tags:
  - Docker
  - Docker Registry
  - DevOps
---

[Docker Hub](https://hub.docker.com) 是 [Docker](https://www.docker.com) 官方維護的公共倉庫。但是如果映像檔 (image) 不便公開，可能就不適合使用公共倉庫，這時候我們就需要 [Docker Registry](https://docs.docker.com/registry/) 來建立自己的私有倉庫。

<!-- more -->

## Install Docker Registry

登入欲建立 Docker Registry 的遠端 Server，從 Docker Hub 拉取 Docker Registry 映像檔並啟動： 

```bash
$ sudo docker run -d -p 5000:5000 --restart=always --name registry registry:2
```

這樣我們就已經在遠端 Server 上安裝好 Docker Registry 。

## Install Docker Registry Frontend (Optional)

目前已有許多第三方為 Docker Registry 實現的 Web UI 介面，方便瀏覽在 registry 上的 images 。綜合目前在 Docker Hub 與 GitHub 上的 stars 數量，比較多人使用的是這套 [Docker Registry Frontend](https://hub.docker.com/r/konradkleine/docker-registry-frontend) ，以 AngularJS 開發的 UI 介面。其安裝方式也相當簡單：

```bash
sudo docker run \
  -d \
  -e ENV_DOCKER_REGISTRY_HOST=ENTER-YOUR-REGISTRY-HOST-HERE \
  -e ENV_DOCKER_REGISTRY_PORT=ENTER-PORT-TO-YOUR-REGISTRY-HOST-HERE \
  -p 8080:80 \
  konradkleine/docker-registry-frontend:v2
```

完成啟動後，您可以打開瀏覽器進入 `http://localhost:8080` 查看 registry 狀態。

## Setup for Insecure Registry

以下 IP `xxx.xxx.xxx.xxx` 表示我們遠端伺服器安裝 Docker Registry 的所在位址。

因為我們遠端的 Docker Registry 並沒有安裝 certificate，因此必須在本地 `/etc/default/docker` 加入：

```
DOCKER_OPTS="$DOCKER_OPTS --insecure-registry=xxx.xxx.xxx.xxx:5000"
```

然後重啟 Docker 服務：

```bash
$ sudo service docker restart
```

如果您使用 **Docker for Mac** ，打開應用程式找到 Preferences：

![](/2017/05/24/creating-your-docker-registry/docker_for_mac.png)

在 `Insecure registries` 列表加入 registry 位址，然後按下 `Apply & Restart` 等待 Docker 重啟完畢就可以了。

## Test Your Docker Registry

為了測試我們建立的 Docker Registry ，我們需要從 Docker Hub 上 pull 一個 image 或自行建立 image 來做測試。此處範例我們在本地端從 Docker Hub 下載一個 `ubuntu` 映像檔：

```bash
$ sudo docker pull ubuntu
```

將下載後的映像檔 tag 指向我們所建立的 registry ：

```bash
$ sudo docker tag ubuntu xxx.xxx.xxx.xxx:5000/ubuntu
```

上傳 image 至 自建 registry ：

```bash
$ sudo docker push xxx.xxx.xxx.xxx:5000/ubuntu
```

從自建 registry 下載 image ：

```bash
$ sudo docker pull xxx.xxx.xxx.xxx:5000/ubuntu
```

Docker Registry 也提供 HTTP API，例如查看 registry 上的 repositories ：

```bash
$ curl http://xxx.xxx.xxx.xxx:5000/v2/_catalog 
```

確認某個 image 的 tag ：

```bash
$ curl http://xxx.xxx.xxx.xxx:5000/v2/SOME_IMAGE/tags/list 
```

更多關於 Docker Registry 的其他用法可參閱[官方文件](https://docs.docker.com/registry) 。
