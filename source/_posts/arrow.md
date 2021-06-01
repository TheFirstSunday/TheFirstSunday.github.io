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

- 实现方式一
``` CSS3
    <!-- 实现方式一 -->
    width: 8px;
    height: 8px;
    border-bottom: 2px solid rgba(255, 255, 255, 0.7);
    border-right: 2px solid rgba(255, 255, 255, 0.7);
    transform: matrix(0.71, -0.7, 0.71, 0.71, 0, 0);
    
  
```

- 实现方式二
``` CSS3

    <!-- 实现方式二 -->
    width: 8px;
    height: 8px;
    border-bottom: 2px solid rgba(255, 255, 255, 0.7);
    border-right: 2px solid rgba(255, 255, 255, 0.7);
    transform: rotate(45deg);
```

- 实现方式三(SVG)

```HTML5

<svg width="750" height="500">
  <polyline points="
    3 3
    30 28
    3 53
  "></polyline>
</svg>

 svg {
      stroke: #000;
      stroke-width: 2;
      stroke-linecap: round;
      stroke-linejoin: round;
      fill: none;
    }
```

- 实现方式四(Canvas)

``` JavaScript

<canvas id="canvas" width="500" height="400">您的浏览器不支持canvas</canvas>
<script>
    const canvas = document.getElementById("canvas");
    const ctx = canvas.getContext("2d");
    ctx.moveTo(0, 0); // 开始坐标在 (0, 0)
    ctx.lineTo(10, 10); // 线条移动到 (10, 10)
    ctx.lineTo(0, 20); // 线条移动到 (0, 20)
    ctx.strokeStyle = "grey"; // 线条颜色设置为灰色
    ctx.stroke(); // 绘制
</script>
```
