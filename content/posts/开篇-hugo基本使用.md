---
title: "开篇-Hugo基本使用"
date: 2022-02-19T17:53:14+08:00
draft: false
---

>网上讲hugo怎么用的着实很多了，所以我只记录自己第一次折腾hugo的步骤。

## 0x0. 准备工作

1. 在github上建一个这样的仓库：<usrname>.github.io
2. 把仓库clone到本地
3. cd到仓库里

## 0x1. hugo部分

1. 创建hugo基本文件
    ```
    hugo new site . --force
    ```

2. 换个主题
    1. 本博客用的是paper，[在这下载](https://github.com/nanxiaobei/hugo-paper)，看名字还是位国人哦，怪不得觉得这个主题比老外的要顺眼
    2. 直接下zip就行，然后解压出来，放到theme里的paper文件夹
    3. 修改根目录下的config.toml文件，在最后加上如下的部分：
        ```
        theme = 'paper'
        ```

3. 随便创建一篇草稿，再随便写点东西（比如本篇）
    ```
    hugo new posts/开篇-hugo基本使用.md
    ```

4. 预览看看
    ```
    hugo server -D
    ```
    在浏览器里打开提示的网址，我这儿是 http://localhost:1313 应该就能看到了。
    此时可以随便改你的站点，保存后浏览器会自动刷新，很方便。

5. 生成
    ```
    hugo -D
    ```
    然后你就能在 public 文件夹里看到生成的页面了。

    按官方文档走的话，会注意到 md 开头有个 `traft: true`，这表示本篇文章是篇草稿，按hugo的逻辑，草稿是不会被部署上去的。

    这个我试了下，如果没把traft改成false的话，最后部署到github上时是不会显示的。

## 0x2. github部署

接着折腾，把这个博客搞到github托管上去。

1. 在根目录新建 .github/workflows/gh-pages.yml 文件，然后把里面填上（就是hugo官网上的）：
    ```
    name: github pages

    on:
    push:
        branches:
        - main  # Set a branch to deploy
    pull_request:

    jobs:
    deploy:
        runs-on: ubuntu-20.04
        steps:
        - uses: actions/checkout@v2
            with:
            submodules: true  # Fetch Hugo themes (true OR recursive)
            fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

        - name: Setup Hugo
            uses: peaceiris/actions-hugo@v2
            with:
            hugo-version: 'latest'
            # extended: true

        - name: Build
            run: hugo --minify

        - name: Deploy
            uses: peaceiris/actions-gh-pages@v3
            if: github.ref == 'refs/heads/main'
            with:
            github_token: ${{ secrets.GITHUB_TOKEN }}
            publish_dir: ./public
    ```

2. 上传
    把所有的文件上传到github后，理论上应该就可以看到了。

    懒得截图了（其实是还没看hugo怎么管理图片...）