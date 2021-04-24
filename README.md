<h1 align="center">personal blog with Hexo, theme Fan</h1>
<p align="center">记录工作、生活中的点点滴滴。</p>
<p align="center"><a href="https://thefirstsunday.github.io/"><b>Preview</b></a></p>

## Feature

### Top article

支持文章置顶功能，在需要置顶的文章（Markdown 文件）加入 `top: 1`，数值越大越靠前。

如：
```
title: xxx
author: xxx
tags:
  - xxx
categories:
  - xxx
date: xxx
top: 1
```

### Algolia_search
```
    注意，每次更新文章后记得执行 `hexo algolia` 更新索引。
```

```
  ---
  title: personal blog with Hexo, theme Fan
  categories: 'Diary'
  tags: 'Diary'
  ---

  the personal blog with Hexo, theme Fan. Record every bit of learning and life.


  ## Quick Start

  ### Create a new post

  ``` bash
  $ hexo new "My New Post"
  ```

  More info: [Writing](https://hexo.io/docs/writing.html)

  ### Run server

  ``` bash
  $ hexo server
  ```

  More info: [Server](https://hexo.io/docs/server.html)

  ### Generate static files

  ``` bash
  $ hexo generate
  ```

  More info: [Generating](https://hexo.io/docs/generating.html)

  ### Deploy to remote sites

  ``` bash
  $ hexo deploy
  ```

  More info: [Deployment](https://hexo.io/docs/one-command-deployment.html)

```
