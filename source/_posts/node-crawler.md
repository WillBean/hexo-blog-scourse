---
title: Node爬虫爬取网页静态资源
date: 2016-12-11 14:30:50
tags: Node, Crawler
---

### 一、准备工作

1. 首先当然是安装node，这一步忽略。
2. 然后是各种模块，本实例用到了http、fs、url、cheerio、request、async、phantom，前三个是node自带的，无需install。
3. 因为要服务器渲染，所以要用到phantomjs，这个需要自行安装一下，最后再配置一下全局环境。

### 二、模块解释

1. cheerio模块用于解析DOM树，进行DOM操作， 具体用法跟JQuery类似，对熟悉JQ的人来说，学会使用也就是几分钟的事。
2. request模块，http模块的高级封装版，便于操作。
3. async模块，解决“恶魔金字塔”问题。
4. phantom模块，在服务器端渲染整个界面，为的是能够爬取到页面上一些通过js等动态加载的内容。

### 三、具体实现

#### 1. 公用接口

```javascript
exports.Strategy = {//js,css,images等文件的保存策略
 "SAVE_IN_ROOT": 1,//保存在根目录下
 "SAVE_IN_SUB_DIR": 2//保存在各级子目录下
};
```

```javascript
exports.uniqueArray = function (arr) {//数组去重
 var hash = {},
     len = arr.length,
     result = [];

 for (var i = 0; i < len; i++){
     if (!hash[arr[i]]){
         hash[arr[i]] = true;
         result.push(arr[i]);
     }
 }
 return result;
};
```

```javascript
exports.timer = function (date,msg) {//简易版计时器
 console.log(msg + " : "+(new Date() - date) +"ms" );
};
```

#### 2. 配置

```javascript
var config = {
 url: "http://localhost:8081/dhay/",//目的网站
 savePath: "J:/nodejs/open-source-spider",//保存路径
 containOutLink: false, //是否爬取外部链接
 totalNum: 10, //爬取页面上限，0为不限制
 endWith: "html",//文件结尾
 saveStrategy: publicAPI.Strategy.SAVE_IN_ROOT,
 getOuterJs: false,//是否爬取远端js
 getOuterCss: false,//是否爬取远端Css
 getOuterImages: false//是否爬取远端图片
};
```

为了简化操作，判断是否为外部资源的方式简化为判断URL是否以http或https为开头，虽然不够严谨，但是能保证爬取下来的网页能根据URL获取到资源。

 保存策略目前也只实现了SAVE_IN_ROOT而已，懒~
 
 #### 3. 全局变量
 
 ```javascript
 var list = [config.url]; //所要爬取的网页链接队列
var count = 0; //当前爬取的网页数
var date = null; 
var urlInfo = url.parse(config.url); //入口链接信息
```

#### 4. 获取服务器渲染之后的页面

```javascript
phantom.create().then(function (ph) {
     ph.createPage().then(function (page) {
         page.open(url).then(function (status) {    
             if (status == 'success') {
                 page.property('content').then(function (html) {
                     console.log(html);
                 }
             }
         })
     });
```
#### 5. 解析DOM树

```javascript
var $ = cheerio.load(html);
var js, css , images;
//获取js列表
var scripts = $("script");
js = getJs(scripts);
console.log(js);
//获取css
var stylesheets = $("link[rel='stylesheet']");
css = getCss(stylesheets);
console.log(css);
//获取图片
var imgs = $("img");
images = getImages(imgs);
console.log(images);
//获取链接
if(!config.totalNum || count < config.totalNum){
   var links = $("a");
   getLink(links,url);
}
```

获取页面中所有的js、css和img，分别进行调用函数，返回所要爬取的文件的url数组。

#### 6. getJs()等函数

```javascript
function getJs(scripts) {
 var res = [];
 scripts.each(function (i, script) {
     var src = script.attribs.src;
     if (!src)
         return;
     if (!config.getOuterJs) {
         if (/^https?/.test(src))
             return;
     }
     res.push(src);
 });
 return publicAPI.uniqueArray(res);
}
```

getJs()函数，遍历每一个元素，判断其src属性是否存在，不存在则跳过，再根据配置判断是否获取外部文件，满足各条件的加入到res数组中，最后去重后返回结果。

 getCss、getImages和getLink方法与上述类似，不同的是getLink多了一些判断和URL格式化操作。
 
 #### 7. 保存网页文本
 
 ```javascript
 var saveHtml = function (url, html, callback) {
 url = url.match(/https?:\/\/((?:(?![\?])[\S])*)/)[1];//截取？以前的字符串
 var endWith = /\/$/.test(url); //判断是否以反斜杠结尾
 url = endWith ? url.match(/(\S*)\/$/)[1] : url; //去除反斜杠
 var reg = new RegExp(/\.html|\.htm|\.asp|\.jsp$/); //判断是否以这些字符串结尾
 var usePathAsName = reg.test(url);
 var array = url.split("/"); //以反斜杠分割字符串
 array[0] = urlInfo.hostname;
 var length = usePathAsName ? array.length-1: array.length;
 var currentPath = config.savePath;

 for (var i = 0; i < length; i++) { //遍历数组，逐层判断当前路径是否存在指定文件夹，不存在则创建
     (function (i) {
         currentPath += "/" + array[i];
         if (fs.existsSync(currentPath)) {
             write(i, callback)
         } else {
             try {
                 fs.mkdirSync(currentPath);
                 write(i, callback)
             }catch (err){
                 console.log(err);
             }
         }
     })(i)
 }

 function write(index, callback) { // 若为最后一个元素，执行写入操作
     if (index == length - 1) {
         var fileName = endWith ? "index." + config.endWith : usePathAsName ? array[array.length-1] : array[array.length-1] + config.endWith; //根据情况选择文件名
         fs.writeFile(currentPath + "/" + fileName, html, function (err) {
             if (err) {
                 console.log(err, "appendFile");
             } else {
                 callback();
             }
         });
     }
 }
};
```

