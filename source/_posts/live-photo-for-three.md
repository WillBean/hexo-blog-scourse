---
title: Three.js实现仿IOS live图效果
date: 2017-05-09 20:57:44
tags: 
- Three.js
- Javascript
- HTML
- Canvas
---

### 一、需求分析

四月出游浪了一波，拍了一堆live图的照片，于是乎就想做个Web页，把这些照片展示出来，正逢最近学习了一些Three.js，就想着用上着技术做个小玩意出来。上网搜索了一波，苹果官网推出了一个live photos的[框架](https://cdn.apple-livephotoskit.com/lpk/1/livephotoskit.js)，但是并不适用于canvas，就寻思着模仿着做一个吧~

### 二、组件设计

既然是模仿，那就要知道别人是怎么做的。新建了个HTML文件，导入[livephotoskit.js](https://cdn.apple-livephotoskit.com/lpk/1/livephotoskit.js)，看着API写了个Demo，运行，开启开发者工具，粗略地研究了一下。因为将Live图导入到电脑中，会变为JPG和MOV格式的两个文件，大家都知道，一个是图片资源，一个是视频资源，而调用[livephotoskit.js](https://cdn.apple-livephotoskit.com/lpk/1/livephotoskit.js)时也需要将着两个文件路径作为参数传入，渲染出来的是一个静态的图片，点击图片上方的按钮，就渐出隐藏图片，显示并播放置于图片后方的视频，当视频播放完毕后再渐入显示图片后隐藏视频。

那么就有大致的实现思路了：将图片和视频重叠放置，必要时显示或隐藏。

在看Three.js 实例的时候，发现了一个视频播放Demo，效果如下图（图片较大，可能加载很久甚至加载不出来）：

<div align='center'>
<img src='https://raw.githubusercontent.com/WillBean/react-native-summary.github.io/master/images/live-photo-for-three/Animation2.gif' width='95%'>
</div>

就想着将这个效果改一改，给用上。

为了便于复用，就将这个组件以类的形式编写出来吧。

这个类起码要包含以下属性和方法：

> 1. options对象，传递组件长宽高、横向纵向块数、动画帧数
> 2. init方法，初始化操作，生成图片、视频实例
> 3. show方法，显示整个组件
> 4. hide方法，隐藏整个组件
> 5. play方法，播放视频
> 6. pause方法，暂停视频

### 三、效果展示

<div align='center'>
<img src='https://raw.githubusercontent.com/WillBean/react-native-summary.github.io/master/images/live-photo-for-three/Animation.gif' width='95%'>
</div>

具体代码就不展示了，源码放在[github](https://github.com/WillBean/photo-for-three.js)，有兴趣的朋友可以去看看。