---
title: GPU Pro 系列内容列表
top: false
mathjax: true
date: 2021-03-18 13:45:33
categories:
- 计算机图形学
---

-----





- CRC Press出版社的《GPU Pro 1~7》

- 《GPU Pro》 1~7和《GPU Zen》等8本书的主编都是图形大牛 Wolfgang Engel。这边是Wolfgang Engel的博客主页：[https://www.blogger.com/profile/11031097395025597662](https://link.zhihu.com/?target=https%3A//www.blogger.com/profile/11031097395025597662)
- 以及Wolfgang Engel维护的《GPU Pro》系列书籍的博客地址：[http://gpupro.blogspot.com/](https://link.zhihu.com/?target=http%3A//gpupro.blogspot.com/)
- 还有Wolfgang Engel维护的《GPU Zen》系列书籍的博客地址：[https://gpuzen.blogspot.com/](https://link.zhihu.com/?target=https%3A//gpuzen.blogspot.com/)

- GPU Pro 1 [2010]
- GPU Pro 2 [2011]
- GPU Pro 3 [2012]
- GPU Pro 4 [2013]
- GPU Pro 5 [2014]
- GPU Pro 6 [2015]
- GPU Pro 7 [2016]



# GPU Pro 1



## 4.1 第一部分 渲染技巧（Rendering Techniques）

\1. 结合高度混合的四叉树位移贴图（Quadtree Displacement Mapping with Height Blending）

\2. 基于几何着色器的NPR效果（NPR Effects Using the Geometry Shader）

\3. 作为后处理的alpha混合（Alpha Blending as a Post-Process）

\4. 虚拟纹理映射101（Virtual Texture Mapping 101）

 

## 4.2 第二部分 全局光照（Global Illumination）

\1. 用于间接光照的快速，基于模板的多分辨率泼溅（Fast, Stencil-Based Multiresolution Splatting for Indirect Illumination）

\2. 屏幕空间定向遮蔽（Screen-Space Directional Occlusion）

\3. 使用几何Impostors的实时多级光线追踪（Real-Time Multi-Bounce Ray-Tracing with Geometry Impostors）

 

## 4.3 第三部分 图像空间（Image Space）

\1. GPU上的各项异性的Kuwahara滤波（Anisotropic Kuwahara Filtering on the GPU）

\2. 边缘抗锯齿的后处理（Edge Anti-aliasing by Post-Processing）

\3. 使用Floyd-Steinberg半色调的环境映射（Environment Mapping with Floyd-Steinberg

Halftoning）

\4. 用于粒状遮挡剔除的分层项缓冲（Hierarchical Item Buffers for Granular Occlusion Culling）

\5. 后期制作中的真实感景深（Realistic Depth of Field in Postproduction）

\6. 实时屏幕空间的云层光照（Real-Time Screen Space Cloud Lighting）

\7. 屏幕空间次表面散射（Screen-Space Subsurface Scattering）

 

## 4.4 第四部分 阴影（Shadows）

\1. 快速常规阴影过滤（Fast Conventional Shadow Filtering）

\2. 混合最小/最大基于平面的阴影贴图（Hybrid Min/Max Plane-Based Shadow Maps）

3: 利用四面体映射实现全向光的阴影映射（Shadow Mapping for Omnidirectional Light Using Tetrahedron Mapping）

\4. 屏幕空间软阴影（Screen Space Soft Shadows

 

## 4.5 第五部分 3D引擎设计（3D Engine Design）

\1. 基于桶排序的GPU多片段效果（Multi-Fragment Effects on the GPU Using Bucket Sort）

2.随着cell带宽引擎的并行光预通道渲染（Parallelized Light Pre-Pass Rendering with the Cell Broadband Engine）

\3. 在Direct3D9和OpenGL 2之间移植代码（Porting Code between Direct3D9 and OpenGL 2.0）

\4. DirectX 9实用线程渲染（Practical Thread Rendering for DirectX 9）

 

 

## 4.6 第六部分 游戏解析（Game Postmortems）

\1. Spore中的风格化渲染（Stylized Rendering in Spore）

2: 《狂野西部：生死同盟》中的渲染技巧（Rendering Techniques in Call of Juarez: Bound in Blood ）

\3. 制作大型，漂亮、快速且流畅的游戏：经验教训（Making it Large, Beautiful, Fast, and Consistent: Lessons Learned）

\4. 可破坏的体积地形（Destructible Volumetric Terrain）



# GPU Pro 2

## 5.1 第一部分 几何操作（Geometry Manipulation）

\1. 使用硬件镶嵌的地形和海洋渲染（Terrain and Ocean Rendering with Hardware Tessellation）

\2. 实际且真实的面部皱纹动画（Practical and Realistic Facial Wrinkles Animation）

\3. GPU上的程序内容生成（Procedural Content Generation on the GPU）

 

 

## 5.2 第二部分 渲染（Rendering）

\1. 预集成的皮肤着色（Pre-Integrated Skin Shading）

\2. 使用延迟着色实现毛发（Implementing Fur Using Deferred Shading）

\3. 户外游戏的大规模地形渲染（Large-Scale Terrain Rendering for Outdoor Games）

\4. 实用形态学抗锯齿（Practical Morphological Antialiasing）

\5. 体积贴花（Volume Decals）

 

 

## 5.3 第三部分 全局光照效果（Global Illumination Effects）

\1. 时域屏幕空间环境光遮蔽（Temporal Screen-Space Ambient Occlusion）

\2. 细节层次与流优化的辐照度法线映射（Level-of-Detail and Streaming Optimized Irradiance Normal Mapping）

\3. 使用光线追踪的实时单次弹射间接光照与阴影（Real-Time One-Bounce Indirect Illumination and Shadows using Ray Tracing）

\4. 半透明均匀介质中光传输的实时近似（Real-Time Approximation of Light Transport in Translucent Homogenous Media）

\5. 基于时间相关光传播量的漫反射全局光照（Diffuse Global Illumination with Temporally Coherent Light Propagation Volumes）

 

 

## 5.4 第四部分 Shadows 阴影

\1. 减少方差阴影图光漏的技巧（Variance Shadow Maps Light-Bleeding Reduction Tricks）

\2. 基于自适应阴影贴图的快速软阴影（Fast Soft Shadows via Adaptive Shadow Maps）

\3. 自适应体积阴影贴图（Adaptive Volumetric Shadow Maps）

\4. 具有时间相关性的快速软阴影（Fast Soft Shadows with Temporal Coherence）

\5. Mip贴图屏幕空间软阴影（Mipmapped Screen-Space Soft Shadows）

 

## 5.5 第五部分 手持设备（Handheld Devices）

 

\1. 一个基于Shader的电子书渲染器（A Shader-Based eBook Renderer）

\2. 移动设备上的后处理特效（Post-Processing Effects on Mobile Devices）

\3. 基于shader的水特效（Shader-Based Water Effects）

 

## 5.6 第六部分 3D Engine Design （3D引擎设计）

\1. 对于游戏的实用动态可见性（Practical, Dynamic Visibility for Games）

\2. 使用像素四元消息传递的着色器分摊（Shader Amortization using Pixel Quad Message Passing）

\3. 用于实时群体的渲染流水线（A Rendering Pipeline for Real-Time Crowds）

# GPU Pro 3

## 6.1 第一部分 几何操作（Geometry Manipulation）

\1. 顶点着色器的镶嵌（Vertex Shader Tessellation）

\2. 基于DirectX 11的实时变形地形渲染（Real-Time Deformable Terrain Rendering with DirectX 11）

\3. 优化体育场的人群渲染（Optimized Stadium Crowd Rendering）

\4. 几何抗锯齿方法（Geometric Antialiasing Methods）

 

## 6.2 第二部分 渲染（Rendering）

\1. 基于GPU的实用椭圆纹理滤波（Practical Elliptical Texture Filtering on the GPU）

\2. 对大气散射的Chapman掠入射函数近似（An Approximation to the Chapman Grazing-Incidence Function for Atmospheric Scattering）

\3. 立体实时水与泡沫的渲染（Volumetric Real-Time Water and Foam Rendering）

\4. CryENGINE 3：回顾近三年的工作（CryENGINE 3: Three Years of Work in Review）

\5. 简单对象的廉价抗锯齿（Inexpensive Antialiasing of Simple Objects）

 

## 6.3 第三部分 全局光照效果（Global Illumination Effects）

\1. 使用Oriented Splats网格的光线追踪近似反射（Ray-Traced Approximate Reflections

Using a Grid of Oriented Splats）

\2. 屏幕空间弯曲锥体：一种实用的方法（Screen-Space Bent Cones: A Practical Approach）

\3. 基于体素模型的实时近场全局照明（Real-Time Near-Field Global Illumination Based on a Voxel Model）

 

## 6.4 第四部分 阴影（Shadows）

1.对阴影贴图的高效在线可见性（Efficient Online Visibility for Shadow Maps）

\2. 深度拒绝图案阴影（Depth Rejected Gobo Shadows）

 

## 6.5 第五部分 3D引擎设计（3D Engine Design）

\1. Z3剔除（Z3 Culling）

\2. 基于四元数的渲染流水线（A Quaternion-Based Rendering Pipeline）

\3. 用DirectX 11实现定向自适应边缘AA滤波器（Implementing a Directionally Adaptive

Edge AA Filter Using DirectX 11）

\4. 设计一个数据驱动的渲染器（Designing a Data-Driven Renderer）



# GPU Pro 4

## 7.1 第一部分 几何操作（Geometry Manipulation）

\1. GPU地形细分和镶嵌（GPU Terrain Subdivision and Tessellation）

\2. 对可编程顶点Pulling渲染流水线的简介（Introducing the Programmable Vertex Pulling Rendering Pipeline）

\3. WebGL全局渲染流水线（A WebGL Globe Rendering Pipeline）

 

## 7.2 第二部分 渲染（Rendering）

\1. 使用Cubemaps和图像代理的实用平面反射（Practical Planar Reflections Using Cubemaps and Image Proxies）

\2. 实时Ptex和矢量位移（Real-Time Ptex and Vector Displacement）

\3. 在GPU上解耦延迟着色（Decoupled Deferred Shading on the GPU）

\4. 分块前向着色（Tiled Forward Shading）

\5. 实时渲染向电影式渲染的迈进（Forward+: A Step Toward Film-Style Shading in Real Time）

\6. 渐进屏幕空间多通道表面体素化（Progressive Screen-Space Multichannel Surface Voxelization）

\7. 基于体素的动态全局照明（Rasterized Voxel-Based Dynamic Global Illumination）

 

## 7.3 第三部分 图像空间（Image Space）

\1. 《小龙斯派罗：交换力量》中的景深着色器（The Skylanders SWAP Force Depth-of-Field Shader）

\2. 模拟后处理景深方法中的局部遮蔽（Simulating

Partial Occlusion in Post-Processing Depth-of-Field Methods）

\3. 第二深度抗锯齿（Second-Depth Antialiasing）

\4. 实用的帧缓冲压缩（Practical Framebuffer Compression）

\5. 一致性 - 增强GPU上的过滤效果（Coherence-Enhancing Filtering on

the GPU）

 

## 7.4 第四部分 阴影（Shadows）

\1. 实时深度阴影贴图（Real-Time Deep Shadow Maps）

 

## 7.5 第五部分 游戏引擎设计（Game Engine Design）

 

\1. 基于方向的引擎架构（An Aspect-Based Engine Architecture）

\2. 使用Direct3D 11进行Kinect编程（Kinect Programming with Direct3D 11）

\3. 一个对Authored Structural Damage的管线（A Pipeline for Authored Structural

Damage）



# GPU Pro 5

## 8.1 第一部分 渲染（Rendering）

\1. 对单通道A缓冲的每像素列表（Per-Pixel Lists for Single Pass A-Buffer）

\2. 使用双通道颜色编码减少纹理内存使用（Reducing Texture Memory Usage by 2-Channel Color Encoding）

\3. 基于粒子的老化材质模拟（Particle-Based Simulation of Material Aging）

\4. 简单的基于光栅化的液体（Simple Rasterization-Based Liquids）

 

## 8.2 第二部分 光照与着色（Lighting and Shading）

\1. 基于物理的区域光照（Physically Based Area Lights）

\2. 利用极线采样的高性能室外光照散射（High Performance Outdoor Light Scattering Using Epipolar Sampling）

3.《杀戮地带》中的体积光效果：Shadow Fall（Volumetric Light Effects in Killzone: Shadow Fall）

\4. 层次-Z 屏幕空间锥追踪反射（Hi-Z Screen-Space Cone-Traced Reflections）

\5. TressFX：先进的实时毛发渲染（TressFX: Advanced Real-Time Hair Rendering）

\6. 线的抗锯齿（Wire Antialiasing）

 

## 8.3 第三部分 图像空间（Image Space）

\1. 屏幕空间的草地（Screen-Space Grass）

\2. 基于每像素链表构造实体几何的屏幕空间可变形网格（Screen-Space Deformable Meshes via CSG with Per-Pixel Linked Lists）

\3. SPU上的背景虚化效果（Bokeh Effects on the SPU）

 

## 8.4 第四部分 移动设备（Mobile Devices）

\1. 手机上的真实感实时皮肤渲染（Realistic Real-Time Skin Rendering on Mobile）

\2. 移动设备上的延迟渲染技术（Deferred Rendering Techniques on Mobile Devices）

\3. 使用ARM® Mali™ GPUs的带宽高效图形渲染（Bandwidth Efficient Graphics with ARM® Mali™ GPUs）

\4. 使用OpenGL ES 3.0的高效目标变形动画（Efficient Morph Target Animation Using OpenGL ES 3.0）

\5. 分块延迟模糊（Tiled Deferred Blending）

\6. 自适应可伸缩纹理压缩（Adaptive Scalable Texture Compression）

\7. 针对ARM® Mali™-T600 GPU的OpenCL内核优化（Optimizing OpenCL Kernels for the ARM® Mali™-T600 GPUs）

 

## 8.5 第五部分 3D引擎设计（3D Engine Design）

\1. 重新认识四元数（Quaternions Revisited）

\2. glTF : 设计一个开放标准的运行时资源格式（glTF: Designing an Open-Standard Runtime Asset Format）

\3. 管理Hierarchy中的变换（Managing Transformations in Hierarchy）

 

## 8.6 第六部分 计算（Compute）

\1. TressFX中的头发模拟（Hair Simulation in TressFX）

\2. 对全动态场景的对象次序光线追踪（Object-Order Ray Tracing for Fully Dynamic Scenes）

\3. GPU上的四叉树（Quadtrees on the GPU）

#  GPU Pro 6

## 9.1 第一部分 几何操作（Geometry Manipulation）

\1. 动态GPU地形（Dynamic GPU Terrain）

\2. 在GPU上通过镶嵌的带宽高效程序化网格（Bandwidth-Efficient Procedural Meshes in the GPU via Tessellation）

\3. 物体碰撞时细分表面的实时形变（Real-Time Deformation of Subdivision Surfaces on Object Collisions）

\4. 游戏中的逼真体积爆炸（Realistic Volumetric Explosions in Games）

 

## 9.2 第二部分 渲染（Rendering）

\1. 《神偷》中的下一代渲染技术（Next-Generation Rendering in Thief）

\2. 草地渲染和使用LOD的模拟（Grass Rendering and Simulation with LOD）

\3. 混合重建抗锯齿（Hybrid Reconstruction Antialiasing）

\4. 使用预计算散射的基于物理的云层实时渲染（Real-Time Rendering of Physically Based Clouds Using Precomputed Scattering）

\5. 稀疏程序化体渲染（Sparse Procedural Volume Rendering）

 

## 9.3 第三部分 光照（Lighting）

\1. 使用光照链表的实时光照（Real-Time Lighting via Light Linked List）

\2. 延迟归一化的辐照度探针（Deferred Normalized Irradiance Probes）

\3. 体积雾与光照（Volumetric Fog and Lighting）

\4. GPU上基于物理的光照探针（Physically Based Light Probe Generation on GPU）

\5. 使用薄片的实时全局光照（Real-Time Global Illumination Using Slices）

 

 

## 9.4 第四部分 阴影（Shadows）

\1. 实用屏幕空间软阴影（Practical Screen-Space Soft Shadows）

\2. 基于分块的全方位阴影（Tile-Based Omnidirectional Shadows）

\3. 阴影贴图轮廓的重新矢量化（Shadow Map Silhouette Revectorization）

 

 

## 9.5 第五部分 移动设备（Mobile Devices）

\1. PowerVR GPU上的混合光线追踪（Hybrid Ray Tracing on a PowerVR GPU）

\2. 实现一个仅有GPU的粒子碰撞系统，使用自适应可伸缩纹理压缩3D纹理和OpenGL ES 3.0（Implementing a GPU-Only Particle-Collision System with ASTC 3D Textures and OpenGL ES 3.0）

\3. 针对移动设备的动画角色毛皮（Animated Characters with Shell Fur for Mobile Devices）

\4. 移动GPU的高动态范围计算摄影（High Dynamic Range Computational Photography on Mobile GPUs）

 

## 9.6 第六部分 计算（Compute）

\1. 基于计算的分块剔除（Compute-Based Tiled Culling）

\2. 在GPU光线追踪器上渲染矢量位移映射表面（Rendering Vector Displacement-Mapped Surfaces in a GPU Ray Tracer）

\3. 对体渲染的平滑概率环境光遮蔽（Smooth

Probabilistic Ambient Occlusion for Volume Rendering）

 

## 9.7 第七部分 3D引擎设计（3D Engine Design）

1. 用于快速光线投射操作的分块线性二元方格（Block-Wise Linear Binary Grids for Fast Ray-Casting Operations）

2. 采用Shader Shaker基的于语义的着色器生成（Semantic-Based Shader Generation Using Shader Shaker）

3. ANGL: 将OpenGL ES引入桌面端（ANGLE: Bringing OpenGL ES to the Desktop）

# GPU Pro 7

## 10.1 第一部分 几何操作（Geometry Manipulation）

1.《古墓丽影：崛起》中的延迟雪地形变（Deferred Snow Deformation in Rise of the Tomb Raider）

\2. Catmull Clark细分曲面（Catmull-Clark Subdivision Surfaces）

 

## 10.2 第二部分 光照（Lighting）

\1. 集群着色：使用DirectX12中的保守光栅化指定光照（Clustered Shading: Assigning Lights Using Conservative Rasterization in DirectX 12）

\2. 精细删减的分块光照列表（Fine Pruned Tiled Light Lists）

\3. 延迟属性插值着色（Deferred Attribute Interpolation Shading）

\4. 实时的体积云朵景观（Real-Time Volumetric Cloudscapes）

 

## 10.3 第三部分 渲染（Rendering）

\1. 自适应虚拟纹理（Adaptive Virtual Textures）

\2. 延迟粗像素着色（Deferred Coarse Pixel Shading）

\3. 使用多帧采样进行渲染（Progressive Rendering Using Multi-frame Sampling）

 

 

## 10.4 第四部分 移动设备（Mobile Devices）

\1. 基于静态局部立方体贴图的高效软阴影（Efficient Soft Shadows Based on Static Local Cubemap）

\2. 移动平台上基于物理的延迟着色（Physically Based Deferred Shading on Mobile）

# GPU Zen

## 11.1 第一部分 几何操作（Geometry Manipulation）

\1. 属性顶点云层（Attributed Vertex Clouds）

\2. 使用内保守光栅化渲染凸面遮挡物（Rendering Convex Occluders with Inner Conservative Rasterization）

 

## 11.2 第二部分 光照（Lighting）

\1. 稳定的间接光照（Stable Indirect Illumination）

\2. 使用膨化体积光的参与性介质（Participating Media Using Extruded Light Volumes）

 

## 11.3 第三部分 渲染（Rendering）

\1. Deferred+ : 针对Dawn引擎的下一代剔除和渲染（Deferred+: Next-Gen Culling and Rendering for the Dawn Engine）

\2. 使用保守光栅化的可编程每像素采样放置（Programmable Per-pixel Sample Placement with Conservative Rasterizer）

\3. 手机卡通渲染（Mobile Toon Shading）

\4. 高质量高效GPU图像细节处理（High Quality GPU-efficient Image Detail Manipulation）

\5. 使用线性变换余弦的线性光着色（Linear-Light Shading with Linearly Transformed Cosines）

 

## 11.4 第四部分 屏幕空间（Screen Space）

1.可扩展的自适应屏幕空间环境光遮蔽（Scalable Adaptive SSAO）

2.在PS4上达到1ms 1080p的鲁棒性屏幕空间环境光遮蔽（Robust Screen Space Ambient Occlusion in 1 ms in 1080p on PS4）

3.基于实际采集的散景（Practical Gather-based Bokeh）



## 相关参考

[【GPU精粹与Shader编程】(一) 开篇 & 全系列11本书核心知识点总览](https://qianmo.blog.csdn.net/article/details/79689041)