---
title: three.js制作3D地图(一)
date: 2024-03-28 10:11:00
tags: three.js
categories: three.js
description: 因公司需求所致，我得拾起Three.js并制作一个3D地图，也顺便记录一下复习过程。阅读时长：18min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240326144804.gif
top_img: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240326144804.gif
---
**概要：**在这篇记录中，我们将学习关于Three.js和制作3D地图的部分基础知识。

### 基础知识

如您所见，本篇文章的封面就是公司UI设计的动画效果图，看上去很难，其实一点都不简单。我们需要满足视角适配、标记省份、追光效果、背景呼吸效果。先来了解一些准备工作和基本知识吧：

1. **获取地图数据**：

- 地图数据可以通过各种渠道获取，包括地理信息系统 (GIS) 数据库、在线地图服务 (如OpenStreetMap) 或专门提供地图数据的供应商。
- GeoJSON 是一种常见的地理数据格式，它使用 JSON 格式来描述地理空间信息，如点、线、面等。GeoJSON 文件通常包含地图的几何信息、属性信息和空间参考信息。

2. **GeoJSON 的作用**：

- GeoJSON 是一种开放的地理数据标准，它提供了一种简单且易于理解的方式来存储地理空间信息。
- 使用 Three.js 制作 3D 地图时，GeoJSON 可以用来描述地图的几何形状，例如国家边界、城市位置等。

3. **墨卡托投影**：

- 墨卡托投影是一种用于将地球表面的经纬度坐标投影到平面上的投影方法。它是最常用的地图投影之一，也被广泛应用于在线地图服务中。
- 在制作基于 Web 的 3D 地图时，通常需要将地图数据转换为墨卡托投影坐标系，因为它适合在 Web 地图中显示，并且能够减少图形变形和失真。

5. **图形学基础**： 

    <img src="https://cdn.jsdelivr.net/gh/pengpen1/blog-images/%E8%A7%86%E9%94%A5%E4%BD%93.png" width = "300" align=center />

上面这个是视锥体的透视图，对应着PerspectiveCamera的四个参数，fov — 摄像机视锥体垂直视野角度，
aspect — 摄像机视锥体长宽比，near — 摄像机视锥体近端面，far — 摄像机视锥体远端面。这个只是举例，原生WebGL和图形学是Three.js的底层知识，想要深入学习的话，图形学是少不了滴。书籍推荐，入门：《WebGL编程指南》 图形学：《交互式计算机图形学基于WebGL的自顶向下方法》

6. **点线面**：在Three.js中，点（Point）、线（Line）、面（Mesh）是表示不同几何对象的基本元素，它们用于构建3D场景中的各种物体和形状。在Three.js中，点由一个包含三个分量（x、y、z）的向量表示，通常称为顶点（Vertex），可以使用THREE.Vector3类来创建和操作点。线由一组顶点组成，并且可以选择性地指定线的颜色、材质等属性，可以使用THREE.Line类来创建线对象。面是由一组点组成的封闭平面几何体，用于表示空间中的平面或立体物体。

7. **几何体(Geometry)**：

three.js提供了各种各样的几何体API，用于描述三维空间中的形状和结构。如立方缓冲几何体(四边形)，`const geometry = new THREE.BoxGeometry( 1, 1, 1 ); `

8. **材质(Material)**：

- 材质用于描述物体表面的外观和属性，例如颜色、光滑度、反射率等。
- 在Three.js中，材质控制着物体在场景中的视觉效果，例如如何反射光线、如何受到光照的影响等。
- Three.js提供了多种类型的材质，例如基础材质（Basic Material）、Lambert材质、Phong材质、标准材质（Standard Material）等，每种材质都有不同的属性和效果。
- 通过指定不同的材质属性，可以实现各种视觉效果，如金属、塑料、玻璃等材质的外观。

9. **网格模型(Mesh)**：

- 网格模型是将几何体和材质结合起来形成的三维物体。
- 在Three.js中，网格模型由一个几何体和一个材质组成，它们通过THREE.Mesh类来创建和管理。
- 网格模型可以看作是具有形状和外观的三维物体，它们可以在场景中被添加、移动、旋转、缩放等。
- 通过创建网格模型，可以在场景中展示具体的物体，如立方体、球体、汽车、人物等。

