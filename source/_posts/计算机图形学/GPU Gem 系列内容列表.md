---
title: GPU Gem系列内容列表
top: false
mathjax: true
date: 2021-03-18 13:45:33
categories:
- 计算机图形学
---

-----



# GPU Gems 系列

- GPU Gems 1 [2004]
- GPU Gems 2 [2005]
- GPU Gems 3 [2006]
- Addison-Wesley出版社的《GPU Gems 1~3》

- 《GPU Gem》1~3 的英文原文web版，已经由NVIDIA开源，链接在这里：[https://developer.nvidia.com/gpugems/GPUGems/gpugems_pref01.html](https://link.zhihu.com/?target=https%3A//developer.nvidia.com/gpugems/GPUGems/gpugems_pref01.html)



# GPU Gems 1

## 1.1 第一部分 自然效果的渲染（Natural Effects）

第1章 用物理模型进行高效的水模拟（Effective Water Simulation from Physical Models）

第2章 水焦散的渲染（Rendering Water Caustics）

第3章 Dawn Demo中的皮肤（Skin in the "Dawn" Demo）

第4章 Dawn Demo中的动画（Animation in the "Dawn" Demo）

第5章 改良的Perlin噪声实现（Implementing Improved Perlin Noise）

第6章 Vulcan Demo中的火焰渲染（Fire in the "Vulcan" Demo）

第7章 无尽波动草叶的渲染（Rendering Countless Blades of Waving Grass）

第8章 衍射的模拟（Simulating Diffraction）

 

## 1.2 第二部分 光照和阴影（Lighting and Shadows）

第9章 有效的阴影体渲染（Efficient Shadow Volume Rendering）

第10章 电影级光照（Cinematic Lighting）

第11章 阴影贴图抗锯齿（Shadow Map Antialiasing）

第12章 全方位阴影映射（Omnidirectional Shadow Mapping）

第13章 使用遮挡区间映射产生模糊的阴影（Generating Soft Shadows Using Occlusion Interval Maps）

第14章 透视阴影贴图（Perspective Shadow Maps: Care and Feeding）

第15章 逐像素光照的可见性管理（Managing Visibility for Per-Pixel Lighting）

 

## 1.3 第三部分 材质（Materials）

第16章 次表面散射的实时近似（Real-Time Approximations to Subsurface Scattering）

第17章 环境光遮蔽（Ambient Occlusion）

第18章 空间BRDF（Spatial BRDFs）

第19章 基于图像的光照（Image-Based Lighting）

第20章 纹理爆炸（Texture Bombing）

 

## 1.4 第四部分 图像处理（Image Processing）

第21章 实时辉光（Real-Time Glow）

第22章 颜色控制（Color Controls）

第23章 景深 （Depth of Field）

第24章 高品质的图像滤波（High-Quality Filtering）

第25章 用纹理贴图进行快速滤波宽度的计算（Fast Filter-Width Estimates with Texture Maps）

第26章 OpenEXR图像文件格式（The OpenEXR Image File Format）

第27章 图像处理的框架（A Framework for Image Processing）****



# GPU Gems 2

## 2.1 第一部分 几何复杂性（Geometric Complexity）

 

第1章　实现照片级真实感的虚拟植物（Toward Photorealism in Virtual Botany）

第2章　使用基于GPU几何体剪切图的地形渲染（Terrain Rendering Using GPU-Based Geometry Clipmaps）

第3章　几何体实例化的内幕（Inside Geometry Instancing）

第4章 分段缓冲（Segment Buffering）

第5章　用多流优化资源管理（Optimizing Resource Management with Multistreaming）

第6章　让硬件遮挡查询发挥作用（Hardware Occlusion Queries Made Useful）

第7章　带有位移映射的细分表面自适应镶嵌（Adaptive Tessellation of Subdivision Surfaces with Displacement Mapping）

第8章　使用距离函数的逐像素位移（Per-Pixel Displacement Mapping with Distance Functions）

 

## 2.2 第二部分 着色、光照和阴影（Shading, Lighting, and Shadows）

第9章　S.T.A.L.K.E.R.中的延迟着色（Deferred Shading in S.T.A.L.K.E.R.）

第10章　动态辐照度环境映射实时计算（Real-Time Computation of Dynamic Irradiance Environment Maps）

第11章　近似的双向纹理函数（Approximate Bidirectional Texture Functions）

第12章　基于贴面的纹理映射（Tile-Based Texture Mapping）

第13章　在GPU上实现mental images的phenomena渲染器（Implementing the mental images Phenomena Renderer on the GPU）

第14章　动态环境光遮蔽与间接光照（Dynamic Ambient Occlusion and Indirect Lighting）

第15章　蓝图渲染和草图绘制（Blueprint Rendering and "Sketchy Drawings"）

第16章　精确的大气散射（Accurate Atmospheric Scattering）

第17章　利用像素着色器分支的高效模糊边缘阴影（Efficient Soft-Edged Shadows Using Pixel Shader Branching）

第18章 将顶点纹理位移用于水的真实感渲染（Using Vertex Texture Displacement for Realistic Water Rendering）

第19章 通用的折射模拟（Generic Refraction）

 

## 3.3 第三部分 高质量渲染（High-Quality Rendering）

第20章 快速三阶纹理过滤（Fast Third-Order Texture Filtering）

第21章 高质量反走样的光栅化（High-Quality Antialiased Rasterization）

第22章 快速预过滤线条（Fast Prefiltered Lines）

第23章 Nalu Demo中的头发动画和渲染（Hair Animation and Rendering in the Nalu Demo）

第24章 使用查找表加速颜色变换（Using Lookup Tables to Accelerate Color Transformations）

第25章 Apple Motion中的GPU图像处理（GPU Image Processing in Apple's Motion）

第26章 改进的Perlin噪声（Implementing Improved Perlin Noise）

第27章　高级的高质量过滤（Advanced High-Quality Filtering）

第28章 Mipmap级的测量（Mipmap-Level Measurement）

 

## 3.4 第四部分 GPU的通用计算：初级读本（General-Purpose Computation on GPUS: A Primer）

 

第29章　流式体系结构和技术趋势（Streaming Architectures and Technology Trends）

第30章　Geforce 6系列GPU的体系结构（The GeForce 6 Series GPU Architecture）

第31章　把计算概念映射到GPU（Mapping Computational Concepts to GPUs）

第32章　尝试GPU计算（Taking the Plunge into GPU Computing）

第33章　在GPU上实现高效的并行数据结构（Implementing Efficient Parallel Data Structures on GPUs）

第34章　GPU流程控制习惯用法（GPU Flow-Control Idioms）

第35章　GPU程序优化（GPU Program Optimization）

第36章　用于GPGPU应用程序的流式缩减操作（Stream Reduction Operations for GPGPU Applications）

 

## 3.5 第五部分 面向图像的计算（Image-Oriented Computing）

第37章　GPU上的八叉树纹理（Octree Textures on the GPU）

第38章　使用光栅化的高质量全局照明渲染（High-Quality Global Illumination Rendering Using Rasterization）

第39章　使用逐步求精辐射度方法的全局照明（Global Illumination Using Progressive Refinement Radiosity）

第40章　GPU上的计算机视觉（Computer Vision on the GPU）

第41章　延迟过滤：困难数据格式的渲染（Deferred Filtering: Rendering from Difficult Data Formats）

第42章　保守光栅化（Conservative Rasterization）

# GPU Gems 3

## 3.1 第一部分 几何体（Geometry）

第1章 使用GPU 生成复杂的程序化地形（Generating Complex Procedural Terrains Using the GPU）

第2章 群体动画渲染（Animated Crowd Rendering）

第3章 DirectX 10 混合形状：打破限制（DirectX 10 Blend Shapes: Breaking the Limits）

第4章 下一代SpeedTree 渲染（Next-Generation SpeedTree Rendering）

第5章 普遍自适应的网格优化（Generic Adaptive Mesh Refinement）

第6章 GPU 生成的树的程序式风动画（GPU-Generated Procedural Wind Animations for Trees）

第7章 GPU 上基于点的变形球可视化（Point-Based Visualization of Metaballs on a GPU）

第8章 区域求和的差值阴影贴图（Summed-Area Variance Shadow Maps）

 

## 3.2 第二部分 光照和阴影（Light and Shadows）

第9章 使用全局照明实现互动的电影级重光照（Interactive Cinematic Relighting with Global Illumination）

第10章 在可编程GPU 中实现并行分割的阴影贴图（Parallel-Split Shadow Maps on Programmable GPUs）

第11章 使用层次化的遮挡剔除和几何着色器得到高效鲁棒的阴影体（Efficient and Robust Shadow Volumes Using Hierarchical Occlusion Culling and Geometry Shaders）

第12章 高质量的环境光遮蔽（High-Quality Ambient Occlusion）

第13章 作为后处理的体积光照散射（Volumetric Light Scattering as a Post-Process）

 

## 3.3 第三部分 渲染（Rendering）

第14章 用于真实感实时皮肤渲染的高级技术（Advanced Techniques for Realistic Real-Time Skin Rendering）

第15章 可播放的全方位捕捉（Playable Universal Capture）

第16章 Crysis 中植被的程序化动画和着色（Vegetation Procedural Animation and Shading in Crysis）

第17章 鲁棒的多镜面反射和折射（Robust Multiple Specular Reflections and Refractions）

第18章 用于浮雕映射的松散式锥形步进（Relaxed Cone Stepping for Relief Mapping）

第19章 Tabula Rasa 中的延迟着色（Deferred Shading in Tabula Rasa）

第20章 基于GPU的重要性采样（GPU-Based Importance Sampling）

 

 

## 3.4 第四部分 图像效果（Image Effects）

第21章 真正的Impostor（True Impostors）

第22章 在GPU上烘焙法线贴图（Baking Normal Maps on the GPU）

第23章 高速的离屏粒子（High-Speed, Off-Screen Particles）

第24章 保持线性的重要性（The Importance of Being Linear）

第25章 在GPU 上渲染矢量图（Rendering Vector Art on the GPU）

第26章 通过颜色进行对象探测：使用GPU 进行实时视频图像处理（Object Detection by Color: Using the GPU for Real-Time Video Image Processing）

第27章 作为后处理效果的运动模糊（Motion Blur as a Post-Processing Effect）

第28章 实用景深后期处理（Practical Post-Process Depth of Field）







## 相关参考

[【GPU精粹与Shader编程】(一) 开篇 & 全系列11本书核心知识点总览](https://qianmo.blog.csdn.net/article/details/79689041)

