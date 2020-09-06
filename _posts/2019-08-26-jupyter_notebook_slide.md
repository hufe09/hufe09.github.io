---
layout:     keynote
title:      "Jupyter Notebook Slide"
subtitle:   "🎞  Jupyter Notebook 幻灯片"
iframe:     "//hufe09.github.io/html/jupyter_notebook_slide.slides.html"
author:     "Hufe"
tags:
    - Slides
    - Jupyter Tools
---


# Jupyter Notebook Slide

## 打开幻灯片视图
**View ->  Cell Toolbar -> Slideshow**

![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/o1ZgHtAwR9Mx32z.png)

## 有下面几种显示形式
- Slide
- Sub-Slide
- Fragment
- Skip
- Notes

## Slide
Slide 之间是左右切换。

## Sub-Slide
同一个 slide 和它的菜单 Sub-Slide 之间是上下切换。

## Fragment
同一个 Slide 下，Fragment在同一页显示。


```python
def fib_recur(n):
    """
    斐波那契数列（Fibonacci sequence）
    """
    assert n >= 0, "n > 0"
    if n <= 1:
        return n
    return fib_recur(n-1) + fib_recur(n-2)

for i in range(1, 20):
    print(fib_recur(i), end=' ')
```

    1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987 1597 2584 4181 

### 图片
![post-bg-datas](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/post-bg-datas.jpg)

## Skip
Skip 不会显示在播放页面。

## Notes
Notes 不会显示在播放页面。

## 生成HTML
`$ cd the_path_of_this_notebook`

`$ jupyter nbconvert jupyter_notebook_slide.ipynb --to slides --post serve`

[jupyter_notebook_slide.slides.html](https://hufe09.github.io/html/jupyter_notebook_slide.slides.html)