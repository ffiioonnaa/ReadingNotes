* physically-based computer vision : color  ,reflectance

​        photometric stereo ,shape-from-shading

* geometric vision : camera models , epipolar geometry(对极几何，在两个相机位置产生的两幅图像之间存在一种特殊几何关系，是sfm问题中2D-2D求解两帧间相机姿态的基本模型)

​        structure-from-motion , stereo, multi-view stereo（用于稠密重建）

* image/video processing,  filtering,  corner&edge detection, optical flow,  tracking, segmentation 

* image understanding: classification, detection, semantic segmentation

* statistics and psychophysics: robust estimation, saliency(人会关注图像上的区域)

* deep learning

color matching function:$$r_{(\lambda)}$$ ,   $$g_{(\lambda)}$$ ,    $$ b_{(\lambda)}$$ 

> 人眼对颜色的感知是线性的，RGB是三原色作加法（投一束某颜色的光），CMY是减法（让一束白光透过一张滤光片）

CIE XYZ color space

> the original color matching functions have negative values（红色部分），为了让所有坐标为正，以及为了让Y轴与人眼对明亮感知特点相匹配，变换坐标系得到XYZ标准颜色空间。
>
> 在XYZ标准空间选择三个顶点来作为三原色，通过线性组合得到三角形内的所有颜色。因此三原色并不能还原出现实世界所有的颜色。

<figure class=''half''>
    <img src="/home/zhao/文档/typora/computer vision.assets/CIE1931_rgxy.png" alt="查看源图像" style="zoom: 25%;" />
    <img src="/home/zhao/文档/typora/computer vision.assets/CIExy1931_Rec_2020_and_Rec_709.png" alt="查看源图像" style="zoom: 33%;" />
</figure>    

YUV color space

> 由RGB线性变换而来。
>
> Y 描述 luminance，UV描述 chrominance components。
>
> 用于video compression standards,因为人眼对于Y通道敏感，而对UV通道不敏感，因此可以通过压缩UV来压缩视频同时保证视觉上不模糊。



capture color images

* 用color splitting prism（棱镜）将光分成三原色，用能感知某种原色的sensor来捕获，因此每个像素需要三套sensor等元器件——3-CCD camera

* Bayer patter

  每个像素只有一种原色的sensor,另外两种颜色的像素值靠差值得到。

  模仿人眼，绿色像素比红色像素多。



### Radiometric Calibration & HDR

* radiance （功率/平方米/空间角）

* irradiance（功率/平方米）



ISP：image signal processing (不同厂家处理不同)

* auto white balance：由于点光源不一定是白光，相机捕获到的图像可能是偏某种色调;

  ​                                            人眼会自动的校正;

  ​                                            相机校正的方式是用RGB矩阵乘一个对角阵来实现













