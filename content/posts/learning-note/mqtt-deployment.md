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
  ├── mosquitto/   
  │   ├── config/
  │   │   └── mosquitto.conf # 基本參數設定
  │   │   └── passwd # 帳密
  │   └── log/
  │       └── mosquitto.log
  ├── docker-compose.yml
  └── create_user.sh # 簡易帳密生成執行檔
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
        - ./mosquitto/config/mosquitto.conf:/mosquitto/config/mosquitto.conf
        - ./mosquitto/config/passwd:/mosquitto/config/passwd
        - ./mosquitto/log/:/mosquitto/log/
        - mosquitto-data:/mosquitto/data/
      ports:
        - 1883:1883
        #- 9001:9001

  volumes:
    mosquitto-data:
  ```

- create_user.sh
  - 用來新增帳密的執行檔，新增後要變更檔案模式 `chmod +x create_user.sh`

  ```sh
  #!/bin/bash

  # Function to check if a command exists
  command_exists() {
    command -v "$1" &> /dev/null
  }

  # Check if Docker is installed
  if ! command_exists docker; then
    echo "Docker is not installed. Please install Docker and try again."
    exit 1
  fi

  # Prompt the user for the username and password
  read -p "Enter the username: " username
  read -sp "Enter the password: " password
  echo

  # Pull the Mosquitto image
  docker pull eclipse-mosquitto:2.0.18

  # Create the config directory if it doesn't exist
  mkdir -p ./mosquitto/config

  # Create an empty password file if it doesn't exist
  touch ./mosquitto/config/passwd

  # Run the container to set up the password
  docker run --rm -v "$(pwd)/mosquitto/config/passwd:/mosquitto/config/passwd" eclipse-mosquitto:2.0.18 sh -c "
  chmod 0700 /mosquitto/config/passwd &&
  chown root:root /mosquitto/config/passwd &&
  mosquitto_passwd -b /mosquitto/config/passwd $username $password
  "

  echo "Username and password have been added to the passwd file."
  ```

## Deploying the MQTT Broker

1. `cd ./mqtt-broker` (切換至 docker-compose.yml 所在資料夾)
2. `docker compose up -d` (使用 docker-compose 工具部署 MQTT broker 服務)
3. `./create_user.sh` (新增可連接使用 MQTT Broker 的帳號密碼)

## Operation and Testing

- 測試工具：[MQTT Explorer](https://mqtt-explorer.com/)

- MQTT Client example in JavaScript

  ```javascript
  const mqtt = require("mqtt");

  class MqttClient {
    constructor(name, url, username, password, topics) {
      this.name = name;
      this.url = url;
      this.username = username;
      this.password = password;
      this.topics = topics;
      this.client = null;
    }

    connect(retryCount = 0) {
      return new Promise((resolve, reject) => {
        this.client = mqtt.connect(this.url, {
          username: this.username,
          password: this.password
        });

        this.client.on("connect", () => {
          console.info(`Connected to MQTT broker: ${this.name}`);
          this.topics.forEach((topic) => {
            this.client.subscribe(topic, (err) => {
              if (err) {
                console.error(`Failed to subscribe to topic ${topic}`);
              } else {
                console.info(`Successfully subscribed to topic ${topic}`);
              }
            });
          });
          resolve(this.client);
        });

        this.client.on("error", (error) => {
          console.error(`Error connecting to MQTT broker: ${this.name}`, error);
          this.client.end();
          if (retryCount < 5) { // Max 5 retries
            setTimeout(() => {
              this.connect(retryCount + 1).then(resolve).catch(reject);
            }, 2000 * retryCount); // Exponential backoff
          } else {
            reject(error);
          }
        });

        this.client.on("close", () => {
          console.info(`Disconnected from MQTT broker: ${this.name}`);
          this.handleClose();
        });

        this.client.on("message", (topic, message) => {
          const messageContent = JSON.parse(message.toString());
          this.handleMessage(topic, messageContent);
        });
      });
    }

    async disconnect() {
      if (this.client) {
        await this.client.end(true);
      }
    }

    handleMessage(topic, message) {
      console.log(`Message received on ${topic}:`, message);
    }

    handleClose() {
      console.log(`Connection to MQTT broker ${this.name} closed.`);
      this.client.removeAllListeners();
    }
  }

  module.exports = { MqttClient };
  ```

## Reference

- [Official Image : https://hub.docker.com/_/eclipse-mosquitto](https://hub.docker.com/_/eclipse-mosquitto)
- [Eclipse Mosquitto Homepage: https://mosquitto.org/](https://mosquitto.org/)
