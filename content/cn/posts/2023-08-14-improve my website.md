---
title: "网站搭建: 个人网站搭建后续 "
date: 2023-08-13
author: 余忻萌
categories:
  - test
tags:
  - article
---

- 记录后续更新优化网站过程中的一些经验。
- Markdown 学习笔记。

---

### **网站优化**
**1.  在网站中嵌入视频**
- 以bilibili网站视频为例，通过分享按钮可以直接获得嵌入代码
```
<iframe src="//player.bilibili.com/player.html?aid=805646984&bvid=BV1N34y1D7vX&cid=415023650&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>
```
- 嵌入的方法：
  - 获取aid后:  ```{{bilibili 805646984}}```
  - 获取cid
  - 直接插入代码

**2.  hugo网站中引入访问计数功能**
- 使用访问计数插件 「不蒜子」
- 集成方法：
  - 官网代码：
 ```
 <script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
 <span id="busuanzi_container_site_pv">本站总访问量<span id="busuanzi_value_site_pv"></span>次</span>
 ```
  - 使用到自己的hugo主题中：
    - 在themes目录下的layouts下的partials下的head.html中引入不蒜子js文件，代码为：
  ```
  <!-- 不蒜子 -->
  <script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
  ```
  
    - 在页面添加统计代码：

  ```
  <span id="busuanzi_container_site_pv">
    本站访问量：<span id="busuanzi_value_site_pv"></span>次
  </span>
  &nbsp;
  <span id="busuanzi_container_site_uv">
      您是本站第 <span id="busuanzi_value_site_uv"></span> 位访问者
  </span>
  ```

    - 找到themes目录下的layouts下的_default下的single.html,添加以下代码:

  ```
  <h5 id="wc" style="font-size: 1rem;text-align: center;">{{ .FuzzyWordCount }} Words|Read in about {{ .ReadingTime }} Min|本文总阅读量<span id="busuanzi_value_page_pv"></span>次</h5>
  ```

### **Markdown 语法笔记**

