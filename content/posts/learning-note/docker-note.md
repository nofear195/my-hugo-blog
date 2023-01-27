---
title: "Docker note"
date: 2023-01-26
lastmod: 2023-01-27
draft: false
authors: ["nofear195"]
description: ""

categories: ["learning-note"]
series: [""]

lightgallery: true

---

My dockerhub link --> https://hub.docker.com/u/nofear195 :smile: :smile: :smile:
<!--more-->

## Reference

1. [用 Docker 了嗎？ 30 天的 Docker 基本教學 系列](https://ithelp.ithome.com.tw/users/20140124/ironman/5149)
2. [30 天與鯨魚先生做好朋友 系列](https://ithelp.ithome.com.tw/users/20102562/ironman/3746)

## Basic Docker Command

### Command Interface

- Management Command : 可被操作的物件
- Command : 操作物件的動作

    ```bash
    $ docker [Management Command] [Command]

    $ docker container run 
    # management command : container
    # command : run
    ```

- docker command 共用指令

    ```bash
    OPTIONS
    ##########
    --help # 列出所有可用指令
    
    COMMAND
    ##########
    inspect # 查閱內部設置
    prune # 修剪，刪除沒用到的可操作物件(management command)
    
    $ docker system df # 列出 docker 使用狀態
    ```

### Container (容器)

- 啟動容器(初始化)：container run
  - docker run = docker pull + docker create + docker start

  ```bash
  # Run a command in a new container
  $ docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]
  
  OPTIONS
  ##########
  --detach, -d  # 背景執行
  --name # 為容器命名
  --platform # 強制使用與本地作業系統不一樣的 image
  --interactive # 保持輸入的模式，即保持可以跟容器互動的狀態
  -tty -t # 分配一個 terminal 視窗
  --publish -p # 將本機端的 port 連接至 容器初始對外開放的 port
  --network # 設置容器連接的虛擬網路名稱，若不設置 default 為 bridge
  --rm # 當離開容器時(exit)，自動刪除容器
  --env -e # 設置環境變數
  
  IMAGE [COMMAND] ## [COMMAND] 若無輸入則使用預設命令，若有則用來取代預設
  
  ### 啟動 nignix 並開啟一個 terminal 視窗供使用者互動，在啟動時執行 bash 值令
  $ docker container run --interactive --tty nginx bash
  $ docker container run -it nginx bash ## 可以把縮寫 command 寫在一起 
  
  ### 啟動 nignix 並用本機端的 8080 port 連接至 nginx 對外開放的 80 port
  $ docker container run -d --name nginx -p 8080:80 nginx
  ```

- 暫停容器：container stop

    ```bash
    # Stop one or more running containers
    $ docker container stop [OPTIONS] CONTAINER [CONTAINER...]
    ```

- 啟動已存在的容器 (被暫停的) ：container start

    ```bash
    # Start one or more stopped containers
    $ docker container start [OPTIONS] CONTAINER [CONTAINER...]
    ```

- 進入執行中的容器 ： container exec

    ```bash
    # Run a command in a running container
    $ docker container exec [OPTIONS] CONTAINER COMMAND [ARG...]
    
    ### 
    $ docker container exec -it nginx bash
    ```

- 列出容器 (活躍中)： container list
  - 容器的退出機制 ： 只有終止了初始指令所執行的程序，才會使容器進入退出狀態

  ```bash
  # List containers
  $ docker container ls [OPTIONS]
  
  aliases
  ##########
  ls, ps ,list
  
  OPTIONS
  ##########
  --all, -a # 列出所有包含被暫停的容器
  --quiet, -q # 只顯示被執行命令的容器 id, 並隱藏其他資訊
  
  ### 相同作用指令
  $ docker ps 
  ```

- 刪除容器 ：container rm

  ```bash
  # Remove one or more containers
  $ docker container rm [OPTIONS] CONTAINER [CONTAINER...]
  
  OPTIONS
  ##########
  --force -f # 強制刪除包含正在執行的容器
  
  ### 刪除容器正常做法
  # 先暫停容器
  $ docker container stop CONTAINER
  # 再刪除容器
  $ docker container rm CONTAINER
  
  ### 強制刪除所有容器
  $ docker container rm --force $(docker container ls -aq)
  
  ### 相同作用指令
  docker rm CONTAINER [CONTAINER...]
  ```

- 容器事件紀錄 (Log) : container logs

  ```bash
  # Fetch the logs of a container
  $ docker container logs [OPTIONS] CONTAINER
  
  OPTIONS
  ##########
  --follow -f # 持續列出發生的紀錄事件
  ```

- 容器連接對應到的 port 號 : container port
  - docker 中 “127.0.0.1” 代表 container 本身
    - 一般非 docker 開發環境會使用 “127.0.0.1” 作為 localhost，但對 docker container 來說就是連線到自己，無法對外做連接，要避免使用
  - “0.0.0.0” 為所有的網路接口，代表接收所有的外部連接

  ```bash
  # List port mappings or a specific mapping for the container
  $ docker container port CONTAINER [PRIVATE_PORT[/PROTO]]
  
  ### nginx container 對應到的 port 號
  $ docker container run -d --name nginx -p 8080:80 nginx
  $ docker container port nginx
  ## result : 80/tcp -> 0.0.0.0:8080
  ## container : 80/tcp
  ## localhost : 0.0.0.0:8080
  ```

### Network (虛擬網路)

- 使用 NAT ( 網路位址轉換 ) 技術，將內部各式 ip 位址對應到外部共用 ip
  - NAT 廣泛使用於 公司內部 or 私人內部網路
  - 想像 docker 為 ip 的分配者，在 docker 內部的 container 都分配到一個內部 ip，並處理對外的網路連接
- 預設虛擬網路 ：bridge
  - 負責橋接所有內部 container 對外的連結
  - eg : “docker container run —publish” -> 使用 bridge 連結內外的 port
- 列出虛擬網路： network list

  ```bash
  # List networks
  docker network ls [OPTIONS]
  
  aliases
  ##########
  ls,list
  ```

- 建立虛擬網路： network create

  ```bash
  # Create a network
  $ docker network create [OPTIONS] NETWORK
  
  ### 新增名稱為 app 的虛擬網路
  $ docker network create app
  ```

- 將容器添加至指定的虛擬網路 : network connect
  - default 連接至 bridge
  - 當初始化容器時 ： docker container run --network NETWORK
  - 當容器已經在運作時：docker network connect NETWORK CONTAINER

  ```bash
  # Connect a container to a network
  docker network connect [OPTIONS] NETWORK CONTAINER
  ```

- 解除容器連線到的虛擬網路 : network disconnect

    ```bash
    # Disconnect a container from a network
    docker network disconnect [OPTIONS] NETWORK CONTAINER
    ```

### Image (映像檔)

- 由 Dockerfile 建造而成，為 docker 執行容器時的說明書以及套件工具的一個檔案
- 列出 docker 上現有的 image : image list

  ```bash
  # List images
  $ docker image list
  
  aliases
  ##########
  ls,list
  ```

- 列出 image 內部堆疊的過程  : image history

  ```bash
  # Show the history of an image
  $ docker image history [OPTIONS] IMAGE
  ```

- 為 image 新增一個標籤 tag : image tag

  ```bash
  # Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
  docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
  ```

- 從 registry (ex:dockerHub)上抓 image 下來： image pull

  ```bash
  # Pull an image or a repository from a registry
  $ docker image pull [OPTIONS] NAME[:TAG|@DIGEST]
  
  :TAG 標籤，default 預設為 latest
  $ docker image pull redis # 預設 pull latest version
  $ docker image pull redis:7.0 # 指定 7.0 version
  $ docker image pull redis:7-alpine # alpine 簡化版，只提供基本服務，檔案可壓縮到最小
  ```

- 將 image 推上 registry (ex:dockerHub)： image push

  ```bash
  # Push an image or a repository to a registry
  $ docker image push [OPTIONS] NAME[:TAG]

  # NAME[:TAG] 名稱前方需要加自己的 dockerId,所以要先把自己建的 image 加上 tag
  $ docker image tag nginx:latest nofear195/nginx:latest
  $ docker image push nofear195/nginx:latest
  ```

- 移除 docker 上的 image ： image rm

  ```bash
  # Remove one or more images
  docker image rm [OPTIONS] IMAGE [IMAGE...]

  aliases
  ##########
  rm, rmi, remove
  
  ### 相同的指令
  docker rmi [OPTIONS] IMAGE [IMAGE...]
  ```

- 建立 docker image : image build

  ```bash
  # Build an image from a Dockerfile
  $ docker image build [OPTIONS] PATH | URL | -
  
  PATH # Dockerfile 所在的路徑，通常會放在根目錄
  
  OPTIONS
  ##########
  --tag -t # 為建立的 image 給予標籤名稱
  --file -f # Dockerfile 的名稱 (Default is 'PATH/Dockerfile')
  
  ```

### Volume

- 容器外的儲存空間，在 docker 中，container 與 volume 是兩個獨立的模組物件
- 可以在 Dockerfile 中使用 “VOLUME” + path 做設置
- 通常 Volume 需手動刪除，不隨著 container 被刪除而刪除
- 列出存在的 volume

  ```bash
  # List volumes
  $ docker volume ls [OPTIONS]
  
  aliases
  ##########
  ls,list
  ```

- 將初始化時容器綁定的 volume，重新命名 “—volume”

  ```bash
  $ docker container run --detach --name mysql --env MYSQL_ROOT_PASSWORD=whatever --volume mysql-data:/var/lib/mysql mysql
  
  --volume [自定義名稱]：[Dockerfile 綁定的 volume 路徑]
  # --volume mysql-data:/var/lib/mysql
  ```

## Dockerfile

### Command

- FROM
  - 引用要使用的 image
- ENV
  - 設置環境變數，以 key = value 方式設定
- RUN
  - 執行當前作業系統的 terminal 指令
    - 可用來安裝套件管理工具等
    - in Linux / Ubuntu : apt-get  install…
    - in alpine : apk add …
    - in macOS : brew install …
    - 指令中的特殊符號
      - 符號使用目的 ：把指令濃縮至同一個 layer 中
      - “ \ ” ( 反斜線) ：換行
      - “＆＆” ：若 && 前面的指令沒有出錯，就執行 && 後面的指令
- WORKDIR
  - 建立一個工作目錄，“且” 使建立的目錄為預設的工作目錄，之後的指令都會在該工作目錄裡執行
    - WORKDIR /app 與 RUN mkdir app 差異
      - RUN mkdir app : 只建立 dir app
      - WORKDIR /app 內含的執行細節
                1. 建立 app 目錄，即 mkdir app
                2. 進入 app 目錄，即 cd app
                3. 在 app 目錄內執行接下來的 Dockerfile 指令
- COPY
  - 複製當前的檔案，到容器內的指定位置
    - 常用指令 :  “ COPY . . “
      - 將 Dockerfile 所在的檔案/資料夾，複製到容器內的 “預設的工作目錄”
- EXPOSE
  - 預設容器開始運行後對外部開啟的 port
- CMD
  - 容器開始運行後(docker container run …)要執行的指令，ex: node …

### Optimization Order

- 原因： docker 重新建立機制，若是上層的 image 重新建置，則其以下的所有的 layer 都將重新建置
- 原則：變動頻率越低，放越上面去優先執行
- 推薦順序
  1. FROM
  2. ENV
  3. EXPOSE
  4. CMD
  5. WORKDIR /app
  6. RUN [terminal command]
  7. COPY
  8. RUN [node …]
- 範例

  ```Dockerfile
  FROM python:3-alpine3.17
  ENV FLASK_APP=app.py
  ENV FLASK_RUN_HOST=0.0.0.0
  EXPOSE 5000
  CMD ["flask", "run"]
  WORKDIR /app
  RUN apk add --no-cache gcc musl-dev linux-headers
  COPY . .
  RUN pip install -r requirements.txt

  ```

## .dockerignore

- 目的：將 image build 沒有用到檔案做忽略
- 範例

  ```.dockerignore
  **/.git
  Dockerfile*
  docker-compose*
  .dockerignore
  .git
  .gitignore
  .vscode
  README.md
  ```

## docker compose

- 一個工具用來定義且執行多個容器的 docker 服務
- 主使用 YAML 檔案 (docker-compose.yml) 去定義容器的服務細節
- 執行 yaml 檔案內定義的單一服務 compose run

  ```bash
  # Run a one-off command on a service.
  $ docker compose run [OPTIONS] SERVICE [COMMAND] [ARGS...]
  ```

- 建立並啟動yaml 檔案內定義的容器(服務) compose up

  ```bash
  # Create and start containers
  $ docker compose up [OPTIONS] [SERVICE...]

  [SERVICE...]: 若沒有帶這個參數，則執行 compose 內所有的容器(服務)
  ```

- 停止和移除容器和虛擬網路 compose down

  ```bash
  # Stop and remove containers, networks
  $ docker compose down [OPTIONS]

  OPTIONS
  ##########
  --volumes, -v, volumes # 移除定義在 compose file 連結的 volume
  ```

- 範例

  ```yml
  services:
  web:
    container_name: 'upload-zip-file'
    build: .
    ports:
      - "8000:5000"
  ```
