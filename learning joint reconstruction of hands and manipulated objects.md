# learning joint reconstruction of hands and manipulated objects

## hand analysis in images/videos/RGB-D
* hand estimation and tracking using <font color="#dd0000">articulated models / statistical shape models</font>
* hand pose ( sparse keypoint ) estimation from <font color="#dd0000">convolutional neural network</font> 
* hand mesh <font color="#dd0000">fitting</font> / tracking given <font color="#dd0000">a good initialization</font>

## MANO
formulate a model of hands and bodies interacting together & fit it to full-body 4D sequences

1000 高分辨率 3D scan -> parametric hand model
16 个 joint

## AMASS

方法：MoSh++

用SMPL来统一15种不同optical marker-based mocap datasets

包含soft-tissue dynamics 和 realistic hand motion

<font color="#dd0000">看视频感觉手部并没有动作，都是半握拳</font>

## ObMan

> 文章总结了现有的 | hand-object数据集
>                                    | synthetic 数据集

* Objects:ShapeNet, 8 类，2772 mesh

* GraspIt software 

* render full body：transfer pose of hand to hand of SMPL+H, body pose 从CMU MoCap中采样，body shape 从CAESAR中采样，global rotation从$SO(3)$中采样

* texture：object texture从ShapeNet中采样，body texture从 body scan in SURREAL中获得                    hand texture ：在手部区域大部分scan丢失了color信息，因此结合面部的色调

* render ：图像来自于LSUN & ImageNet datasets. 渲染工具为Blender 

## Hand-object reconstruction

<img src="image/微信图片_20191120172948.png" style="zoom: 67%;" />

network architecture has two branches：
* 预测object shape (normalized coordinate space)
* 预测hand mesh & trans scale <font color="#dd0000"> (用于把object变换到hand-relative coordinate system)</font>

### 1. Differentiable  hand model

* ResNet18 encoder (在ImageNet上预训练) 输出特征向量 $\Phi_{Hand}$ ——> fully connected network regresses $\theta$ 和 $\beta$
* $J$：16+5(手指头位置)
* $L_{Hand}=L_{V_{Hand}}+L_{J}+L_{\beta}$  ,   $L_{\beta}=\rVert\beta\rVert^2$

前两个loss为预测值和ground truth之间的$L2$distance

#### Regularization on hand shape

* regularization on shape 使hand model趋向于average shape in MANO training set（$\beta=\vec{0} \in R^{10}$）

### 2. Object mesh estimation

* ResNet18 encoder 输出特征向量$\Phi_{Obj}$ + 点云(从球体/一组正方形中采点)——>AtlasNet——>fully connected network regresses new coordinates on object surface

* 本文用正多面体（icosphere）细分（subdivision）3级，642个点。

* $L^n_{Object}=L^n_{V_{Obj}}+\mu_LL_L+\mu_EL_E$

  1. 预测点与在ground truth外表面随机采样的点的loss：<font color="#dd0000">symmetric Chamfer loss</font>   

  2. Training the objects in<font color="#dd0000"> <font color="#00dd">n</font>ormalized coordinates</font>：ground truth 被放缩平移内接到一个固定半径的球内，即对ground truth归一化

#### Regularization on object shape

AtlasNet 输出为点云，因此无法保证triangulation的质量，因此为了使mesh更好，引入额外两个loss

* $L_E$表示对于边的长度与平均边长相差很大的惩罚；
* $L_L$表示鼓励物体的曲率接近球体的曲率；

#### Hand-relative coordinate system

1. predict the object in a normalized scale $L^n_{V_{Obj}}$

2. $L_T=\parallel T-\hat{T}\parallel_2^2$          $L_S=\parallel S-\hat{S}\parallel_2^2$

   predict translation $\hat{T}$ (object centriod in hand-relative coordinates) and scale $\hat{S}$ (maximum radius of centroid-centered object) in two branches

3. multiply the AtlasNet decoded vertices by the $\hat{T}$ and  $\hat{S}$  to obtain the final object reconstruction

4. loss in hand-relative coordinates:

   $L_{Object}=L_{V_{Obj}}+L_T+L_S$

### 3. Contract loss

$d(v,V_{ojb})=inf_{w\in{V_{obj}}}\parallel v-w\parallel_2$       $inf$:下确界，集合中值最小的元素

$d(C,V_{ojb})=inf_{v\in{C}}d(v,V_{ojb})$           $inf$:下确界，集合中值最小的元素

$l_\alpha(x)=\alpha tanh(\frac{x}{\alpha})$                             $\alpha$:characteristic distance of action



$L_R(V_{Obj},V_{Hand})=\sum_{v\in{V_{Hand}}}1_{v\in{Int(V_{Obj})}}l_r(d(v,V_{Obj}))$   $r$ : repulsion characteristic distance 2cm

$L_A(V_{Obj},V_{Hand})=\sum_{i=1}^6l_a(d(C_i\bigcap Ext(Obj),V_{Obj}))$  $a$ : 1cm

$L_{contact}=\lambda_RL_R+(1-\lambda_R)L_A$

![image-20191121152055549](image/image-20191121152055549.png)



 <img src="image/20181125220731958.png" alt="img"  /> 

[sigmoid tanh ReLu激活函数](https://blog.csdn.net/lwc5411117/article/details/83620184)

* 训练过程

第一阶段： loss hand+loss object
第二阶段：loss hand +loss object +loss contact

## experiment

intersection volume ：将手和物体体素化

penetration depth ：如果碰撞了，则手上的点到物体表面距离的最大值

simulation ：在仿真环境下，手是固定的，物体受重力，测量物体质心的位移

* evaluation dataset

  First-person hand benchmark(FHB) :video, 3Dhand annotation

  joints 通过手上的magnetic sensor得到

  Hands in action dataset(HIC)



结论:

1.两路寻别训练，然后联合训练的代码实现

2.contact loss设计基本可以直接用，判断接触的方法需要改

3.<font color="##dd066">先估计归一化的物体，然后估计相对于人手坐标系的缩放和平移，人手的坐标系是如何建立的</font>

4.用仿真去验证，手固定，物体受重力，质心位移

5.评估的量可以借鉴



## code 实现

8 类boject  2772个mesh

MANO

通过Grasp 共生成21000个 mesh

image->hand encoder->beta pose trans scale ->MANO->vertices joints

​                                                    前30(pca共45)                                      约 600    21(16+5)

​             ->object encoder->AtlasNet->nomolized vertices *scale +trans->vertices

​                                                                                      642



