<h1 align="center">personal blog with Hexo, theme Fan</h1>
<p align="center">记录工作、生活中的点点滴滴。</p>
<p align="center"><a href="https://thefirstsunday.github.io/"><b>Preview</b></a></p>

## gitmoji

[https://gitmoji.carloscuesta.me/](https://gitmoji.carloscuesta.me/)
  
在提交内容的前面增加了emoji标签：  **:emoji:**，其中emoji是表情图标的标签，列表见下面的附录表格。


|emoji|	emoji代码	|commit 说明
|----|----|----|
|:art: (调色板)|	```:art:```	|改进代码结构/代码格式
|:zap: (闪电):racehorse: (赛马)|	```:zap:“:racehorse:```	|提升性能
|:fire: (火焰)|	```:fire:```|	移除代码或文件
|:bug: (bug)|	```:bug:```	|修复 bug
|:ambulance: (急救车)|	```:ambulance:```|	重要补丁
|:sparkles: (火花)|	```:sparkles:```	|引入新功能
|:memo: (备忘录)	|```:memo:```	|撰写文档
|:rocket: (火箭)	|```:rocket:```	|部署功能
|:lipstick: (口红)	|```:lipstick:```	|更新 UI 和样式文件
|:tada: (庆祝)	|```:tada:```	|初次提交
|:white_check_mark: (白色复选框)	|```:white_check_mark:```	|增加测试
|:lock: (锁)	|```:lock:```	|修复安全问题
|:apple: (苹果)	|```:apple:```|	修复 macOS 下的问题
|:penguin: (企鹅)|	```:penguin:```|	修复 Linux 下的问题
|:checkered_flag: (旗帜)|	```:checked_flag:```	|修复 Windows 下的问题
|:bookmark: (书签)|	```:bookmark:```	|发行/版本标签
|:rotating_light: (警车灯)|	```:rotating_light:```	|移除 linter 警告
|:construction: (施工)|	```:construction:```	|工作进行中
|:green_heart: (绿心)	|```:green_heart:```	|修复 CI 构建问题
|:arrow_down: (下降箭头)	|```:arrow_down:```	|降级依赖
|:arrow_up: (上升箭头)|	```:arrow_up:```	|升级依赖
|:construction_worker: (工人)|	```:construction_worker:```	|添加 CI 构建系统
|:chart_with_upwards_trend: (上升趋势图)|	```:chart_with_upwards_trend:```	|添加分析或跟踪代码
|:hammer: (锤子)|	```:hammer:```	|重大重构
|:heavy_minus_sign: (减号)|	```:heavy_minus_sign:```|	减少一个依赖
|:whale: (鲸鱼)	|```:whale:```	|Docker 相关工作
|:heavy_plus_sign: (加号)|	```:heavy_plug_sign:```	|增加一个依赖
|:wrench: (扳手)	|```:wrench:```	|修改配置文件
|:globe_with_meridians: (地球)|	```:globe_with_meridians:```|	国际化与本地化
|:pencil2: (铅笔)|	```:pencil2:```|	修复 typo


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
