---
title: three.js制作3D地图(二)
date: 2024-05-22 10:46:00
tags: three.js
categories: three.js
description: 上期我们成功的完成了需求，画出了3D地图，这期优化一下进场动画。阅读时长：18min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240326144804.gif
top_img: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240326144804.gif
---
**概要：**在这篇记录中，我们将学习关于Three.js以及进场动画等相关方面的知识。

### 基础知识

#### 顶点

顶点（vertex）是构成3D模型的基本单位之一。每个顶点代表了模型中的一个点，通常具有三维坐标（X, Y, Z）。在三维图形中，顶点不仅用于定义物体的形状，还可以包含其他信息，如颜色、纹理坐标和法线向量等。

顶点数据通常组成一个顶点数组，这些顶点通过顶点着色器在GPU上进行处理。在three.js中，顶点可以通过`THREE.BufferGeometry`或`THREE.Geometry`类来管理，这些类提供了存储和操作顶点数据的功能。

#### 顶点着色器

顶点着色器是一种运行在GPU上的小程序，它的作用是处理顶点数据。顶点着色器对每个顶点独立执行，通常用于执行诸如变换顶点位置（比如模型变换、视图变换和投影变换）、顶点光照计算、颜色或其他属性的调整等操作。顶点着色器的输出通常是顶点的新位置和其他一些可能 影响后续渲染阶段的顶点属性。

#### 片元着色器

片元着色器（有时也称为像素着色器）是图形管线中的另一种类型的着色器，用于计算像素的最终颜色和其他属性。片元着色器在光栅化阶段之后运行，每个光栅化的像素都会调用一次片元着色器。它可以执行多种任务，如应用纹理、执行复杂的光照和阴影计算、处理透明效果和色彩混合等。

顶点着色器处理完顶点数据后，三角形的顶点会被传递到光栅化过程，这个过程决定了屏幕上哪些像素应该用于绘制这些三角形。每个这样的像素称为一个“片元”，对于这些片元，片元着色器会计算最终颜色。因此，顶点着色器和片元着色器共同决定了3D物体在屏幕上的表现形式。

#### 高级着色语言

```js
    <script type="x-shader/x-vertex" id="vertexShader">
      // uniform float amplitude;
      // attribute float size;
      uniform float amplitude;

      attribute vec3 vertexColor;

      varying vec4 varColor;

      void main()
      {
      varColor = vec4(vertexColor, 1.0);

      vec4 pos = vec4(position, 1.0);
      pos.z *= amplitude;

      vec4 mvPosition = modelViewMatrix * pos;

      gl_PointSize = 1.0;
      gl_Position = projectionMatrix * mvPosition;
      }
    </script>

    <script type="x-shader/x-fragment" id="fragmentShader">
      varying vec4 varColor;

      void main()
      {
      gl_FragColor = varColor;
      }
    </script>
    
var shaderMaterial = new THREE.ShaderMaterial({
   attributes: shaderAttributes,
   uniforms: shaderUniforms,
   vertexShader: document.getElementById("vertexShader").textContent,
   fragmentShader: document.getElementById("fragmentShader").textContent,
 });
```

上述着色器代码是用一种名为GLSL（OpenGL Shading Language）的语言编写的。因为WebGL是基于OpenGL的，而OpenGL使用GLSL(OpenGL Shading Language)这一着色器语言，是一种接近C语言的代码。所以我们需要学习使用GLSL，用于在图形处理单元（GPU）上编写代码的高级着色语言。它专门用于定义图形渲染管线中的可编程阶段，比如顶点着色器和片元着色器。

着色器代码可以写在单独的文件中（顶点着色器后缀为`.vs`，片元着色器后缀为`.fs`），也可以定义在`<script>`标签中。UV映射是将每个面片贴的图统一映射到一张纹理上，记录每个面片贴图在纹理上对应的位置。

#### 材质

材质是独立于物体顶点信息之外的与渲染效果相关的属性，通过设置材质可以改变物体的颜色、纹理贴图、光照模式等。

- 基本材质 MeshBasicMaterial-使用基本材质的物体，渲染后物体的颜色始终为该材质的颜色，而不会由于光照产生明暗、阴影效果
- Lambert材质 MeshLambertMaterial-符合Lambert光照模型的材质，主要特点是只考虑漫反射而不考虑镜面反射的效果，适合大部分物体的漫反射效果，但不适合金属、镜子等需要镜面反射效果的物体
- Phong材质 MeshPhongMaterial-符合Phong光照模型的材质，漫反射部分和Lambert光照模型相同，另外考虑了镜面反射的效果，尤其适合对金属、镜面的表现
- 法向材质 MeshNormalMaterial-将材质的颜色设置为其法向量的方向，对于调试非常有用，在调试时，要知道物体的法向量，使用法向材质就很有效

#### 光源

- THREE.AmbientLight	这是一个基础光源，也叫环境光源，该光源的颜色将会叠加到场景现有的颜色上面，无法创建阴影。环境光没有明确的光源位置，在各处形成的亮度也是一致的，通常用来为整个场景指定一个基础亮度

