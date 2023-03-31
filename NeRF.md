# 神经辐射场（NeRF）

## 相关任务评价指标

### Peak Signal to Noise Ratio, PSNR（峰值信噪比）

峰值信噪比(Peak Signal to Noise Ratio, PSNR)是一种评价图像质量的度量标准。因为PSNR值具有局限性，所以它只是衡量最大值信号和背景噪音之间的图像质量参考值。PSNR的单位为dB，==**其值越大，图像失真越少。**==

* 一般来说，PSNR高于40dB说明图像质量几乎与原图一样好
* 在30-40dB之间通常表示图像质量的失真损失在可接受范围内
* 在20-30dB之间说明图像质量比较差
* PSNR低于20dB说明图像失真严重。

计算方式（给定一张$m \times n$的标签图$\mathbf{I}$和预测图$\mathbf{K}$）：

1. 首先计算$MSE$

$$
MSE = \frac{1}{mn}\sum_{i=0}^{m-1}\sum_{j=0}^{n-1}[\mathbf{I}(i,j)-\mathbf{K}(i,j)]^2
$$

2. 计算$PSNR$

$$
PSNR = 10 \cdot log_{10}(\frac{MAX_I^2}{MSE})
$$

其中$MAX_I^2 = 2^B - 1$，$B$代表表示图片像素的比特数，例如uint8数据就是8位，对于多通道图片有三种主要方法

* 分别计算 RGB 三个通道的 PSNR，然后取平均值

* 或者计算RGB各个通道的MSE的均值，然后统一求PSNR

* 或者把RGB转化为 YCbCr，然后只计算 Y(亮度)分量的PSNR

### Structural similarity index, SSIM（线形相似指数）

结构相似性指数（structural similarity index，SSIM）是一种用于量化两幅图像间的结构相似性的指标。与L2损失函数不同，SSIM仿照人类的视觉系统（Human Visual System,HVS）实现了结构相似性的有关理论，对图像的局部结构变化的感知敏感。SSIM从==亮度==、==对比度==以及==结构量化图像==的属性，用**均值**估计==亮度==，**方差**估计==对比度==，**协方差**估计==结构相似程度==。==**SSIM值的范围为0至1，越大代表图像越相似。如果两张图片完全一样时，SSIM值为1。**==

计算方式：

给定$\mathbf{x},\mathbf{y}$两张图片，两者之间的照明度(luminance)、对比度 (contrast) 和结构 (structure)分别如下公式所示：
$$
l(x,y)=\frac{2\mu_x\mu_y + c_1}{\mu_x^{2}+\mu_y^2+c_1}\\
c(x,y)=\frac{2\sigma_x\sigma_y + c_2}{\sigma_x^{2}+\sigma_y^2+c_2}\\
s(x,y)=\frac{\sigma_{xy}+c_3}{\sigma_x\sigma_y+c_3}
$$

$$
SSIM=l(x,y)^\alpha \cdot c(x,y)^\beta \cdot s(x,y)^\gamma\\
SSIM=\frac{(2\mu_x\mu_y + c_1)(2\sigma_x\sigma_y + c_2)}{(\mu_x^{2}+\mu_y^2+c_1)(\sigma_x^{2}+\sigma_y^2+c_2)} ,where~~\alpha=\beta=\gamma=1
$$

| 符号           | 意义                                  |
| -------------- | ------------------------------------- |
| $\mu_x$        | x图像的均值                           |
| $\mu_y$        | y图像的均值                           |
| $\sigma_x$     | x图像的标准差（$\sigma_x^2$即是方差） |
| $\sigma_y$     | y图像的标准差（$\sigma_y^2$即是方差） |
| $\sigma_{x,y}$ | 两幅图像的协方差                      |
| $c_1$          | $=(k_1L)^2,~normal~~k_1=0.01,L=2^B-1$ |
| $c_2$          | $=(k_2L)^2,~normal~~k_2=0.03,L=2^B-1$ |
| $c_3$          | $={c_2}/{2}$                          |

### Learned Perceptual Image Patch Similarity, LPIPS（学习感知图像块相似度）

学习感知图像块相似度(Learned Perceptual Image Patch Similarity, LPIPS)也称为“感知损失”(perceptual loss)，用于度量两张图像之间的差别。来源于CVPR2018的一篇论文《The Unreasonable Effectiveness of Deep Features as a Perceptual Metric》，该度量标准学习生成图像到Ground Truth的反向映射强制生成器学习从假图像中重构真实图像的反向映射，并优先处理它们之间的感知相似度。LPIPS 比传统方法（比如L2/PSNR, SSIM, FSIM）更符合人类的感知情况。==**LPIPS的值越低表示两张图像越相似，反之，则差异越大。**==

