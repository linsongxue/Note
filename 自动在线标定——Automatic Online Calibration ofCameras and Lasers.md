# 自动在线标定——Automatic Online Calibration of Cameras and Lasers

## 总体概括

>In this paper, we introduce two new real-time techniques that enable camera-laser calibration online, automatically, and in arbitrary environments. The first is a probabilistic monitoring algorithm that can detect a sudden miscalibration in a fraction of a second. The second is a continuous calibration optimizer that adjusts transform offsets in real time, tracking gradual sensor drift as it occurs.

这篇文章的两个新技术：

* 一个可以在几分之一秒内发现标定错误（文章说标定数据可能会跟随时间而变化）的监测算法
* 一个是实时的标定优化，可以跟踪传感器漂移的发生

但是文章也存在一个问题：

* 目标函数不是全局凸函数

但是作者说局部最优会在全局最优附近

>Although the calibration objective function is not globally convex and cannot be optimized in real time, in practice it is always locally convex around the global optimum, and almost everywhere else.

因此目标函数的局部形状能够决定当前参数是否需要被优化，而且也能提供优化方向

>Thus, the local shape of the objective function at the current parameters can be used to determine whether the sensors are calibrated, and allows the parameters to be adjusted gradually so as to maintain the global optimum.

文章以==是否需要控制场（标定板）==和==是否需要手动标定点==为两个坐标轴来区分各种标定情况，本文属于不需要控制场、不需要手动标定，==只要场景中存在可被检测到的边界就能进行标定==

## 贡献

* 证明了有可能判断是否标定参数是正确的，即判断之前标定好的传感器是否发生了小的变化
* 发现了小的变化可以在线更新标定参数

## 标定过程

### 图片处理

#### Step 1

将图片中的边选择出来，使用的方法是将图片中的每一个像素位置，赋值为其与8个相邻像素的最大差的绝对值
$$
E_{i,j} = max(|I_{i,j}-I_{i-1,j-1}|,|I_{i,j}-I_{i,j-1}|,...,|I_{i,j}-I_{i+1,j+1}|)
$$
$E_{i,j}$是新产生图片的某一位置上的像素点，$E$是新的图片

#### Step 2: inverse distance transform

$$
D_{i,j} = \alpha E_{i,j} + (1-\alpha)\cdot \mathop{max}_{x,y}E_{x,y}\cdot \gamma^{max(|x-i|,|y-i|)}
$$

其中$D$是此步中的生成图片

这两步图片处理产生的示意图如下所示，上面是Step1，下面是Step2

![两步产生的图片](https://i.loli.net/2021/11/17/9jGKMqURpbYSBt3.png)

### 雷达点云处理

对点云的处理主要是显示雷达距离的突变，原本数据中的点$P_p$与新计算后的某点$X_p$的关系如下所示
$$
X_p = max(P_{p-1}-P_p, P_{p+1}-P_p,0)^{\gamma}
$$
而且这里面点云的距离都是以==米==为单位，另外需要把突变小于==30cm==的点去掉，$\gamma = 0.5$，对点云图处理完的结果如下图所示

![突变点显示](https://i.loli.net/2021/11/18/gGKyIY5ri6muw1o.png)

## 错误标定检测

假设把所有的点云点按照标定矩阵$C$对应到了$X$，此时$X$和$D$是相互对应的，假设目标函数为$J$，那么$J$在标定矩阵为$C$的情况下表示为
$$
J_C=\sum^{n}_{f=n-w} \sum^{|X^f|}_{p=1}X^f_p \cdot D^f_{i,j}
$$
其中需要说明的参数是==$w$==是窗口大小，即用过去多少帧的图像和点云图来计算，那么==$f$==自然是表示帧序号，另外==$i,j$==是==$p$==投影到图片上的位置。

==**如果标定矩阵是正确的，此时$J_C$一定是$J$最大的取值，任何细微扰动都会使此目标函数下降**==

文章中提出让6DoF的标定矩阵$C$在其半径为1的临域中取值，那么共有$3^6=729$种取值，然后让$F_C$表示这些取值使得其目标函数比$J_C$小的比例，如果都比$J_C$小那么$F_C=1$，作者给出了以下测试结果，顶端的蓝线是正确的标定矩阵，剩下的线是随便找了点错误的标定矩阵，从图上可以看出

* 正确标定矩阵在极大概率下会使$J_C$最大
* 窗口越大这种区分越明显

<img src="http://tva1.sinaimg.cn/large/70b5161bly1gwjl2voi9bj20t8130dwc.jpg" alt="image.png" style="zoom:50%;" />

另外的柱状图也可以看出正确标定和错误标定的区别

<img src="http://tva1.sinaimg.cn/large/70b5161bly1gwjlfz114jj21800wygu2.jpg" alt="image.png" style="zoom:50%;" />

然后坐着计算出了正确标定的高斯分布函数
$$
\mu_1=99.7\%\\
\sigma_1=1.4
$$
错误标定的高斯分布
$$
\mu_2=50.5\% \\
\sigma_1=14
$$
然后用P
$$
P=\frac{e^{-0.5(x-\mu_1)^2/\sigma_1^2}}{e^{-0.5(x-\mu_1)^2/\sigma_1^2} + e^{-0.5(x-\mu_2)^2/\sigma_2^2}}
$$
表示$F_C=x$时其是正确标定的概率

## 标定追踪

对于接近正确的标定矩阵使用梯度上升进行修正