- THREE.PointLight	点光源，从空间的一点向所有方向发射光源。点光源不计光源大小，可以看作是一个点发出的光源，点光源照到不同物体表面的亮度是线性递减的，因此离点光源越远的物体会越暗

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240422111136.png)

- THREE.SpotLight	这种光源有聚光的效果，类似台灯、天花板上的吊灯或者手电筒。聚光灯朝着一个方向投射出类似圆锥形的光线，还可以随着物体移动

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240422111431.png)

- THREE.DirectionalLight	无限光，平行光。从这种光源发出的光线可以看作是平行的，比如太阳光。对于任意平行的平面，平行光照射的亮度都是相同的，而与平面所在的位置无关，对于平行光而言，设置光源位置尤为重要，它决定了光照的方向

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240422111550.png)

- THREE.HemisphereLight	这是一种特殊光源，可以通过模拟反光面和光线微弱的天空来创建更加自然的室外光线。无法创建阴影。
- THREE.AreaLight	使用这种光源可以指定散发光线的平面，而不是一个点。无法创建阴影。
- THREE.LensFlare	这不是一种光源，但是通过使用THREE.LensFlare，可以为场景中的光源添加镜头光晕效果。

#### 阴影

上面零散的说了些可以响应光照产生阴影的材料，可以创建阴影的光源，这里总结一下：

明暗是相对的，阴影的形成是因为比周围获得的光照更少，因此要形成阴影，必须要有光源，能形成阴影的光源有平行光和聚光灯

能表现阴影效果的材质有`LambertMaterial`和`PhongMaterial`

我们需要对产生阴影的物体设置`castShadow`，对接收阴影的物体设置`receiveShadow`



### GLSL基础知识

**1. 着色器结构：** GLSL着色器通常包括一些预处理指令、全局变量、输入输出变量以及至少一个主函数（main）。顶点着色器和片元着色器都需要各自的主函数。

 **2.数据类型：**GLSL包括标准的基本类型（如 `int`, `float`, `double`）和一些特殊的图形类型，如向量和矩阵。

- **向量：** `vec2`, `vec3`, `vec4` 分别表示2、3、4维浮点数向量。
- **矩阵：** `mat2`, `mat3`, `mat4` 分别表示2x2、3x3、4x4的浮点数矩阵。

**3. 变量修饰符：**

- `uniform`: 在一次渲染调用中不变的全局变量，常用于从CPU向GPU传递参数，如变换矩阵、光源信息等。
- `attribute` (只在较旧版本的GLSL中使用，现在用 `in` 替代): 用于顶点着色器中，表示每个顶点的属性，如顶点坐标、纹理坐标。
- `varying` (现在使用 `in` 和 `out` 替代): 用于在顶点着色器和片元着色器之间传递数据。



### 着色器功能和技术

**1. 纹理采样：**
要在片元着色器中使用纹理，需要定义一个 `sampler` 类型的uniform变量来表示纹理，然后使用 `texture()` 函数进行采样。

```GLSL
uniform sampler2D myTexture;
in vec2 TexCoord;

void main() {
    FragColor = texture(myTexture, TexCoord);
}
```

**2. 光照计算：**
在着色器中实现光照通常涉及多个步骤：计算法向量、计算光源方向、计算视线方向，然后根据这些向量计算漫反射和镜面反射强度。

```GLSL
vec3 norm = normalize(Normal);
vec3 lightDir = normalize(lightPos - FragPos);
float diff = max(dot(norm, lightDir), 0.0);
vec3 diffuse = diff * lightColor;

vec3 viewDir = normalize(viewPos - FragPos);
vec3 reflectDir = reflect(-lightDir, norm);
float spec = pow(max(dot(viewDir, reflectDir), 0.0), shininess);
vec3 specular = spec * lightColor;

FragColor = vec4((ambient + diffuse + specular) * objectColor, 1.0);
```



### 疑问解答

**1.如何实现自转和公转？**

自转只需在帧动画这改变自身的rotation即可：

```js
  planet.rotation.y += 0.01;  // 根据需要调整旋转速度
```

公转有两种实现思路，一个是每帧改计算变行星自身的坐标点位，依次实现公转：

```js
let angle = 0;  // 初始角度
const radius = 20;  // 行星距离恒星的距离

function animate() {
  requestAnimationFrame(animate);

  // 更新行星的公转
  angle += 0.01;  // 每帧改变的角度
  planet.position.x = radius * Math.cos(angle);
  planet.position.z = radius * Math.sin(angle);

  renderer.render(scene, camera);
}
```

第二个是把恒星和行星看成一个整体，然后旋转整体：

```js
const star = new THREE.Object3D();
const planet = new THREE.Mesh(new THREE.SphereGeometry(1, 32, 32), new THREE.MeshStandardMaterial({color: 0x00ff00}));
planet.position.x = 20;  // 设置行星距离恒星的距离
star.add(planet);  // 将行星添加为子对象
scene.add(star);  // 将恒星添加到场景中

function animate() {
  requestAnimationFrame(animate);

  // 旋转恒星对象，从而实现行星的公转
  star.rotation.y += 0.01;

  renderer.render(scene, camera);
}

```



### 参考链接

- [GL Shader Language（GLSL）详解-基础语法)](https://zhuanlan.zhihu.com/p/349296191)

