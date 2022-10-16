---
title: "Hugo setting"
subtitle: ""
date: 2022-10-16
draft: false
authors: ["nofear195"]
description: ""
featuredImage: "hugo_logo.webp"

tags: ["hugo"]
categories: ["environment-build"]
series: ["blog-setting"]

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

# 新增 hugo site
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
[DoIt basic setting](https://hugodoit.pages.dev/theme-documentation-basics/)

## Tips