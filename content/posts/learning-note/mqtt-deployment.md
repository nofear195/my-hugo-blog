---
title: "MQTT : 簡易服務部署與操作指南"
date: 2024-05-27
lastmod: 2024-05-28
draft: false
authors: ["nofear195"]
description: ""
categories: ["learning-note"]
tags: ["MQTT", "Docker", "Deployment"]
lightgallery: true

---

MQTT: A Simple Guide to Service Deployment and Operation
<!--more-->

## Introduction

- MQTT（Message Queuing Telemetry Transport）是一種輕量級的訊息傳輸協議，專為低頻寬、高延遲或不穩定的網絡環境設計。
- 廣泛應用於物聯網（IoT）設備的通訊。
- 主要角色
  - MQTT Broker : 可比喻為燈塔，是訊息傳送的媒介，負責接收訊息與廣播訊息
  - MQTT Client : 傳送訊息給 MQTT broker 的裝置，也是接收 MQTT broker 廣播訊息的裝置
  - 基本設置：一個 MQTT Broker，多個 Client 端

## Preparation

- 安裝 Docker 環境以及 Docker Compose tool
- 檔案結構：

  ```bash
  mqtt-broker/
  │
  ├── config/
  │     └── mosquitto.conf # 基本參數設定
  │     └── passwd # 帳密
  │ 
  ├── log/
  │ 
  ├── docker-compose.yml
  └── create_user.sh # 單次使用者與密碼生成執行檔
  └── create_user_batch.sh # 批次使用者與密碼生成執行檔
  ```

- mosquitto.conf (基本參數設定)

  ```conf
  # Enable persistent storage
  persistence true
  persistence_location /mosquitto/data/

  # Logging
  log_dest file /mosquitto/log/mosquitto.log

  # Security settings
  allow_anonymous false  # Disable anonymous access
  password_file /mosquitto/config/passwd

  # MQTT listener
  listener 1883
  protocol mqtt

  # Websockets listener
  # listener 9001
  # protocol websockets

  ```

- docker-compose.yml

  ```yml
  services:
    mqtt-broker:
      image: eclipse-mosquitto:2.0.18
      container_name: mqtt-broker
      restart: always
      volumes:
        - ./config/mosquitto.conf:/mosquitto/config/mosquitto.conf
        - ./config/passwd:/mosquitto/config/passwd
        - ./log/:/mosquitto/log/
        - mosquitto-data:/mosquitto/data/
      ports:
        - 1883:1883
        #- 9001:9001

  volumes:
    mosquitto-data:
  ```

- 新增 MQTT 使用者與密碼
  - 指令 `mosquitto_passwd`
  - 使用者與密碼格式：  `username:password` (username 不可包含 ":")
  - 參考連結：[https://mosquitto.org/man/mosquitto_passwd-1.html](https://mosquitto.org/man/mosquitto_passwd-1.html)
  - 自製簡易執行檔
    - `create_user.sh`
      - 單次生成執行檔
      - 指令：`mosquitto_passwd -b /mosquitto/config/passwd $username $password`
    - `create_user_batch.sh`
      - 批次生成執行檔
      - 指令：`mosquitto_passwd -U /mosquitto/config/passwd` (更新文件內所有使用者與密碼組合)
    - 使用執行檔前需變更檔案模式 `chmod +x create_user.sh`

## Deploying the MQTT Broker

1. `cd ./mqtt-broker` (切換至 docker-compose.yml 所在資料夾)
2. `./create_user.sh` or `./create_user_batch.sh` (新增可連接使用 MQTT Broker 的使用者與密碼)
3. `docker compose up -d` (使用 docker-compose 工具部署 MQTT broker 服務)

## Operation and Testing

- 測試工具：[MQTT Explorer](https://mqtt-explorer.com/)

- MQTT Client example in JavaScript

  - publisher.js

  ```javascript
  const mqtt = require('mqtt');

  const client = mqtt.connect('mqtt://mqtt-broker:1883',{
      username:'user',
      password:'user'
  });

  client.on('connect', () => {
    console.log('Publisher connected to MQTT broker');
    setInterval(() => {
      client.publish('test/topic', 'Hello, MQTT!'); // Publish a message to a topic
    }, 1000);
  });

  client.on('close', () => {
    console.log('Publisher disconnected from MQTT broker');
    client.end()
  });
  ```

  - subscriber.js

  ```javascript
  const mqtt = require('mqtt');

  const client = mqtt.connect('mqtt://mqtt-broker:1883',{
      username:'user',
      password:'user'
  });

  client.on('connect', () => {
    console.log('Subscriber connected to MQTT broker');
    client.subscribe('test/topic'); // Subscribe to a topic
  });

  client.on('message', (topic, message) => {
    console.log(`Received message on topic ${topic}: ${message.toString()}`);
  });

  client.on('close', () => {
    console.log('Subscriber disconnected from MQTT broker');
    client.end()
  });
  ```

## Reference

- [實作範例: https://github.com/nofear195/mqtt-broker-nodejs-client](https://github.com/nofear195/mqtt-broker-nodejs-client)
- [Official Image : https://hub.docker.com/_/eclipse-mosquitto](https://hub.docker.com/_/eclipse-mosquitto)
- [Eclipse Mosquitto Homepage: https://mosquitto.org/](https://mosquitto.org/)
