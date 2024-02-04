# Deploying a Hugo Blog on GitHub


Hugo 踩坑紀錄
&lt;!--more--&gt;

{{&lt; admonition type=info title=&#34;提示&#34;  &gt;}}
本篇環境使用 [Visual Studio Code Dev Containers](https://code.visualstudio.com/docs/remote/containers#_quick-start-open-a-git-repository-or-github-pr-in-an-isolated-container-volume) extensioin
在 docker container 內完成 Blog 製作
{{&lt; /admonition &gt;}}

## Environment Settings

- reference : [https://github.com/devcontainers/features/tree/main/src/hugo](https://github.com/devcontainers/features/tree/main/src/hugo&gt;)
- path : .devcontainer/devcontainer.json

```json
 {
  &#34;name&#34;: &#34;Go&#34;,
  &#34;image&#34;: &#34;mcr.microsoft.com/devcontainers/go:1-1.21-bullseye&#34;,
  &#34;features&#34;: {
    &#34;ghcr.io/devcontainers/features/hugo:1&#34;: {
    &#34;extended&#34;: true, // 用於支援將 SASS/SCSS 轉成原生 CSS
    &#34;version&#34;: &#34;latest&#34;
    }
  }
 }
```

## Deploy workflow

1. create a new repository from Github
2. vscode 上方工具列 View =&gt; Command Platte
3. 搜尋 **Dev Containers: Clone Repository in Container Volume…**
4. search the repository name that created from step1
5. 設置 Dev Container Configuration file
    - Image ：Go devcontainers
    - Features : Hugo devcontainers
    - options : extend
6. 開始用 terminal 建置 hugo 環境

    ```bash
    # 查看 hugo 使否已建置好
    hugo version

    # 新增 hugo site 於根目錄 .
    hugo new site . --force

    # 新增主題
    git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke

    # 設置 site 主題參數至 config.toml 檔案
    echo theme = \&#34;ananke\&#34; &gt;&gt; config.toml

    # 新增文章 (draft:false)
    hugo new posts/my-first-post.md

    # 啟動本地端 server
    hugo server -D
    hugo server --disableFastRender

    # 建置 public 靜態網頁
    hugo -D
    ```

7. 在 config.toml 的 baseURL 設置為之後 github.io 網址 eg: **baseURL = &#34;&lt;https://nofear195.github.io/my-hugo-blog/&gt;&#34;**
8. 新增 .github/workflows/gh-pages.yml 檔案(需要先手動新增 .github , workflows 資料夾)
9. 在 gh-pages.ymal 內新增以下內容

    ``` yml
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
              hugo-version: &#39;latest&#39;
              extended: true

          - name: Build
            run: hugo --minify

          - name: Deploy
            uses: peaceiris/actions-gh-pages@v3
            if: github.ref == &#39;refs/heads/main&#39;
            with:
              github_token: ${{ secrets.GITHUB_TOKEN }}
              publish_dir: ./public
    ```

10. 將以上完成的內容 push 至 github 上
11. 在該 repositoy 頁面的工具欄點選 settings =&gt; pages
12. Source =&gt; Deploy form a branch
13. Branch =&gt; gh-pages =&gt; /root

## Setup for FixIt theme

- 需依照 FixIt Theme 文件教學去設置 config.toml 檔案才能正常運行
- 運行指令 `hugo serve --disableFastRender` 啟動本地端 hugo server

### 方法一

- 從 FixIt Theme 文件教學做設置 ＝&gt; [FixIt basic setting](https://fixit.lruihao.cn/documentation/basics/)

### 方法二

  1. 跳過正常建置程序 `hugo new site . --force`
  2. 直接建立 themes 資料夾
  3. 新增 submodule `git submodule add https://github.com/hugo-fixit/FixIt.git themes/FixIt`
  4. 複製 themes/FixIt 裡面的 exampleSite 至 根目錄 .
  5. 運行指令 `hugo serve --disableFastRender` 啟動本地端 hugo server

{{&lt; admonition type=info title=&#34;提示&#34;  &gt;}}
  方法二的步驟不限於 FixIt 主題，任何主題內含有 exampleSite 字眼的文件都可用這方法直接跑範例，  &lt;br&gt;
  自己再依需求從從範例修改
{{&lt;/ admonition&gt;}}

### 細節紀錄

- Twikoo comment 留言板設置教學 : [Twikoo 文档](https://twikoo.js.org/quick-start.html) 用 **Vercel 部署方式**

## Others

### 刪除 .git config 內記錄有關 summodule 的資訊

- 可以先用 `cat .git/config` 查看 git config 資訊
- 使用時機 手動刪除 themes 內的 submodules 後又想重新下載時，因為 git.config 紀錄，出現下載失敗資訊
- stack overflow 討論串連結 [Deleting all Git cached submodules from repository](https://stackoverflow.com/questions/34890313/deleting-all-git-cached-submodules-from-repository)
- 下面附上最佳解答，我實測有效的 code

    ``` bash
    # deinit all submodules from .gitmodules
    git submodule deinit .

    # remove all submodules (`git rm`) from .gitmodules
    git submodule | cut -c43- | while read -r line; do (git rm &#34;$line&#34;); done

    # delete all submodule sections from .git/config (`git config --local --remove-section`)
    # by fetching those from .git/config
    git config --local -l | grep submodule | sed -e &#39;s/^\(submodule\.[^.]*\)\(.*\)/\1/g&#39; \
     | while read -r line; do (git config --local --remove-section &#34;$line&#34;); done

    # manually remove leftovers
    rm .gitmodules
    rm -rf .git/modules

    # create a new empty .gitmodules
    touch .gitmodules
    ```

- 解法二
  - [git submodule add时提示“projectfolder already exists in the index” #208](https://github.com/yaoningvital/blog/issues/208)

  ``` bash

  git rm --cached themes/FixIt

  git ls-files --stage themes/FixIt

  git submodule add https://github.com/hugo-fixit/FixIt.git themes/FixIt

  ```

### 後記

- 總算把 github page 建起來，並套上 hugo 框架，真的好不容易啊，只能說好多坑，所以先把還記得都列上去了，之後有想到再新增吧 XD


---

> Author: nofear195  
> URL: https://nofear195.github.io/my-hugo-blog/posts/hugo-setting/  

