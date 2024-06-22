---
title: "WebSocket : 簡易 WebSocket Broadcast Server 實踐"
date: 2024-06-22
lastmod: 2024-06-23
draft: false
authors: ["nofear195"]
description: ""
categories: ["learning-note"]
tags: ["WebSocket", "Broadcast server"]
lightgallery: true

---

WebSocket: Simple WebSocket Broadcast Server Implementation
<!--more-->

## WebSocket Introduction

- WebSocket 是一種即時的通訊協定，為 Client（客戶端）和 Server (服務器) 之間建立一個雙向的資料傳輸的通道。
- 運作方式 :
  1. 建立連結：由 Client 發起特殊 HTTP 請求來發起 WebSocket 連接，Server 接收請求後建立 WebSocket 連結
  2. 資料交換階段：Client 和 Server 相互發送資料
  3. 結束連結：任一方都可以隨時發送一個關閉消息來結束連接
- 為 Full-Duplex (全雙工)的通訊模式，即 Client 與 Server 可以同時發送和接收資訊，互不干擾。
- 應用場景：即時聊天室、協作平台、遊戲互動

## WebSocket Broadcast Server

- 角色
  - Broadcast Server : 可比喻為燈塔，是訊息傳送的媒介，負責接收訊息與廣播訊息
  - Client : 傳送訊息給 Server，由 Server 廣播至其他已連接的 Clients
  - 基本設置：一個 MQTT Broker，多個 Client 端

- 特色
  - 輸入 port number (通訊埠號)，即可建立一個新的 WebSocket Server
  - 預設資料傳輸格式 `{role: 'user', message: 'Hello!!!'}`
    - role: Client 註冊的名稱、角色 (ex: user001, user002 ...等)
    - message:  傳輸內容
  - Client role 唯一，確保同時間只有一個唯一的 role 發出訊息

## Preparation

- 確保在 Node.js 環境
- 使用 `npm install` 安裝所有相依套件
- 檔案結構：

  ```bash
  ├── BroadcastServer.js # Server Class
  ├── index.js # Server 使用的範例
  ├── client.js # 客戶端範例（Node.js）
  └── client.html # 客戶端範例（HTML）
  ```

## Operation and Testing

- 啟動 Server (擇一)
  - `npm run start`
  - `node index.js`

  ```javascript
  const server = new BroadcastServer(5000); // port 5000
  ```

- 啟動 Client (擇一)
  - `node client.js`
  - 直接打開 client.html 檔案

  ```javascript
  const WebSocket = require("ws");
  const ws = new WebSocket("ws://localhost:5000");
  const role = "nodejs-client";

  ws.on("open", function open(){...})
  ws.on("message", function incoming(){...})
  ws.on("error", function error(){...})
  ws.on("close", function close(){...})

  function sendMessage(message) {
    const data = { role, message };
    ws.send(JSON.stringify(data));
  }
  ```

## Reference

- [實作範例: https://github.com/nofear195/websocket-broadcast-server](https://github.com/nofear195/websocket-broadcast-server)
