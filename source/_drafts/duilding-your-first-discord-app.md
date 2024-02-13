---
title: Building your first Discord app
date: 2024-01-01 21:30:59
tags:
---

Discord應用程式允許您使用一系列API和互動功能來自訂和擴充您的伺服器。本指南將引導您使用JavaScript建立您的第一個Discord應用程式，到最後您將擁有一個使用斜線命令、發送訊息並回應元件互動的應用程式。

我們將建立一個Discord應用程式，讓伺服器成員可以玩剪刀石頭布（有7個選擇，而非通常的3個）。本指南以初學者為目標，但假設您具備基本的JavaScript理解。

<!-- more -->


## 第一步：建立應用程式

首先，如果您還沒有應用程式，您需要在開發者入口網站上建立一個：

輸入您的應用程式名稱，然後按「建立」。

建立您的應用程式後，您將進入應用程式設定的 **General Overview** 頁面，在此您可以更新有關您的應用程式的基本資訊，如描述和圖示。您還會看到 **Application ID** 和 **Interactions Endpoint URL**，我們稍後在指南中會用到。

### Configuring your bot

接下來，我們將為您的應用程式配置 [bot user](https://discord.com/developers/docs/topics/oauth2#bot-vs-user-accounts)，使其能夠以與其他伺服器成員相似的方式出現和行為。

在左側的側邊欄中，點擊 **Bot**。在此頁面上，您可以配置設定，如其 [privileged intents](https://discord.com/developers/docs/topics/gateway#privileged-intents) 或是否可以由其他使用者安裝。

在 **Bot** 頁面上還有一個 **Token** 區域，允許您複製和重置您的 bot 令牌。

Bot tokens 用於授權 API 請求，並攜帶您的 bot user 權限，因此它們非常敏感。請務必永遠不要分享您的令牌，也不要將其檢入任何版本控制中。

請繼續複製令牌，並將令牌存儲在安全的地方（例如密碼管理器中）。

> 您將無法再查看您的令牌，除非重新生成它，因此請確保將其存放在安全的地方。

### Adding scopes and bot permissions

應用程式需要獲得安裝使用者的批准，才能在 Discord 中執行操作（例如建立 slash command 或檢索伺服器成員列表）。在安裝應用程式之前，讓我們選擇一些範圍（scopes）和權限（permissions）來請求。

點擊左側的側邊欄中的 **OAuth2**，然後選擇 **URL generator**。

> URL generator 根據您為應用程式選擇的範圍和權限創建一個安裝連結。您可以使用此連結將應用程式安裝到您自己的伺服器上，或與他人分享，以便他們安裝它。

現在，只需新增 `bot` **scope**，這會新增您的 bot user。

在選擇 `bot` 後，您還可以為 bot 選擇不同的權限。目前，只能選擇 **Send Messages**。

查看所有 [OAuth2 scopes](https://discord.com/developers/docs/topics/oauth2#shared-resources-oauth2-scopes) 的列表，或在文件中深入瞭解 [permissions](https://discord.com/developers/docs/topics/permissions)。

### Installing your app

一旦您新增了 scopes，您應該會看到一個URL，您可以複製以安裝您的應用程式。

> 在開發應用程式時，您應該在不被其他人主動使用的伺服器上進行構建和測試。如果您還沒有自己的伺服器，您可以免費創建一個。

從上面複製URL，然後將其貼上到您的瀏覽器中。您將被引導完成安裝流程，在這裡，請確保您正在將應用程式安裝到您可以開發和測試的伺服器上。

安裝您的應用程式後，您可以前往您的伺服器，並看到它已經加入了 ✨

有了配置和安裝好的應用程式，讓我們開始開發吧。


## 第二步：運行您的應用程式

在 [GitHub 存儲庫](https://github.com/discord/discord-example-app) 中可以找到示例應用中使用的所有程式碼。

為了讓開發變得更簡單，該應用程式使用了 [discord-interactions](https://github.com/discord/discord-interactions-js)，這提供了型別和輔助函數。如果您更喜歡使用其他語言或庫，請查看 [Community Resources](https://discord.com/developers/docs/topics/community-resources) 文件。

## Remixing the project

本指南使用 Glitch，它允許您在瀏覽器中克隆並進行開發。如果您更喜歡在本地開發應用程式，[README](https://github.com/discord/discord-example-app#running-app-locally) 中有使用 ngrok 的說明。

> 雖然 Glitch 非常適合開發和測試，但由於其技術限制，製作應用程式的生產環境應考慮其他主機提供商。

首先，重製（或克隆）這個 Glitch 項目 🎏。

一旦您重製了專案，您將進入一個新的 Glitch 專案。


專案結構

專案的所有檔案都在您的 Glitch 項目的左側。以下是主要文件夾和文件的概覽：

```
├── examples    -> short, feature-specific sample apps
│   ├── app.js  -> finished app.js code
│   ├── button.js
│   ├── command.js
│   ├── modal.js
│   ├── selectMenu.js
├── .env        -> your credentials and IDs
├── app.js      -> main entrypoint for app
├── commands.js -> slash command payloads + helpers
├── game.js     -> logic specific to RPS
├── utils.js    -> utility functions and enums
├── package.json
├── README.md
└── .gitignore
```

### Adding credentials

您的 `app.js` 檔案中已經有一些程式碼，但是為了發送請求，您需要應用程式的 token 和 ID。您可以直接將所有憑據存儲在 `.env` 檔案中。

首先，從前面複製您的 bot 使用者 token，然後將其粘貼到 `.env` 檔案中的 `DISCORD_TOKEN` 變數中。

接下來，轉到您應用程式的 **General Overview** 頁面，然後複製 **App ID** 和 **Public Key**。將這些值粘貼到 `.env` 檔案中，分別作為 `APP_ID` 和 `PUBLIC_KEY`。

配置完您的憑據後，讓我們安裝並處理斜線命令（slash commands）。

### Installing slash commands

> 為了安裝斜線命令，該應用程式使用了 `node-fetch`。您可以在 `DiscordRequest()` 函數內的 `utils.js` 文件中看到安裝的實現。

該專案包含一個 `register` script，您可以使用它來安裝 `ALL_COMMANDS` 中定義的命令，該命令在 `commands.js` 的底部定義。它透過調用 HTTP API 的 `PUT /applications/<APP_ID>/commands` 端點將命令安裝為全域命令。

如果您想自定義您的命令或新增其他命令，您可以參考 [commands documentation](https://discord.com/developers/docs/interactions/application-commands#application-command-object-application-command-structure) 中的命令結構。

在您的 Glitch 項目底部，點擊 **Terminal**，然後粘貼以下命令運行註冊腳本：

```
npm run register
```

按 Enter 鍵運行該命令。

如果您返回到您的伺服器，您應該會看到斜線命令出現。但是，如果您嘗試運行它們，什麼都不會發生，因為您的應用程式沒有接收或處理來自 Discord 的任何請求。

## 第三步：處理互動

為了使您的應用程式能夠接收斜線命令請求（以及其他互動），Discord 需要一個公共 URL 來發送這些請求。您可以在應用程式設定中配置此 URL 為 **Interaction Endpoint URL**。

### Adding an interaction endpoint URL

Glitch 專案預設具有 public URL。複製您專案的 URL，方法是點擊右上角的「分享」按鈕，然後在對話框底部附近複製 **Live site** 的項目鏈接。

> 如果您是在本地開發，GitHub [README](https://github.com/discord/discord-example-app#running-app-locally) 中有有關將請求導向到本地環境的指示。

複製了連結後，從開發者入口網站進入您應用程式的設定。

在您應用程式的 **General Information** 頁面上，有一個 **Interactive Endpoint URL** 選項，您可以在此處粘貼您應用程式的 URL，並在其後附加 `/interactions`，這是 Express 應用程式設定來監聽請求的地方。

點擊 **Save Changes**，確保您的端點已成功驗證。

示例應用程式以兩種方式處理驗證：

- 使用 `PUBLIC_KEY` 和 [discord-interactions package](https://github.com/discord/discord-interactions-js#usage)，並使用一個包裝函數（從 `utils.js` 中導入），使其符合 Express 的驗證界面。這將在您的應用程式的每個傳入請求上運行。
- 回應傳入的 `PING` 請求。

您可以在 [interactions documentation](https://discord.com/developers/docs/interactions/receiving-and-responding#receiving-an-interaction) 中深入了解準備應用程式接收互動的更多資訊。

### Handling slash command requests

在端點驗證完成後，前往您專案的 `app.js` 檔案，找到處理 `/test` 命令的程式區塊：

```js
// "test" command
if (name === 'test') {
    // Send a message into the channel where command was triggered from
    return res.send({
    type: InteractionResponseType.CHANNEL_MESSAGE_WITH_SOURCE,
    data: {
        // Fetches a random emoji to send from a helper function
        content: 'hello world ' + getRandomEmoji(),
    },
    });
}
```

上面的程式碼正在以一條消息回應互動，該消息會顯示在它發起的頻道中。您可以在 [文檔](https://discord.com/developers/docs/interactions/receiving-and-responding#interaction-response-object-interaction-callback-type) 中查看所有可用的回應類型，例如使用 modal 進行回應。

> `InteractionResponseType.CHANNEL_MESSAGE_WITH_SOURCE` 是由 `discord-interactions` 匯出的一個常數。

前往您的伺服器，確保您的應用程式的 /test 斜線命令正常運作。當您觸發它時，您的應用程式應該會發送一條包含「hello world」後跟一個隨機表情符號的訊息。

在接下來的部分，我們將添加一個額外的命令，使用斜線命令選項、按鈕和選單，來構建剪刀石頭布遊戲。

## 第四步：新增訊息元件

`/challenge` 命令將是我們啟動剪刀石頭布風格遊戲的方式。當該命令被觸發時，應用程式將向頻道發送訊息組件，引導用戶完成遊戲。

### Adding a command with options


`/challenge` 命令，在 `commands.js` 中稱為 `CHALLENGE_COMMAND`，具有一個選項陣列。在我們的應用程式中，這些選項是代表在玩剪刀石頭布時用戶可以選擇的不同事物的對象，使用 `game.js` 中 `RPSChoices` 的鍵生成。

您可以在 [文檔](https://discord.com/developers/docs/interactions/application-commands#application-command-object-application-command-option-structure) 中深入瞭解命令選項及其結構。

