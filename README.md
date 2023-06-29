![](https://camo.githubusercontent.com/6fd93f55e76b0fdff8c7f0f4144442be54981320001398ff34e31c5f7cac812d/68747470733a2f2f692e696d6775722e636f6d2f783872744772342e676966)

# 3D游戏着色器

你是否对添加纹理，光照，阴影，法线贴图，发光物体，环境光吸收，反射，折射，以及更多到你的3D游戏中感兴趣？很好！下面是一系列着色技术，它会让你的游戏视觉部分到一个新的高度。我有解释每一个技术，你能拿着在这里学的去应用到任何你使用的技术栈中，它可能是Godot，Unity，Unreal，或其他的。对于这个着色器之间的胶水，我有选择了最好的Panda3D游戏引擎和OpenGL着色语言（GLSL）。这些如果在你的技术栈，那么你会特别地受益于如何去使用这些着色技术用Panda3D和OpenGL。

## 安装

## 编译示例

## 运行示例

## 参考坐标系

在你编写任何着色器之前，你将熟悉下面一些参考坐标系。浓缩起来就是原点`(0,0,0)` 作为当前坐标系相对于什么？一旦你知道了这些，你就能通过矩阵变换它们到其他向量空间。通常，在一些着色器输出看起来错误的时候，是因为坐标系混淆了。

### 模型

![](https://camo.githubusercontent.com/1e1b033aea5324e796a57335a8425c8267d3383e2e1e35cf4f47b1f6b5179b46/68747470733a2f2f692e696d6775722e636f6d2f38787074616a552e676966)

模型坐标系是相对于模型原点，这个原点通常是模型的几何中心。

### 世界

![](https://camo.githubusercontent.com/5175787a6938a41a3cacf3ab21db9e07d0b010254d2caa5dc3e83c2fe982f526/68747470733a2f2f692e696d6775722e636f6d2f66486c346f68582e676966)

世界坐标系是相对于你所创建的场景的原点。

### 视图

![](https://camo.githubusercontent.com/62174eb8cc620450d60629b60dde893a80b87fb8a9fe68447cf68544577f7156/68747470733a2f2f692e696d6775722e636f6d2f336234534747482e676966)

视图坐标系是相对于活动相机的位置。

### 裁切

![](https://camo.githubusercontent.com/bdae8baedba87cd2d364d2f567061183a13aa6dd6f6079b45f8def9dcc82d654/68747470733a2f2f692e696d6775722e636f6d2f695345575339592e706e67)

裁切坐标系是相对于相机底片的中心，所有的坐标都是齐次的，变化在`-1`到`1`之间。`X`和`Y`与相机底片是平行的，`Z`是深度。

![](https://camo.githubusercontent.com/08d213060e8777eeda21e62925d10e62c4f4b38dde702b001879522bd66ea49a/68747470733a2f2f692e696d6775722e636f6d2f4d68676d4f4c762e676966)

任何不在相机截头锥体范围内的顶点会被剪切或丢弃。你能看到这些发生在立方体靠后时被相机的远平面剪切，或立方体靠侧面。

### 屏幕

![](https://camo.githubusercontent.com/baeb173656a1793e88de4e55ae9f9d9b5b28f621295b116071f8dcfcdcbd4fcc/68747470733a2f2f692e696d6775722e636f6d2f624848726a4f6c2e706e67)

屏幕坐标系（通常）是相对于屏幕的左下角，`X`从`0`到屏幕的宽，`Y`从`0`到屏幕的高。

## GLSL

![](https://camo.githubusercontent.com/0d168e01e0ee292a74621987983f0d363fff826539d2521fec3bfb3e367a4d37/68747470733a2f2f692e696d6775722e636f6d2f3762354d4342472e676966)

替代使用固定功能管线，你将使用可编程GPU渲染管线。因为它是可编程的，你能以着色器的形式去提供程序。

```glsl
#version 150

void main() {}
```

这是一个基本的GLSL着色器，由GLSL版本号和`main`函数组成。

```glsl
#version 150
uniform mat4 p3d_ModelViewProjectionMatrix;
in vec4 p3d_Vertex;

void main()
{
    gl_Position = p3d_ModelViewProjectionMatrix * p3d_Vertex;
}
```

这里是一个简化的GLSL顶点着色器，其变换传入的一个顶点到剪切空间并输出新顶点的齐次位置。`main`函数不会返回任何值，因为它是`void`类型并且`gl_Position`变量是内置的输出。



注意关键字`uniform`与`in`。这`uniform`关键字代表这个全局变量对与所有顶点都是相同的。Panda3D为你给`p3d_modelViewProjectionMatrix`赋值，其对于每个顶点都是相同的矩阵，这`in`关键字代表这个全局变量正在被提供到着色器。顶点着色器接受被其依附的几何体中的每个顶点。

```glsl
#version 150
out vec4 fragColor;
void main() 
{
    fragColor = vec4(0,1,0,1);    
}
```

这是一个基本的GLSL片段着色器，其输出片段颜色为纯绿色。记住一个片段影响最多一个屏幕像素，单个像素能够被多个片段影响。

注意`out`关键字，这`out`关键字代表这个全局变量正在被着色器计算并赋值。变量名`fragColor`是任意的，所以自由选择不同的名字。

![](https://camo.githubusercontent.com/44ac0f1acd93f9bff9619836c9d6a87866a98506e6d8237168304e09da5edde1/68747470733a2f2f692e696d6775722e636f6d2f563235557a4d612e676966)

这上面是两个着色器显示的输出。

## 渲染到纹理
