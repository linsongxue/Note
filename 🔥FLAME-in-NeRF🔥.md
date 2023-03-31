# **🔥FLAME-in-NeRF🔥**

## 工作背景

* 3DMM和基于PCA的方法可以独立控制脸部形状、表情和外观，但是其只能建模脸，对于头发等头部细节无法建模，同时这种方法只能生成头部不同角度的照片，无法生成在场景中的不同角度照片

* 基于NeRF的方法可以生成场景照片，但是缺乏对于表情等脸部细节的控制

本文则提出一种场景生成和可控细节结合的方式

## 主要思想

用NeRF可以生成某一给定场景下、不同角度的人脸图像，但是不可控表情，想要可控表情，就在训练前先用传统方法提取表达面部表情的参数，然后将这一参数作为输入，应用到NeRF中去，即可完成可控NeRF。此外本文借用了变换域的概念，适应面部表情的微小变化。

## 工作描述

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gy9q3jax94j21gi13u1kx.jpg)

大致过程可以分为：

1. 使用DECA和landmark fitting技术提取每一帧的相机位姿、形状参数和表情参数
2. 使用得到的参数获得头部FLAME模型在当前帧的剪影
3. 通过剪影信息确定当前帧中采样光线的语义信息
4. 和表情参数等一起投入到“表情变化域”NeRF中

### 神经场变化域

$$
x'=D(\mathbf{x},\omega_i)
$$

$D$即变化域，$\omega_i$为变化码，基本就是将Nerfies中的预测SE(3)变为预测位移（需要每个点都预测一个相对位移）

### 表情控制

在NeRF场中加入一个从每一帧图像中检测出的表情参数，控制生成

### 光线采样的空间先验知识

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gy9r8m6nz9j21du0j67ik.jpg)

由于表情只针对脸部，所以仅对脸部光线进行表情参数$\beta_i$的输入，非脸部就乘以系数0

### 经典场

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gy9raka8rhj21gm0sgk55.jpg)

### 变化域

![image-20220111151904535](/Users/xuelinsong/Library/Application Support/typora-user-images/image-20220111151904535.png)