计算方式（给定一张$m \times n$的标签图$\mathbf{x}$和预测图$\mathbf{x_0}$）：
$$
d(\mathbf{x},\mathbf{x_0})=\sum_{l}\frac{1}{H_lW_l}\sum_{h,w}||w_l \times (\hat{y}^l_{hw}-\hat{y}^l_{0hw})||_2^2
$$
其中$\hat{y}^l_{hw},\hat{y}^l_{0hw}$代表从特征提取堆（通常为CNN）的$l$层提取的输出，而且经过通道归一化

==需要先安装lpips(pip install lpips)、pytorch之后才可以进行LPIPS的计算==

## 整体介绍

![image.png](http://tva1.sinaimg.cn/large/70b5161bgy1gx32goba8pj21v80ggtsw.jpg)

本文主要提出了一种可以将一个5维自变量（$x,y,z,\theta,\phi$）映射为RGB和体积密度（主要是用来计算该点是否透光）值的函数，这种函数被参数化为前馈神经网络的形式。

![image.png](http://tva1.sinaimg.cn/large/70b5161bgy1gx3zrjs1jhj21xq0kyapf.jpg)

方法的主要过程可以概括为

1. 从一束光线采样一系列三维点
2. 用这些三维点坐标和其光线方向输入到网络中，产出该点的颜色和体积密度
3. 使用传统渲染技术将此位置（有三维坐标可以确定位置，方向可以确定看到的形态和颜色）视角下的图像计算出来

这种技术称为==**神经辐射场（neural radiance field）**==

本文中为了更准确、高效得完成视觉变化图像生成任务，在原有方法上加入了==位置编码==和==分级抽象==，前者帮助输入升维升频率，以增强拟合效果，后者则提高拟合效率

与传统的图形学方法对比来说，我们的方法既可以生成高分辨率图像，又可以避免体素化技术带来的大量内存占用

本文三个贡献：

* 将具有复杂几何形状的物体的连续角度视图表示为5维的神经辐射场，并以神经网络的形式来固定参数
* 基于传统图形学方法的体积渲染技术可以生成视角图片，并且生成过程是可微分的。文章中采用阶梯式采样来训练
* 位置编码技术可以将我们的输入信息升维，以更好地拟合最终效果

## 神经辐射场场景表达

上文说使用5维变量作为输入，以RGB和体积密度$\sigma$，但是实际操作中常常使用6维变量（$x,y,z$ + 3D direction）

网络的整体结构为：

* $\mathbf{x}$作为三位坐标输入到8层MLP（各256维）得到==体积密度$\sigma$==和==256维特征向量==

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxawxcs2e7j21jo0maq6v.jpg)

* 256维向量和3D direction输入到一层另外的MLP得到==RGB==

## 使用辐射场进行体积渲染

某一点的颜色表示$\mathbf{C(r)}$

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxaub8t3z4j21sk06wmyz.jpg)

其中$\sigma(x)$表示体积密度，$\mathbf{r}(t)=\mathbf{o}+t\mathbf{d}$，表示从光心$\mathbf{o}$向$\mathbf{b}$方向射的光线，$t_n,t_f$分别表示近（near）光心点和远（far）光心点，$T(t)$表示光线从近点到达远点过程中不被阻碍的概率

### 采样点选择

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxaw5semr7j20gm0badhr.jpg)

为了保证采样点不过于分散，作者采用分散均匀采样的方法，采样的方式为：==在两个采样点间进行均匀采样==，假设有N个采样点

![image-20211212113430247](/Users/xuelinsong/Library/Application Support/typora-user-images/image-20211212113430247.png)

然后体素渲染公式变成

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxawefsq21j21i4084dhe.jpg)

其中$\delta_i=t_{i+1}-t_i$，$\alpha_i = (1 - exp(-\sigma_i\delta_i))$表示alpha合成中的alpha值

## 本文Tricks

### 位置编码

为了较好得拟合低频的5维输入，要将低维输入映射到高频空间，所以使用位置编码，具体编码形式

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxawj7lre3j21ms03k0u0.jpg)

### 分层采样

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxawnxksrtj20eo0bw401.jpg)

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxawoa4pt0j20es0ccq4l.jpg)

文章使用两个模型来模拟粗粒度采样和细粒度采样，首先粗粒度得到大的距离上的体素密度，然后在相对体素密度占据多数的地方增加采样点，最终将两个模型的采样点都用来进行训练

### 损失函数

简单的L2正则函数

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxawrsm72dj215605kgmj.jpg)
