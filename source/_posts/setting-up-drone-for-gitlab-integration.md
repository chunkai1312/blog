---
title: 配置 Drone 與 GitLab 整合
date: 2018-06-18 12:02:48 
tags:
  - Drone
  - GitLab
  - Docker
  - NodeJS
  - Git
  - CI
---

Drone 是一個以 Go 編寫、基於 Docker 的 CI/CD 平台。本篇文章簡單介紹 Drone 如何安裝與 GitLab 整合。

<!-- more -->

# Prepare Your GitLab

首先必須準備 GitLab 環境。依照需求，你可以選擇 [self-hosted](https://about.gitlab.com/pricing/#self-hosted) 或者 [GitLab.com](https://about.gitlab.com/pricing/#gitlab-com)。自行架設安裝 GitLab 的方式很多種，請參考[官方說明](https://about.gitlab.com/installation/)。如果你偏好使用 Docker，個人推薦使用 [docker-gitlab](http://www.damagehead.com/docker-gitlab/) 這個專案來快速安裝部署你的 GitLab 平台。

準備好 GitLab 環境之後，你必須在 GitLab 上註冊應用程式取得 OAuth `client` 及 `secret`。假設你的 Drone Server 位置是 `http://YOUR_DRONE_HOST`，則 Callback URL 就填入 `http://YOUR_DRONE_HOST/authorize`。Scopes 選項記得要勾選 `api`，以取得足夠的 GitLab API 權限。

![](/2018/06/18/setting-up-drone-for-gitlab-integration/add_new_application.png)

# Install Drone

Drone 支持 GitLab 8.2 及更高版本。請確認已經安裝，[Docker](https://www.docker.com/) 以及 [Docker Compose](http://docs.docker.com/compose/)。編寫 `docker-compose.yml` 檔案並使用以下環境變量配置 Drone 容器。

```
version: '2'

services:
  drone-server:
    image: drone/drone:0.8

    ports:
      - 80:8000
      - 9000
    volumes:
      - /var/lib/drone:/var/lib/drone/
    restart: always
    environment:
      - DRONE_GITLAB=true
      - DRONE_GITLAB_CLIENT=95c0282573633eb25e82
      - DRONE_GITLAB_SECRET=30f5064039e6b359e075
      - DRONE_GITLAB_URL=http://gitlab.mycompany.com
      - DRONE_SECRET=${DRONE_SECRET}

  drone-agent:
    image: drone/agent:0.8

    restart: always
    depends_on:
      - drone-server
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - DRONE_SERVER=drone-server:9000
      - DRONE_SECRET=${DRONE_SECRET}
```

將 GitLab 上取得的 `Application Id` 及 `Secret` 分別填入 `DRONE_GITLAB_CLIENT` 及 `DRONE_GITLAB_SECRET`。然後執行 `docker-compose up -d` 就啟動好 Drone 了。其他可選的環境變數選項請參考 [Drone 官方說明](http://docs.drone.io/install-for-gitlab/)。


Drone 啟動完成後，進入 `http://YOUR_DRONE_HOST` 會要求 GitLab 取得授權。

![](/2018/06/18/setting-up-drone-for-gitlab-integration/authorize_drone.png)

取得授權後，接著會導向 `http://YOUR_DRONE_HOST/account/repos` 頁面，就可以選擇你要在 GitLab 下要整合 Drone 的專案。

# Test Your Project

以一個簡單的 Node.js 專案為例，我們在目錄下加入 `.drone.yml`：

```
pipeline:
  install:
    image: node:8
    commands:
      - npm install
  test:
    image: node:8
    commands:
      - npm test
```

這裡定義了兩個 pipeline，首先安裝依賴，然後執行測試。然後將專案 push 至 GitLab 即觸發 Drone 程序。關於 `.drone.yml` 設置的詳細說明請參考 [Drone 官方文件](http://docs.drone.io/pipelines/)。

![](/2018/06/18/setting-up-drone-for-gitlab-integration/drone_example.png)

OK，完成！

# References

[GitLab](https://about.gitlab.com/)
[Drone](https://github.com/drone/drone)
