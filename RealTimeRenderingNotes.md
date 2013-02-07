# Real time rendering 读书笔记

书的网站：www.realtimerendering.com是学习图形学的参考

***

# Chapter 2 The Graphics Rendering Pipeline

本章的主要内容是关于graphics rendering pipeline. 该pipeline的作用如下：
>*给出一个虚拟的摄像机，三维物体、光源、着色函数、纹理后，产生（render）一个二维的图像*

real-time rendering 的pipeline可以粗略划分为三个阶段

* Application级，由CPU完成，运算逻辑比较复杂，
* Geometry级，在GPU上执行。GPU上既有很多programmable cores，同时又有很多固定操作的硬件
* Rasterizer，利用前一级的结果在GPU上进行
have something changed
其中每一个可能又是一条流水线。 容易理解的是，流水线中最慢的一级决定了render的速度。这个速度可以被表示为FPS或Hz。 例如
> *流水线中最慢的一级须要20ms才能完成，那么render speed就是$$\frac{1}{0.020}=50Hz]$$*

***

## 2.2 Application stage
Application Stage需要得到一堆几何图形送到几何级，通常跑在CPU上，例如著名的**collision detection**。
***

## 2.3Geometry Stage
几何级负责单个多边形和单个顶点的操作。这个级又可以继续划分： 模型和视点变换，顶点着色，投影， 剪切和屏幕映射等。
###2.3.1 Model and View Transform
每一个物体本身有一个model coordinates，在model transform之后，该物体将会变换到World Coordinates中，这样可以保证所有的物体在同一个空间中，只需要进行一定的变换，而不需要对其进行分割等其他操作。
其实World Coordinates是用来计算Camera的，因为只有在Camera视野中的物体才会被Render，而Camera在World Space中有个坐标和方向，为了表示哪里Camera能看到，哪里看不到，所以又会将所有模型进行View Transform。

View Transform是将Camera所看的方向定为Z轴的负方向
###2.3.2 Vertex Shading
决定光线对特定材料进行的作用叫做Shading。将所有参与Shading的物体、光源、camera都转换到一个space中（world space）将比较有利于Shading的计算。而这些计算所需要的材料信息（例如顶点坐标、法向量、颜色和其他任何数值）将储存在每一个顶点上。顶点着色的结果（可能包含颜色、向量、纹理坐标信息等）将送到rasterization stage中

Shading一般在World Coordinates中进行
###2.3.3 Projection
两种投影手段： Orthographic、Perspective
###2.3.4 Clipping
只有一部分需要被传进rasterizer stage的物体，需要进行clipping。例如，一条线有一个顶点在里面，一个在外面，那么该线在外面的顶点，就会被该线和屏幕边缘的交点所取代。Clipping Stage通常是由专用的硬件进行操作的。
###2.3.5 Screen Mapping
只有经过了clipped的primitives进入screen map stage。 物体的x和y坐标需要符合screen coordinates。 Screen coordinate和Z坐标一起构成了Window Coordinates。

注意，pixel[0,9]实际上cover了[-0.5,9.5)的Window Coordinate, 不过后来的OpenGL和DirectX10都使用了另一种mapping的方法，即pixel[0,9]cover了[0.0,10.0)

***

## 2.4 Rasterizer Stage
得到了变换和投影之后的顶点，以及相应的着色信息之后，rasterizer stage的目标是计算屏幕上每一个像素应该着什么颜色。Rasterizer Stage由以下步骤构成
###2.4.1 Triangle Setup
计算三角形表面，插值等，由固定硬件进行操作
###2.4.2 Triangle Traversal
检查每一个pixel的中心，看被哪一个三角形包括。每一个三角形的属性通过数据插值的方式得到
###2.4.3 Pixel Shading
由Programmable GPU Core进行计算。使用插值shading数据作为输入，结果是一个或者多个颜色值。一个非常重要的是texturing的technique。
###2.4.4 Merging
每一个像素的信息存在了color buffer中，在merge stage中可以结合shading stage中产生的颜色碎片，并进行可见面辨别。Z-buffer和color buffer一样大
##2.5 Through the Pipeline
Follow a model of a CAD application

***

#  Chapter 3 The Graphics Processing Unit

NVIDIA defines the GPU. 
为了效率，pipeline中的一部分还是只能进行配置，不能进行编程，但之后的大趋势是programmable

Vertex Shader=>Geometry Shader=>Clipping=>Screen Mapping=>Triangle Setup=>Triangle Traversal=>Pixel Shader=>Merger

Vertex Shader是完全的Programmable，对应于chapter2中的Model and View Transform、Vertex Shading和Projection三个阶段。
Geometry Shader也是fully programmable的, 操作primitive的vertices。 Clipping\ screen mapping\triangle setup和triangle traversal都是固定的，而pixel shader是完全programmable的，能够进行pixel shading的计算。

##3.2 The Programmable Shader Stage
现代的Shader使用了一种common shader core，可以进行vertex,pixel和geometry三种shader的运算。

Shader的编程使用了一种类似C的编程语言：shading language HLSL、CG、GLSL

##3.4 The Vertex Shader

顾名思义，只针对vertices. vertex shader提供了修改、创建或者忽略一些多边形顶点的操作。
##3.5 Geometric Shader
input：物体和物体的顶点
output：产生0个或更多的物体
例如，mesh可以作为input，他们的中心可以作为output的points。三角形的法向量可能被计算出来，并加入output的data中，诸如此类。
###3.5.1 stream output
out put in a stream,可以随时读取

##3.6 The Pixel Shader
已经准备好了rasterization的数据。
每一个triangle都会被traverse，并且每一个三角形都会根据vertex的数据进行interpolate，

##3.7 Merging Stage

##3.8 Effects

***

#4. Chapter4 Transforms

##4.1 Translation


2

2

22

2

2

2

2

2

2

2

2

2
















