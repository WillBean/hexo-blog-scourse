---
title: WebVR初体验
date: 2017-05-10 10:50:44
tags:
- Three.js
- Javascript
- WebVR
- HTML
- Canvas
---

结合上一篇文章[Three.js实现仿IOS live图效果](https://willbean.github.io/2017/05/09/live-photo-for-three/)，我们来体验一波WebVR~

本文基于[webvr-polyfill](https://github.com/googlevr/webvr-polyfill)，苦于没钱购买专业版的VR设备，就只能做个可以用普通VR眼镜看的程序了。所用到的原理倒也简单，[webvr-polyfill](https://github.com/googlevr/webvr-polyfill)会把屏幕分为左右两视图，并加入基于陀螺仪的控制器，当佩戴VR眼镜时即可看到3D效果。

### 加入背景

上一篇中实现了[Photo.js](https://github.com/WillBean/photo-for-three.js/blob/master/js/Photo.js)组件，现在给场景加入一些星星背景，以免过于单调。

```javascript
/*背景星星*/
var particles = 20000;  //星星数量
/*buffer做星星*/
var bufferGeometry = new THREE.BufferGeometry();

var positions = new Float32Array( particles * 3 );
var colors = new Float32Array( particles * 3 );

var color = new THREE.Color();

var gap = 1000; // 定义星星的最近出现位置

for ( var i = 0; i < positions.length; i += 3 ) {

// positions

  /*-2gap < x < 2gap */
  var x = ( Math.random() * gap *2 )* (Math.random()<.5? -1 : 1);
  var y = ( Math.random() * gap *2 )* (Math.random()<.5? -1 : 1);
  var z = ( Math.random() * gap *2 )* (Math.random()<.5? -1 : 1);

  /*找出x,y,z中绝对值最大的一个数*/
  var biggest = Math.abs(x) > Math.abs(y) ? Math.abs(x) > Math.abs(z) ?　'x' : 'z' :
    Math.abs(y) > Math.abs(z) ? 'y' : 'z';

  var pos = { x, y, z};

  /*如果最大值比n要小（因为要在一个距离之外才出现星星）则赋值为n（-n）*/
  if(Math.abs(pos[biggest]) < gap) pos[biggest] = pos[biggest] < 0 ? -gap : gap;

  x = pos['x'];
  y = pos['y'];
  z = pos['z'];

  positions[ i ]     = x;
  positions[ i + 1 ] = y;
  positions[ i + 2 ] = z;

// colors

  /*70%星星有颜色*/
  var hasColor = Math.random() > 0.3;
  var vx, vy, vz;

  if(hasColor){
    vx = (Math.random()+1) / 2 ;
    vy = (Math.random()+1) / 2 ;
    vz = (Math.random()+1) / 2 ;
  }else{
    vx = 1 ;
    vy = 1 ;
    vz = 1 ;
  }

  /*var vx = ( Math.abs(x) / n*2 ) ;
   var vy = ( Math.abs(y) / n*2 ) ;
   var vz = ( Math.abs(z) / n*2 ) ;*/

  color.setRGB( vx, vy, vz );

  colors[ i ]     = color.r;
  colors[ i + 1 ] = color.g;
  colors[ i + 2 ] = color.b;
}

bufferGeometry.addAttribute( 'position', new THREE.BufferAttribute( positions, 3 ) );
bufferGeometry.addAttribute( 'color', new THREE.BufferAttribute( colors, 3 ) );
bufferGeometry.computeBoundingSphere();

/*星星的material*/
var material = new THREE.PointsMaterial( { size: 6, vertexColors: THREE.VertexColors } );
var particleSystem = new THREE.Points( bufferGeometry, material );
scene.add( particleSystem );
```

以上代码引自[SolarSystem](https://github.com/SoAanyip/SolarSystem.git)，实例化一个BufferGeometry对象，随机生成星星的位置与颜色并绑定到该对象上，而后以BufferGeometry对象为参数实例化Points对象并加入到场景中。

### 随机分布Photo对象

```javascript
function setRandomPosition(mesh, distance) {
  var ang1 = Math.PI * Math.random() - Math.PI / 2,
    ang2 = Math.PI * Math.random() * 2,
    dist = Math.cos(ang1) * distance;

  mesh.position.y = Math.sin(ang1) * distance;
  mesh.position.x = Math.cos(ang2) * dist;
  mesh.position.z = Math.sin(ang2) * dist;
}
```

初始化Photo对象时调用以上函数，将其随机分布到以半径为distance的球体表面。

### 控制Photo对象的显示与隐藏

现在要使Photo对象位于显示器中部时显示并播放视频，其它时候隐藏。

在循环体中加入以下代码

```javascript
var smallCamera = camera.clone(); // 克隆camera
smallCamera.fov = 20; // 缩小视角
smallCamera.updateProjectionMatrix();
for(var i =0;i<meshes.length;i++){
  var cont = meshes[i];
  showUp = cont.position.clone().project(smallCamera)
  var shouldShow = (showUp.x < 1 && showUp.x > -1 && showUp.y < 1 && showUp.y > -1 && showUp.z < 1 && showUp.z > -1) // 判断是否应该显示

  if(shouldShow ){
    cont.show()
  }
  if(!cont.counter){
    cont.play()
  }
  if( !shouldShow ){
    cont.hide()
  }
}
```

以上代码用于判断对象是否出现在camera可视范围内，但有个bug，就是存在于camera正后方的元素，shouldShow也会为true。

### 加入WebVR

重头戏来了，现在我们要加入WebVR使之成为3D场景。

引入js框架

```html
<script src="js/VRControls.js"></script>
<script src="js/VREffect.js"></script>
<script src="js/webvr-polyfill.js"></script>
```

实例化组件

```javascript
controls = new THREE.VRControls(camera);
effect = new THREE.VREffect(renderer);
effect.setSize(window.innerWidth, window.innerHeight);

navigator.getVRDisplays().then(function(displays) {
  if (displays.length > 0) {
    vrDisplay = displays[0];
    // Kick off the render loop.
    vrDisplay.requestAnimationFrame(loop);
  }
});
```

调用

```javascript
document.querySelector('button#vr').addEventListener('click', function() {
  vrDisplay.requestPresent([{source: renderer.domElement}]);
});
```

### 效果展示

<div align='center'>
<img src='https://raw.githubusercontent.com/WillBean/react-native-summary.github.io/master/images/three-js-webvr-practice/three.png' width='95%'>
</div>

### 性能优化方案

开启VR模式后，卡顿现象异常严重，甚至无法正常使用，主要可能是因为Photo渐入渐出动画的计算量过于巨大，这就有几点优化的方案：

1. 减少动画帧数，让动画尽快完成。
2. 离散分布对象，减少对象重叠的可能性，防止多个对象同时渐入渐出。
3. 以上办法如果都不能缓解卡顿现象的话，就只能去掉动画了。

源码放在[github](https://github.com/WillBean/photo-for-three.js)，有兴趣的朋友可以去看看。