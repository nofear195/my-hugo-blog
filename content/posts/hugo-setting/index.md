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

## Setup

### github side

1. 從 github 新增 repository
2. 複製 repository HTTPS 連結

### vscode side

1. vscode 上方工具列 View => Command Platte
2. 搜尋 **Dev Containers: Clone Repository in Container Volume…**
3. 貼上在 github 取得的 HTTPS 連結
4. vscode 上方工具列 Terminal => New Termianl
5. 開始用 terminal 建置 hugo 環境

```bash
# 查看 hugo 使否已建置好
hugo version
```

```bash
# 新增 hugo site
hugo new site . --force
```

```bash
# 新增主題
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```

```bash
# 設置 site 主題參數至 config.toml 檔案
echo theme = \"ananke\" >> config.toml
```