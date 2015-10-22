---
layout: post
title:  "移动端Web优化方案"
date:   2015-10-22 11:00:00
categories: mobile h5 web
---

### 优化的几点方案

1. 减少HTTP请求

`
合并图片（css sprites），合并CSS和JS文件；图片较多的页面也可以使用 lazyLoad 等技术进行优化。
`

2. 请正确理解 Repaint（重绘） 和 Reflow（重排）

3. 减少DOM操作

4. 使用JSON格式来进行数据交换

5. 高效使用HTML标签和CSS样式

6. 使用CDN加速（内容分发网络）

7. 将CSS和JS放到外部文件中引用，CSS放头，JS放尾

8. 精简CSS和JS文件

9. 压缩图片和使用图片Sprite技术　





### 参考文章：
[http://www.cnblogs.com/Darren_code/archive/2011/12/31/property.html](http://www.cnblogs.com/Darren_code/archive/2011/12/31/property.html){:target="_blank"}

