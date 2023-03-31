# Nerfies

**NTK知识**

https://zhuanlan.zhihu.com/p/339971642

一句话总结：NTK衡量的是，在使用SGD优化参数下，其对应的随机到样本 ![[公式]](https://www.zhihu.com/equation?tex=%5Cdisplaystyle+x%27) ，在参数更新非常一小步 ![[公式]](https://www.zhihu.com/equation?tex=%5Cdisplaystyle+%5Ceta) 后， ![[公式]](https://www.zhihu.com/equation?tex=%5Cdisplaystyle+f%28+x%29) 的变化。

NTK在无限宽神经网络下有几个非常重要，有用的性质： 1. 在无限宽的网络中，如果参数 ![[公式]](https://www.zhihu.com/equation?tex=%5Cdisplaystyle+%5Ctheta+_%7B0%7D) 在以某种合适的分布下初始化，那么在该初始值下的NTK ![[公式]](https://www.zhihu.com/equation?tex=%5Cdisplaystyle+k_%7B%5Ctheta+_%7B0%7D%7D) 是一个确定的函数，这意味着，不管我的初始值是多少，最终总会收敛到一个确定的核函数上，它与初始化无关！ 2. 而且在无限宽网络中， ![[公式]](https://www.zhihu.com/equation?tex=%5Cdisplaystyle+k_%7B%5Ctheta+_%7Bt%7D%7D) 并不会随着训练的变化而变化，也就是说，在训练中参数的改变并不会改变该核函数。

## 工作背景

NeRF的提出可以胜任==静态物体的隐式表达==，但是对于人头重建来说，==保持姿态静止和表情不变是难以做到的==，因此作者在这里提出了一种可以用来==重建（细微）动态下人头的算法==。

## 主要思想

因为手持摄像设备采集的图像中，每一帧的人脸、人头都会有细微不同，这就导致即使是3维空间内某一固定点，由不同帧提供的信息（例如rgb）都是相互矛盾的，所以如果直接使用各帧图片来训练一个NeRF的话，效果会很差。但是这不是不可解决的，==当人头发生了细微变化时，虽然脸上的各点的3维坐标和信息发生了改变（例如细微抬头后，脸上的点在3维空间内稍微升高），但是这些点的相对结构没有发生变化，将改变后的点进行一定转化后还能复原为没改变前的形态，==根据这一原则，我们可以选择一个造型，即某一固定帧图像，作为标准来训练一个NeRF，然后其他帧上的信息可以看作是固定帧图像经过一定转化后得到的，这样就可以充分利用采集的信息，且不会造成信息的矛盾。

## 工作描述

### 1.总体过程

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxzfs0d0d6j21v20jqtk5.jpg)

整体遵照上图，选定一个经典场（canonical frame），然后将其他场景（observation frame）经过一个变换域映射到经典场$(x,y,z)\rightarrow (x',y',z') $ 

### 2.实操细节

#### (1)变换域的输出形式

文中将变换域描述为
$$
T(\mathbf{x},\mathbf{\omega}_i) = \mathbf{x} + W(\mathbf{x},\mathbf{\omega}_i)\\
W:(\mathbf{x},\mathbf{\omega}_i) \rightarrow SE(3)\\
SE(3)=(\mathbf{r};\mathbf{v})
$$
文章没有使用简单的位移变化，而是采用李代数，==可以让一系列点使用同一个变化参数==，具体看下图

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxzgqks3akj20wc0ww10a.jpg)

#### (2)弹性约束

由于本算法只针对==微小头部变化==起作用，所以需要约束局部变化的幅度，这一任务由弹性约束来完成，其通过约束某点在变换域 $T$ 作用下的Jacobian矩阵来实现对变化幅度的约束。假设Jacobian矩阵$J_T = U\Sigma V^T$，那么弹性约束可以写作
$$
L_{elastic}=||log\Sigma||^2_F
$$
为了在训练中规避外点影响，文章使用了鲁棒的损失函数
$$
L_{elastic-r}=\rho(||log\Sigma||^2_F, c)\\
\rho(x,c)=\frac{2(x/c)^2}{(x/c)^2+4}
$$

#### (3)背景约束

对于背景，应该是一些固定的不会产生变化的点，所以他们在变换域中的变化越小越好，因此引入背景约束
$$
L_{bg}= \frac{1}{K}\sum^{K}_{k=1}||T(\mathbf{x}_k)-\mathbf{x}_k||_2
$$
其中$\mathbf{x}_k$表示不能动的点，即假设背景点

#### (4)从粗粒度到细粒度

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxzmnn90mnj20ui0lwk6t.jpg)

由于细小面部变化的存在，直接使用高维的位置编码容易产生过拟合，所以作者提出先使用低维度的位置编码来训练低分辨率图像，然后慢慢升高编码纬度