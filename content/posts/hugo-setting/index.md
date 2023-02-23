---
title: "Hugo setting"
subtitle: ""
date: 2022-10-16
lastmod: 2023-02-15
draft: false
authors: ["nofear195"]
description: ""
featuredImage: "hugo_logo.webp"
categories: ["environment-build"]
tags: ["hugo"]
lightgallery: true

---

Hugo 踩坑紀錄
<!--more-->

{{< admonition type=info title="提示"  >}}
以下建立方式皆以 [Visual Studio Code Dev Containers](https://code.visualstudio.com/docs/remote/containers#_quick-start-open-a-git-repository-or-github-pr-in-an-isolated-container-volume) extensioin
在獨立的 docker container 環境下建置完成
{{< /admonition >}}

## Setup (normal)

### github side 1

1. 從 github 新增 repository
2. 複製 repository HTTPS 連結

### vscode side

1. vscode 上方工具列 View => Command Platte
2. 搜尋 **Dev Containers: Clone Repository in Container Volume…**
3. 貼上在 github 取得的 HTTPS 連結
4. 環境選擇： 手動輸入 Hugo (Community)
5. vscode 上方工具列 Terminal => New Termianl
6. 開始用 terminal 建置 hugo 環境

```bash
# 查看 hugo 使否已建置好
hugo version

# 新增 hugo site 於根目錄 .
hugo new site . --force

# 新增主題
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke

# 設置 site 主題參數至 config.toml 檔案
echo theme = \"ananke\" >> config.toml

# 新增文章 (draft:false)
hugo new posts/my-first-post.md

# 啟動本地端 server
hugo server -D

# 建置 public 靜態網頁
hugo -D
```

7. 在 config.toml 的 baseURL 設置為之後 github.io 網址 eg: **baseURL = "https://nofear195.github.io/my-hugo-blog/"**
8. 新增 .github/workflows/gh-pages.yml 檔案(需要先手動新增 .github , workflows 資料夾)
9. 在 gh-pages.ymal 內新增以下內容

``` inside file
name: github pages

on:
  push:
    branches:
      - main  # Set a branch to deploy
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/checkout@v3
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main'
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

10. 將以上完成的內容 push 至 github 上

### github side 2

1. 在該 repositoy 頁面的工具欄點選 settings => pages
2. Source => Deploy form a branch
3. Branch => gh-pages => /root

## Setup (for DoIt theme)

### 前置環境配置

- 需更改 container 環境的 Hugo 版本 ： hugo -> hugo_extend (主要是能支援將 .scss 轉換成 .css)
  - 需更改的文件有
    1. devcontainer.json

      ```json
      "name": "Hugo (Community)",
      "build": {
        "dockerfile": "Dockerfile",
        "args": {
        // Update VARIANT to pick hugo variant.
        // Example variants: hugo, hugo_extended
        // Rebuild the container if it already exists to update.
        "VARIANT": "hugo_extended",
        // Update VERSION to pick a specific hugo version.
        // Example versions: latest, 0.73.0, 0,71.1
        // Rebuild the container if it already exists to update.
        "VERSION": "latest",
        // Update NODE_VERSION to pick the Node.js version: 12, 14
        "NODE_VERSION": "14"
        }
      },

      ```

    2. Dockerfile

    ```bash
    # Update the NODE_VERSION arg in docker-compose.yml to pick a Node version: 18, 16, 14
    ARG NODE_VERSION=16
    FROM mcr.microsoft.com/vscode/devcontainers/javascript-node:0-${NODE_VERSION}
    # VARIANT can be either 'hugo' for the standard version or 'hugo_extended' for the extended version.
    ARG VARIANT=hugo_extended
    # VERSION can be either 'latest' or a specific version number
    ARG VERSION=latest
    ```

- 更改文件配置後重新 build container 即可套用 hugo_extend 環境
- 需依照 DoIt Theme 文件教學去設置 config.toml 檔案才能正常運行
- 運行指令 `hugo serve --disableFastRender` 啟動本地端 hugo server 並支援 **hotreload** 功能

### 方法一

- 從 DoIt Theme 文件教學做設置 ＝> [DoIt basic setting](https://hugodoit.pages.dev/theme-documentation-basics/)

### 方法二

  1. 跳過正常建置程序 `hugo new site . --force`
  2. 直接建立 themes 資料夾
  3. 新增 submodule `git submodule add https://github.com/HEIGE-PCloud/DoIt.git themes/DoIt`
  4. 複製 themes/DoIt 裡面的 exampleSite 至 根目錄 .
  5. 運行指令 `hugo serve --disableFastRender` 啟動本地端 hugo server

{{< admonition type=info title="提示"  >}}
  方法二的步驟不限於 DoIt 主題，任何主題內含有 exampleSite 字眼的文件都可用這方法直接跑範例，  <br>
  自己再依需求從從範例修改
{{</ admonition>}}

### 細節紀錄

- 文章右側的目錄列表**正常顯示**需要在 config.toml 內 [params.page.toc] 其中一個參數設置 `keepStatic = false`
- Twikoo comment 留言板設置教學 : [Twikoo 文档](https://twikoo.js.org/quick-start.html) 用 **Vercel 部署方式**

## Others

### 刪除 .git config 內記錄有關 summodule 的資訊

- 可以先用 `cat .git/config` 查看 git config 資訊
- 使用時機 手動刪除 themes 內的 submodules 後又想重新下載時，因為 git.config 紀錄，出現下載失敗資訊
- stack overflow 討論串連結 [Deleting all Git cached submodules from repository](https://stackoverflow.com/questions/34890313/deleting-all-git-cached-submodules-from-repository)
- 下面附上最佳解答，我實測有效的 code

```bash
# deinit all submodules from .gitmodules
git submodule deinit .

# remove all submodules (`git rm`) from .gitmodules
git submodule | cut -c43- | while read -r line; do (git rm "$line"); done

# delete all submodule sections from .git/config (`git config --local --remove-section`) by fetching those from .git/config
git config --local -l | grep submodule | sed -e 's/^\(submodule\.[^.]*\)\(.*\)/\1/g' | while read -r line; do (git config --local --remove-section "$line"); done

# manually remove leftovers
rm .gitmodules
rm -rf .git/modules
```

- 解法二
  - [git submodule add时提示“projectfolder already exists in the index” #208](https://github.com/yaoningvital/blog/issues/208)

``` bash

git rm --cached themes/DoIt

git ls-files --stage themes/DoIt

git submodule add https://github.com/HEIGE-PCloud/DoIt.git themes/DoIt

```

### 後記

- 總算把 github page 建起來，並套上 hugo 框架，真的好不容易啊，只能說好多坑，所以先把還記得都列上去了，之後有想到再新增吧 XD