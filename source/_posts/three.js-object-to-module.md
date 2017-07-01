---
title: Three.js obj模型转js并渲染
date: 2017-03-23 14:13:46
tags:  Three.js
---

### 一、格式转化

1. 下载python.到python官网[https://www.python.org/](https://www.python.org/)进行下载，由于我是使用windows，所以直接下载了python3 windows的安装包，一路next完成安装，安装完成再配置一下环境变量即可。
2. 下载three.js提供的格式转换文件——<strong>convert_obj_three.py</strong> （three.js官网可下载，或者到其github直接复制粘贴），注意版本，有python2和python3两个版本，按需下载。
3. 将<strong>convert_obj_three.py</strong> 和你的obj文件放置同一目录，命令行下执行

```bash
python convert_obj_three.py -i infile.obj -o outfile.js 
```

该命令还有其他一些参数，详情可查阅convert_obj_three.py 注释部分。

### 二、模型渲染

我使用的是three.js [r84]版本，较之前面版本有些不同。

```javascript
let loader = new THREE.JSONLoader();
loader.load('./img/port2.js', (geometry, materials)=> {
        var material = new THREE.MultiMaterial(materials);
        var object = new THREE.Mesh(geometry, material);
        scene.add(object);
    }, function (xhr) {
        console.log((xhr.loaded / xhr.total * 100) + '% loaded');
    },
    // Function called when download errors
    function (xhr) {
        console.error(xhr);
    }
);
```