#### 8. 保存js等资源
```javascript
var saveJs = function (url, js, callback ,callback2) {
 if(!js.length){
     callback2(null);
     return;
 }
 var length = js.length;
 var count = 0;
 var root = config.savePath + "/" + urlInfo.hostname;
 if(config.saveStrategy == publicAPI.Strategy.SAVE_IN_ROOT){
     if(fs.existsSync(root)){
         write(callback,callback2)
     }else{
         fs.mkdirSync(root);
         write(callback,callback2)
     }
 }

 function write(callback,callback2) {
     for(var i = 0;i<length;i++){
         (function (i) {
             var reg = new RegExp(/^\//);
             var path = reg.test(js[i]) ? js[i].substring(1) : js[i];
             var array = path.split("/");
             var currentPath = root;
             var len = array.length;
             for(var j =0;j<len-1;j++){ //创建对应目录
                 (function (j) {
                     currentPath += "/" + array[j];
                     if (fs.existsSync(currentPath)) {
                         if(j == len-2){
                             fetch(js[i],function () {
                                 callback(count, js[i])
                             },callback2)
                         }
                     } else {
                         try {
                             fs.mkdirSync(currentPath);console.log(j,4);
                             if(j == len-2){
                                 fetch(js[i],function () {
                                     callback(count, js[i])
                                 },callback2)
                             }
                         }catch (err){
                             console.log("error!")
                         }
                     }
                 })(j);
             }
         })(i);
     }
 }

 function fetch(js,callback,callback2) {
     request(url+js,function (err,res,body) { //获取资源，再写入
         //console.log(body);
         js = js.match(/((?:(?![\?])[\S])*)/)[1];
         if(err){
             count++;
         }else{
             fs.writeFile(root +"/"+ js,body,function (err) {
                 count++;
                 if(count == length)
                     callback2(null);
                 if(err){
                     console.log(err);
                 }else{
                     callback();
                 }
             });

         }
     })
 }
};
```

保存js等资源的方法比保存网页的更为复杂，主要是因为一个页面可能存在多个js、css等资源，这里有两种保存的策略，一是直接将所有文件保存在根目录下，如根目录如localhost，现有js文件链接为“localhost:8080//abc/js/main/js”，将该js文件保存在localhost/js/下，另一种是保存在对应目录下，即localhost/abc/js/下，但是第二种方法会导致出现很多重复的文件，就没有实现出来。

 获取css、images的方法类似，不做赘述。
 
 #### 9. 并行执行写入操作
 
 ```javascript
 async.parallel([
 function (callback) {
     saveHtml(url, html, function () {
         console.log("Page:"+(count+1)+"    Url:"+url+"    success!\n");
         callback(null);
     });
 },
 function (callback) {
     saveJs(url,js, function (x, js) {
         console.log("Page:"+(count+1)+"    Js"+(x+1)+"     Src:"+js+"    Success!\n");
     },callback);
 },
 function (callback) {
     saveCss(url, css, function (x, css) {
         console.log("Page:"+(count+1)+"    Css"+(x+1)+"     Src:"+css+"    Success!\n");
     },callback);
 },
 function (callback) {
     saveImage(url, images, function (x,img) {
         console.log("Page:"+(count+1)+"    Images"+(x+1)+"     Src:"+img+"    Success!\n");
     },callback);
 }
],function (err) {
 page.close();
 if(err){
     console.log(err,"ERROR IN PARALLEL PAGE "+(count+1));
 }else {
     count++;
     console.log("Page:"+(count)+" finished!");
     var cur = list.shift(),next = list[0];
     next = /^https?:/.test(next) ? next : cur+'/'+next;
     if( count < config.totalNum)
         requirePage(next);
 }
})
```

用async模块，并行处理写入操作，当所有写入操作结束后，从list队列获取下一跳地址，循环操作。

#### 10. 测试 爬取http://localhost:8081/ 结果如图：

<div align='center'>
<img src='https://raw.githubusercontent.com/WillBean/react-native-summary.github.io/master/images/node-crawler/dir.png' width='50%'>
</div>