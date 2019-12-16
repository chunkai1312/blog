---
title: 使用 Docker 安裝 GitLab CE 並啟用 GitLab Pages
tags: [GitLab, GitLab Pages, Docker, Traefik]
categories: [技術分享]
index_img: /2019/02/12/installing-gitlab-ce-and-enabling-gitlab-pages-with-docker/cover.jpg
date: 2019-02-12 22:39:25
---

![cover](/2019/02/12/installing-gitlab-ce-and-enabling-gitlab-pages-with-docker/cover.jpg)

> GitLab CE v8.16 版以後，支援 GitLab Pages 功能。就像 [GitHub Pages](https://pages.github.com/) 一樣，你可以為 GitLab 上的 project 建立靜態網站。

<!-- more -->

## 使用 Docker 安裝 GitLab

以下使用 [sameersbn/docker-gitlab](https://github.com/sameersbn/docker-gitlab) 提供的鏡像檔安裝 GitLab 環境。最簡單的入門方式是透過 [docker-compose](https://docs.docker.com/compose/)：

```bash
$ wget https://raw.githubusercontent.com/sameersbn/docker-gitlab/master/docker-compose.yml
```

使用以下命令啟動 GitLab：

```
$ docker-compose up
```

將瀏覽器指向 `http://localhost:10080` 並使用預設的使用者名稱和密碼登錄：

* username: `root`
* password: `5iveL!fe`

`sameersbn/gitlab` 鏡像檔提供了可用的 [配置參數](https://github.com/sameersbn/docker-gitlab#available-configuration-parameters) 依照你想要的方式配置 GitLab。

## 配置 GitLab Pages

### 事前準備

在設定 GitLab Pages 之前，需要：

1. 有一個單獨的網域名稱，在該網域下將提供 GitLab Pages。我們假設是 `example.io`。
2. 配置萬用字元（wildcard）DNS 記錄
3. （Optional）如果決定在 HTTPS 下提供 Pages，需要為該網域提供萬用字元（wildcard）憑證。
4. （Optional but recommended）請用共享的 GitLab Runners。

### DNS 配置

在 DNS 提供商配置一條 A 類型的萬用字元（wildcard）DNS 記錄，指向 GitLab 運行的主機。例如：

```
*.example.io. 1800 IN A 1.1.1.1
```

其中 `example.io` 為 Gitlab Pages 的網域；而 `1.1.1.1` 是 GitLab 服務的 IP 位址。

### 啟用 GitLab Pages

要啟用 GitLab Pages 功能，需要寫入以下配置參數：

```yml
...
    envrironment:
      - GITLAB_PAGES_ENABLED=true
      - GITLAB_PAGES_PORT=18080
      - GITLAB_PAGES_EXTERNAL_HTTP=:18080
...
```

GitLab Pages 預設跑在 docker 容器的 `8081` port。以下透過 docker-compose 設定對應到宿主機器的 `18080` port：

```yml
...
    ports:
    - "10080:80"
    - "10022:22"
    - "18080:8081"
...
```

## 使用 Traefik 反向代理

[Traefik](https://traefik.io/) 是一套 HTTP 反向代理和負載均衡器，與 Docker 有深度的整合，可以輕鬆部署微服務。其安裝配置方式可參照 Traefik [官方文件](https://docs.traefik.io/user-guide/docker-and-lets-encrypt/)。

假設底下是一份用於啟動 Traefik 服務的 `docker-compose.yml` 檔案：

```yml
version: '2'

services:
  traefik:
    image: traefik:1.5.4
    restart: always
    ports:
      - 80:80
      - 443:443
    networks:
      - web
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /opt/traefik/traefik.toml:/traefik.toml
      - /opt/traefik/acme.json:/acme.json
    container_name: traefik

networks:
  web:
    external: true
```

先建立一個 `web` 的 docker 虛擬網路：

```bash
$ docker network create web
```

啟動 Traefik 服務：

```bash
$ docker-compose up
```

然後我們修改原來 GitLab 的 `docker-compose.yml` 檔案，在 gitlab 服務下加入：

```yml
...
    labels:
      - "traefik.backend=gitlab"
      - "traefik.gitlab.frontend.rule=Host:examole.com"
      - "traefik.gitlab.port=80"
      - "traefik.pages.frontend.rule=HostRegexp:{subdomain:[a-z]+}.examole.io"
      - "traefik.pages.port=8081"
...
```

* 將 `traefik.gitlab.frontend.rule=Host:examole.com` 中的 `examole.com` 換成你指定的 GitLab 網域名稱。
* 將 `traefik.pages.frontend.rule=HostRegexp:{subdomain:[a-z]+}.examole.io` 中的 `examole.io` 換成你提供的 GitLab Pages 網域名稱。

## 建立 GitLab Pages

GitLab 官方提供了許多建立 GitLab Pages 的 [範例](https://gitlab.com/pages)。以下以 [GitBook](https://github.com/GitbookIO/gitbook) 為例。

我們在自架 GitLab 上建立一個 `docs` 的 group，並在底下建立 `gitbook` repo。然後將官方的 GitBook [範例](https://gitlab.com/pages/gitbook) clone 下來，push 至我們的 GitLab repo。

官方預設已經寫好 `.gitlab-ci.yml` 腳本。如果 GitLab CI 運行沒問題，就會部署至 GitLab Pages。

> 需要安裝 GitLab Runner 以執行 GitLab CI pipelines。

打開瀏覽器，進入 `http://docs.example.io/gitbook` 查看內容是否正確。

## References

[Docker Gitlab](http://www.damagehead.com/docker-gitlab/)
[GitLab Pages](https://docs.gitlab.com/ce/user/project/pages/index.html)
[Traefik](https://traefik.io/)