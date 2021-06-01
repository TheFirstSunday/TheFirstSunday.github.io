---
title: 心情烦躁、画个箭头吧
date: 2020-12-28 15:06:36
categories: 'Diary'
tags: CSS3
---

## CSS3实现右侧箭头(烦躁、画个箭头消消火)

``` HTML

    <div class='right-arrow' /> 
```

``` CSS3
    <!-- 实现方式一 -->
    width: 8px;
    height: 8px;
    border-bottom: 2px solid rgba(255, 255, 255, 0.7);
    border-right: 2px solid rgba(255, 255, 255, 0.7);
    transform: matrix(0.71, -0.7, 0.71, 0.71, 0, 0);
    
  
```

``` CSS3

    <!-- 实现方式二 -->
    width: 8px;
    height: 8px;
    border-bottom: 2px solid rgba(255, 255, 255, 0.7);
    border-right: 2px solid rgba(255, 255, 255, 0.7);
    transform: rotate(45deg);
```