在上面，我们了解了地图数据，然后和一些three.js的基础，总之就是用几何体描述物体的形状和结构，然后用材质描述物体的外观和属性，最后用网格模型将几何体和材质组合在一起，形成了具体的三维物体，它们共同构成了Three.js中的三维场景。

实际生活中，一个物体往往是有位置的，对于threejs而言也是一样的，我们可以.position定义网格模型Mesh在三维场景中的位置，然后将定义好的网格模型添加到三维场景scene中。如果这个网格模型在相机的可视范围内的话，那么渲染器会执行渲染操作，将画面渲染出来。





### **项目需求**

- 地图需要有三种颜色层级，未加入系统的省份：41, 68, 93，已加入系统的省份：42, 56, 68，近期上传数据的身份：58, 140, 166
- 描边以及追光效果





### 学习顺序

#### 1.分析数据

点可以创建出线，通过组合线可以创建出面，进而创建出立体物体，画出地图第一步就是要有正确的地理数据让我们构建出正确的地图，所以我们先从地图json数据开始学习分析。如下所示，这是我所用的GeoJSON数据，就两个属性，type和features

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240330181336.png)

GeoJSON 中的每个对象都有一个类型字段(type)，用于指示对象的类型。常见的类型包括：

- Point：表示一个点
- LineString：表示一条线
- Polygon：表示一个多边形
- MultiPoint：表示多个点的集合
- MultiLineString：表示多条线的集合
- MultiPolygon：表示多个多边形的集合
- Feature：表示一个地理特征
- FeatureCollection：表示多个地理特征的集合

我们点开features继续往下看，嘿，一下多了好多属性，我们一个一个看，name和行政区划代码adcode就不必多说了吧，geometry里面是我们需要的几何信息，这里的是一个多重多边形，coordinates就包含了这些多重多边形的坐标点位，这里去掉一层数组其实就是Polygon的格式了。质心Centroid，就是一个几何对象的中心点，在我们标记一个省份的时候有用。

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240330182121.png)



#### 2.辅助坐标轴

