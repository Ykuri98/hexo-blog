---
title: Hexo美化记录
date: 2022-05-04 21:44:19
categories:
- 技术
tags:
- hexo
---











一些Hexo美化技术分享

<!-- more -->

## live 2D

在git中输入`$ npm install --save hexo-helper-live2d`安装live 2D插件。

在_config.yml文件下添加如下配置

```yaml
live2d:
    enable: true
    scriptFrom: local
    model: 
        use: live2d-widget-model-haruto #模型选择，与文件包同名
    display: 
        position: right  #模型位置
        width: 150       #模型宽度
        height: 300      #模型高度
    mobile: 
        show: false      #是否在手机端显示
```

再次在git中输入`npm install packagename`，packagename为https://github.com/xiazeyu/live2d-widget-models下的live 2D文件包。

## 动态背景

在主题文件夹下的layout\layout.ejs中加入如下代码

```html
<!--动态线条背景-->
<script type="text/javascript"
color="220,220,220" opacity='0.7' zIndex="-2" count="200" src="//cdn.bootcss.com/canvas-nest.js/1.0.0/canvas-nest.min.js">
</script>
```

注意一定要放在`<body>`中。

## 网页标题优化

在主题文件夹下的source\js中新建一个FunnyTitle.js 文件，在里面填写如下代码：

```javascript
 var OriginTitle = document.title;
 var titleTime;
 document.addEventListener('visibilitychange', function () {
     if (document.hidden) {
         $('[rel="icon"]').attr('href', "/img/trhx2.png");
         document.title = 'ヽ(●-`Д´-)ノ你丑你就走！';
         clearTimeout(titleTime);
     }
     else {
         $('[rel="icon"]').attr('href', "/img/trhx2.png");
         document.title = 'ヾ(Ő∀Ő3)ノ你帅就回来！' + OriginTitle;
         titleTime = setTimeout(function () {
             document.title = OriginTitle;
         }, 2000);
     }
 });
```

在主题文件夹下的layout\layout.ejs中加入如下代码：

```html
<!--浏览器搞笑标题-->
<script type="text/javascript" src="\js\FunnyTitle.js"></script>
```

更多美化可以访问该知乎文章：https://zhuanlan.zhihu.com/p/69211731
