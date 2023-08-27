---
title: "Vite 打包 Vue3 / Nuxt3 專案並部署在 GitHub Pages 上"
date: 2023-03-07
lastmod: 2023-03-07
draft: false
authors: ["nofear195"]
description: ""
categories: ["learning-note"]
tags: ["Vue","Deploy","軟體研發"]
lightgallery: true

---

Building Vue3/Nuxt3 Project with Vite and Deploying it as a Static Site on GitHub Pages.
<!--more-->

## Reference

1. [Vite - Deploying a Static Site](https://vitejs.dev/guide/static-deploy.html)
2. [Nuxt3- Deployment](https://nuxt.com/docs/getting-started/deployment)

## Steps

### 1. 設置部署來源

1. 在專案主頁面中，找到並點擊 Settings 齒輪按鈕選項
2. 在左方導覽中的 Code and automation 列表底下中，找到並點擊 Pages 選項
![vite-static-deploy-1](images/vite-static-deploy-1.png)
3. 在 "Build and deployment" 的 Source 下拉式選單中，選擇 “GitHub Actions”
![vite-static-deploy-2](images/vite-static-deploy-2.png)

### 2. 設置 Pages 根路徑

#### For Vue3

在 `vite.config.js` 中新增 `base` 參數

- 若部署於 `https://<USERNAME>.github.io/` 上，則可以省略不加
- 若部署於 `https://<USERNAME>.github.io/<YOUR-REPO-NAME>/`上，則需新增 `base` 參數

```javascript
export default defineConfig({
  // ...
  base:'/your-repository-name/', // base default 值為 '/'
})

```

#### For Nuxt3

在 `nuxt.config.ts` 中新增 `baseURL` 參數

- 若部署於 `https://<USERNAME>.github.io/` 上，則可以省略不加
- 若部署於 `https://<USERNAME>.github.io/<YOUR-REPO-NAME>/`上，則需新增 `baseURL` 參數

```javascript
export default defineConfig({
  // ...
  app:{
    baseURL:'/your-repository-name/', // baseURL default 值為 '/'
  },
})

```

### 3. 設置打包及部署腳本

1. 在專案底下建立資料夾 `./.github/workflows`, 並新增 yml 檔案

```bash
mkdir .github
cd .github
mkdir workflows
cd workflows
touch main.yml
```

2. 在 yml 檔案中新增以下腳本

- 之後所有合併至主線的 commit，都會觸發此打包及部署腳本

#### For Vue3

```yml
# Simple workflow for deploying static content to GitHub Pages
name: Deploy static content to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: ['main']

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets the GITHUB_TOKEN permissions to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow one concurrent deployment
concurrency:
  group: 'pages'
  cancel-in-progress: true

jobs:
  # Single deploy job since we're just deploying
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Set up Node
        uses: actions/setup-node@v3
        with:
          node-version: 18
          cache: 'npm'
      - name: Install dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Setup Pages
        uses: actions/configure-pages@v3
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v1
        with:
          # Upload dist repository
          path: './dist'
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v1
```

#### For Nuxt3

- 唯一與 Vue3 差異在於 Nuxt3 專案打包的語法是 `npm run generate`

```yml
# Simple workflow for deploying static content to GitHub Pages
name: Deploy static content to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: ['main']

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:      

# Sets the GITHUB_TOKEN permissions to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow one concurrent deployment
concurrency:
  group: 'pages'
  cancel-in-progress: true

jobs:
  # Single deploy job since we're just deploying
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Set up Node
        uses: actions/setup-node@v3
        with:
          node-version: 18
          cache: 'npm'
      - name: Install dependencies
        run: npm install
      - name: Generate
        run: npm run generate
      - name: Setup Pages
        uses: actions/configure-pages@v3
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v1
        with:
          # Upload dist repository
          path: './dist'
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v1
```

### 4. 合併所有的改變至 GitHub 主線上

- 可在專案主頁面中，找到並點擊 Actions 選項查看 yml 部署狀況

![vite-static-deploy-3](images/vite-static-deploy-3.png)
![vite-static-deploy-4](images/vite-static-deploy-4.png)