基础的我们就直接跳过了，感兴趣的可以去看看[官方文档](https://threejs.org/docs/index.html#manual/zh/introduction/Creating-a-scene)，一般我们绘制3D效果都是要画个辅助坐标轴的，`THREE.AxesHelper()`参数就是你想要的坐标轴尺寸，three.js坐标轴颜色红**R**、绿**G**、蓝**B**分别对应坐标系的**x**、**y**、**z**轴，对于three.js的3D坐标系默认**y轴朝上**。



### 实现追光效果

核心逻辑：是用同一份地图json中的中国地图轮廓坐标 + 着色器 + 设置opacitys

```js
    // 初始化中国轮廓地图
    const ChinaOutlineParams = {
      // 需要的参数
      lines: [],
      positions: null,
      opacitys: null,
      points: null,
      geometry: null,
      // 速度控制
      currentPos: 0,
      currentPos1: 1800,
      currentPos2: 3600,
      pointSpeed: 10,
      // 追光控制
      pointSize: 3,
      pointColor: "#ffffff",
    };
    const initChinaOutline = async (scene) => {
      let indexBol = true;

      /**
       * 边框 图形绘制
       * @param polygon 多边形 点数组
       * @param color 材质颜色
       * */
      // function lineDraw(polygon, color) {
      //   const lineGeometry = new THREE.BufferGeometry();
      //   const pointsArray = new Array();
      //   polygon.forEach((row) => {
      //     const [x, y] = row;
      //     // 创建三维点
      //     pointsArray.push(new THREE.Vector3(x, -y, 0));
      //     console.log(indexBol);
      //     if (indexBol) {
      //       ChinaOutlineParams.lines.push([x, -y, 0]);
      //     }
      //   });
      //   indexBol = false;
      //   // 放入多个点
      //   lineGeometry.setFromPoints(pointsArray);

      //   const lineMaterial = new THREE.LineBasicMaterial({
      //     color: color,
      //   });
      //   return new THREE.Line(lineGeometry, lineMaterial);
      // }
      // 只是要追光效果的话不需要画地图
      function lineDraw(polygon) {
        polygon.forEach((row) => {
          const [x, y] = row;
          // 创建三维点
          ChinaOutlineParams.lines.push([x, -y, 0]);
          // 想增加点密度来实现更细腻的追光效果，但是效果不理想
          // ChinaOutlineParams.lines.push([x + 0.05, -y, 0]);
          // ChinaOutlineParams.lines.push([x - 0.05, -y, 0]);
          // ChinaOutlineParams.lines.push([x + 0.12, -y, 0]);
          // ChinaOutlineParams.lines.push([x - 0.12, -y, 0]);
        });
        indexBol = false;
      }

      const chinaData = await requestData("./data/map/中国轮廓.json");
      console.log("中国轮廓数据", chinaData);

      // 中国边界
      const feature = chinaData.features[0];
      // const province = new THREE.Object3D();
      // province.properties = feature.properties.name;
      // 点数据
      const coordinates = feature.geometry.coordinates;
      coordinates.forEach((coordinate) => {
        // coordinate 多边形数据
        coordinate.forEach((rows) => {
          const line = lineDraw(rows, 0xe10909);
          // province.add(line);
        });
      });

      // province.position.set(0, 0, 2);
      // province.rotation.set(0, Math.PI, Math.PI);
      // scene.add(province);

      // 着色器相关
      ChinaOutlineParams.geometry = new THREE.BufferGeometry();
      ChinaOutlineParams.positions = new Float32Array(
        ChinaOutlineParams.lines.flat(1)
      );
      // 设置顶点
      ChinaOutlineParams.geometry.setAttribute(
        "position",
        new THREE.BufferAttribute(ChinaOutlineParams.positions, 3)
      );
      // 设置 粒子透明度为 0
      ChinaOutlineParams.opacitys = new Float32Array(
        ChinaOutlineParams.positions.length
      ).map(() => 0);
      ChinaOutlineParams.geometry.setAttribute(
        "aOpacity",
        new THREE.BufferAttribute(ChinaOutlineParams.opacitys, 1)
      );

      const vertexShader = `
        attribute float aOpacity;
        uniform float uSize;
        varying float vOpacity;

        void main(){
            gl_Position = projectionMatrix*modelViewMatrix*vec4(position,1.0);
            gl_PointSize = uSize;

            vOpacity=aOpacity;
        }
        `;

      const fragmentShader = `
        varying float vOpacity;
        uniform vec3 uColor;

        float invert(float n){
            return 1.-n;
        }

        void main(){
          if(vOpacity <=0.2){
              discard;
          }
          vec2 uv=vec2(gl_PointCoord.x,invert(gl_PointCoord.y));
          vec2 cUv=2.*uv-1.;
          vec4 color=vec4(1./length(cUv));
          color*=vOpacity;
          color.rgb*=uColor;
          gl_FragColor=color;
        }
        `;
      const material = new THREE.ShaderMaterial({
        vertexShader: vertexShader,
        fragmentShader: fragmentShader,
        transparent: true, // 设置透明
        // emissive: 0xff0000,
        // lending: THREE.AdditiveBlending, //在使用此材质显示对象时要使用何种混合。加法
        uniforms: {
          uSize: {
            value: ChinaOutlineParams.pointSize,
          },
          uColor: {
            value: new THREE.Color(ChinaOutlineParams.pointColor),
          },
        },
      });
      ChinaOutlineParams.points = new THREE.Points(
        ChinaOutlineParams.geometry,
        material
      );
      scene.add(ChinaOutlineParams.points);

      ChinaOutlineParams.points.position.set(0, 0, 1.01);
      ChinaOutlineParams.points.rotation.set(0, Math.PI, Math.PI);
      //UI设计  12秒1圈   长度大概是 64/360
      // 渲染
      function render() {
        // console.log(ChinaOutlineParams.currentPos, ChinaOutlineParams.lines);
        if (
          ChinaOutlineParams.points &&
          ChinaOutlineParams.geometry.attributes.position
        ) {
          ChinaOutlineParams.currentPos += ChinaOutlineParams.pointSpeed;
          for (let i = 0; i < ChinaOutlineParams.pointSpeed; i++) {
            ChinaOutlineParams.opacitys[
              (ChinaOutlineParams.currentPos - i) %
                ChinaOutlineParams.lines.length
            ] = 0;
          }

          for (let i = 0; i < 888; i++) {
            ChinaOutlineParams.opacitys[
              (ChinaOutlineParams.currentPos + i) %
                ChinaOutlineParams.lines.length
            ] = i / 50 > 2 ? 2 : i / 50;
          }

          ChinaOutlineParams.currentPos1 += ChinaOutlineParams.pointSpeed;
          for (let i = 0; i < ChinaOutlineParams.pointSpeed; i++) {
            ChinaOutlineParams.opacitys[
              (ChinaOutlineParams.currentPos1 - i) %
                ChinaOutlineParams.lines.length
            ] = 0;
          }

          for (let i = 0; i < 888; i++) {
            ChinaOutlineParams.opacitys[
              (ChinaOutlineParams.currentPos1 + i) %
                ChinaOutlineParams.lines.length
            ] = i / 50 > 2 ? 2 : i / 50;
          }

          ChinaOutlineParams.currentPos2 += ChinaOutlineParams.pointSpeed;
          for (let i = 0; i < ChinaOutlineParams.pointSpeed; i++) {
            ChinaOutlineParams.opacitys[
              (ChinaOutlineParams.currentPos2 - i) %
                ChinaOutlineParams.lines.length
            ] = 0;
          }

          for (let i = 0; i < 300; i++) {
            ChinaOutlineParams.opacitys[
              (ChinaOutlineParams.currentPos2 + i) %
                ChinaOutlineParams.lines.length
            ] = i / 50 > 2 ? 2 : i / 50;
          }
          ChinaOutlineParams.geometry.attributes.aOpacity.needsUpdate = true;
        }
        requestAnimationFrame(render);
      }
      requestAnimationFrame(render);
    };
```



### 实现发光效果

**方案1 使用泛光后期效果**

核心逻辑：将发光的物体放到一个层级，没发光的放到一个层级。默认所有对象是在0层，我们将0层设置为发光层，不需要发光的对象设置在1层，我们需要关闭自动清除，然后在requestAnimationFrame循环里面手动更改相机层级、渲染后期效果、清除缓冲区深度数据。注意：camera要和渲染的物体一个层级时才能被渲染出来。

```js
//清除深度缓冲区深度数据
this.renderer.clearDepth();

//关闭渲染每一帧之前自动清除其输出
this.renderer.autoClear = false;

//手动清除颜色、深度或模板缓存
this.renderer.clear();
```

```js
          this.renderer.clear();
          this.camera.layers.set(0);
          if (this.composer) {
            this.composer.render();
          }
          // 清除深度缓冲区深度数据
          this.renderer.clearDepth();
          this.camera.layers.set(1);
          this.renderer.render(this.scene, this.camera);
          this.animationStop = window.requestAnimationFrame(() => {
            this.loop();
          });
```



**方案2 使用聚光灯**

核心逻辑：将背景材质改为可以受光源影响，并添加合适高度的聚光灯以此模拟发光效果。

```js
          // 聚光灯,颜色，光照强度，光源照射的最大距离，光线照射范围的角度，聚光锥的半影衰减百分比，沿着光照距离的衰减量
          let spotLight = new THREE.SpotLight(
            0x1af0ff,
            1.8,
            200,
            Math.PI / 3,
            0,
            2
          );
          spotLight.position.set(...centerXY, 25);
          spotLight.target.position.set(...centerXY, 0); // 设置聚光灯的目标位置
          this.addObject(spotLight.target); // 需要将目标的位置加入到场景中
          spotLight.penumbra = 0.45; // 设置聚光灯的边缘模糊程度
```



### 实现倒影效果

threejs中使用Reflector实现出倒影效果，并且需要显示镜面下面的内容，镜面的其他地方为透明（例如模仿地板倒影效果）

three.js给我们封装好了现成的可以实现镜面效果的类Reflector，使用也很简单

```js
import { Reflector } from "three/examples/jsm/objects/Reflector.js";


// 创建镜面反射器
const reflector = new Reflector(plane, {
    clipBias: 0.003, // 裁剪偏差
    textureWidth: window.innerWidth * window.devicePixelRatio,
    textureHeight: window.innerHeight * window.devicePixelRatio,
    color: 0x889999,
});
scene.add(reflector);
```

其中，第一个参数用于指定 Reflector 的几何形状。第二个参数是可选的，是一个配置选项对象，包含以下属性：

- color: 反射面的颜色，可以是一个 CSS 颜色字符串或是一个 three.js 的 Color 对象，默认值是 0x7F7F7F。
- textureWidth: 反射纹理的宽度，单位是像素，默认值是 512。
- textureHeight: 反射纹理的高度，单位是像素，默认值是 512。
- clipBias: 剪裁偏移值，用于控制剪裁平面的位置，可以用于解决渲染的反射对象和原始对象之间的闪烁问题，默认值是 0。
- shader: 用于渲染反射效果的着色器程序，可以是一个 three.js 的 ShaderMaterial 对象，默认值是 undefined，表示使用内置的着色器。
- encoding: 反射纹理的编码格式，默认值是 LinearEncoding。
- multisample: 反射纹理的多重采样级别，用于抗锯齿，默认值是 0，表示不使用多重采样。

Reflector 对象有以下方法：

- getRenderTarget(): 获取渲染到的反射纹理对象，可以用于后续的处理。
- dispose(): 释放 Reflector 对象的资源，包括纹理和几何形状。



这样子确实创建出了镜面效果，但是确实黑色的一片，且把下发的背景给遮挡住了，我想要的效果是需要显示镜面下面的内容，镜面的其他地方为透明（例如模仿地板倒影效果）。然后就去网上找了一下解决方案，有修改源码的：

找到 `three/examples/jsm/objects/Reflector` 文件。

```js
fragmentShader: /* glsl */`uniform vec3 color;uniform sampler2D tDiffuse;varying vec4 vUv;#include <logdepthbuf_pars_fragment>float blendOverlay( float base, float blend ) {

// 需要修改的第一个地方， 这里是要虚化倒影的 ******************************************
*return( base < 0.5 ? ( 2.0 * base * blend ) : ( 1.0 - 2.0 * ( 1.0 - base ) * ( 1.0 - blend ) ) );// 修改为:return( base < 1.0 ? ( 0.3 * base * blend ) : ( 1.0 - 2.0 * ( 1.0 - base ) * ( 1.0 - blend ) ) );}vec3 blendOverlay( vec3 base, vec3 blend ) {return vec3( blendOverlay( base.r, blend.r ), blendOverlay( base.g, blend.g ), blendOverlay( base.b, blend.b ));}void main() {#include <logdepthbuf_fragment>vec4 base = texture2DProj( tDiffuse, vUv );

// 需要修改的第二个地方， 设置背景透明的 *******************************************
gl_FragColor = vec4( blendOverlay( base.rgb, color ), 1.0 );// 修改为// gl_FragColor = vec4( blendOverlay( base.rgb, color ), .01 );#include <encodings_fragment>
```

结果是不行，就这种垃圾方案竟然要钱...



然后我就改变思路，倒影不能设置为透明的，那背景总可以吧，把倒影放在背景层的下面，设置背景透明度，最后成功了：

```js
    // 初始化背景
    const initSceneImag = (scene, url, size) => {
      new THREE.TextureLoader().load(url, (texture) => {
        const img = texture.image;
        let height = (img && img.height) || 1080;
        let width = (img && img.width) || 1920;
        console.log("size", height, width); // 1080*1920
        height = size * height;
        width = size * width;
        const mat = new THREE.MeshStandardMaterial({
          map: texture,
          // side: THREE.DoubleSide,
          transparent: true,
          opacity: 0.75,
        });
        const geom = new THREE.PlaneGeometry(width, height);
        const mesh = new THREE.Mesh(geom, mat);
        mesh.position.set(...centerXY, bottomZ + 0.1);
        mesh.position.y += 2.5;
        mesh.position.x += 0.9;
        // mesh.receiveShadow = true; // 设置接受阴影
        scene.add(mesh);

        // 创建镜面
        const mirrorOptions = {
          clipBias: 0.03,
          textureWidth: window.innerWidth * window.devicePixelRatio,
          textureHeight: window.innerHeight * window.devicePixelRatio,
          transparent: true,
          opacity: 0.5,
        };
        const mirror = new Reflector(geom, mirrorOptions);
        // mirror.receiveShadow = true; // 设置接受阴影
        mirror.position.set(...centerXY, bottomZ - 0.2);
        mirror.position.y += 2.5;
        mirror.position.x += 0.9;
        scene.add(mirror);
      });
      scene.fog = new THREE.Fog(0xffffff, 2, 90);
    };
```

效果图如下所示：

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/3d4ec957317800b9b475862b6dad04f.png)




### 优化需求

- 光柱会被背景挡住，渲染顺序问题？ 给渲染器设置禁用深度测试倒是可以解决，就是有些渲染会出问题`renderer.sortObjects = false` **√采用了CSS2DRenderer渲染gif替代手绘光柱**
- 优化光柱效果 **√ 增加了复制次数**



### 疑问解答

**1.Polygon 和 MultiPolygon各是什么？**

- 在地理信息系统（GIS）中，Polygon 和 MultiPolygon 是用于描述地理空间多边形的两种几何类型。
- Polygon（多边形）：Polygon 是一种简单的几何类型，用于表示封闭的二维区域，通常由一组有序的点（顶点）组成，最后一个点会自动连接到第一个点，形成一个封闭的区域。
  例如，一个国家的边界、一个湖泊的边界或者一个建筑物的外轮廓都可以用 Polygon 来描述。
- MultiPolygon（多重多边形）：MultiPolygon 是一种复杂的几何类型，用于表示多个独立的、不相交的多边形组成的集合。每个 MultiPolygon 对象包含多个 Polygon 对象，每个 Polygon 表示一个独立的封闭区域。例如，一个国家可能由多个岛屿组成，每个岛屿的边界都可以用一个 Polygon 来描述，而这些岛屿的集合则可以用一个 MultiPolygon 来表示。



**2.如何设置场景颜色、场景图片、全景图呢？**

```js
    //设置场景颜色
	onSetSceneColor(color) {
		this.scene.background = new THREE.Color(color)
	}
	//设置场景图片
	onSetSceneImage(url) {
		this.scene.background = new THREE.TextureLoader().load(url);
	}
	// 设置全景图
	onSetSceneViewImage(url) {
		const texture = new THREE.TextureLoader().load(url);
		texture.mapping = THREE.EquirectangularReflectionMapping
		this.scene.background = texture
		this.scene.environment = texture
	}
```



**3.如何统一设置透明的呢？**

我们可以用一个容器对需要设置透明度的3D对象进行统一管理，比如`THREE.Object3D` 和 `THREE.Group` 。
THREE.Object3D 是 Three.js 中的基类，所有的 3D 对象都是 THREE.Object3D 类或其子类的实例。它是场景中所有对象的通用容器，具有基本的属性和方法，如位置、旋转、缩放等。可以将几乎任何类型的对象添加到 THREE.Object3D 中，包括几何体、网格、摄像机等。
THREE.Group 是 THREE.Object3D 的子类，它提供了更高级别的组织和管理功能。与 THREE.Object3D 相比，THREE.Group 具有更多的方法来管理其子对象，例如添加、移除和排序子对象。THREE.Group 对象本身没有几何形状或材质，它只是一种组织方式，用于将相关对象分组并对它们进行集体操作。
`THREE.Object3D` 和 `THREE.Group` 都可以设置透明度（opacity）属性，但是它们本身并不具有透明度属性。透明度通常是通过设置对象的材质（Material）来实现的。我们可以遍历 `THREE.Group` 的子对象并设置它们的材质透明度属性。

```js
// 创建一个 THREE.Group
var group = new THREE.Group();

// 添加一些子对象到组内
group.add(object1);
group.add(object2);
// 添加更多的对象...

// 遍历组内的子对象，设置它们的透明度
group.traverse(function (child) {
    if (child instanceof THREE.Mesh) {
        // 检查对象是否为 Mesh，因为透明度只对 Mesh 有效
        // 设置透明度为 0.5
        child.material.opacity = 0.5;
        // 开启透明渲染
        child.material.transparent = true;
    }
});

// 将组添加到场景中
scene.add(group);

```



### 参考链接

- [用Three.js搞个炫酷的3D区块地图 - 掘金 (juejin.cn)](https://juejin.cn/post/7250375753598844983)

