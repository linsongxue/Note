# 标定板标定——Calibration of RGB Camera With Velodyne LiDAR

==本文是but_velodyne代码的论文==

## 评价标准

本文中使用的评价标准是作者自己定义的，主要是将雷达点通过标定参数变换到图像上，然后根据语义来判断是否把==前景==和==背景==混淆了。

$E_p = \frac{E}{P}$

其中$E_p$是整体的错误率，$E$是错误点数量（把前景点对应到了背景或者把背景点对应到了前景），$P$是全部点数量

这使得标定需要以下几个条件：

* 标定板在RGB摄像头中易于被检测出
* 标定板在LiDAR中易于被检测出
* 背景相对平滑

## 粗粒度标定（Coarse calibration）

**这里假定位移相对于角度变换更显著，所以把标定结果暂设为$\begin{bmatrix}1 & 0 & 0 & t_x \\ 0 & 1 & 0 & t_y \\ 0 & 0 & 1 & t_z \\ 0 & 0 & 0 & 1 \end{bmatrix}$**



这里使用标定板如图所示，其特点是容易同时被RGB相机和LiDAR同时检测到

![标定板](https://i.loli.net/2021/11/12/krd4O9pYgBFAcjV.png)

### RGB图像检测

用一般的霍夫圆检测就比较精准，因为这个特征看起来就相对明显，重要的是下一部分的LiDAR检测

### LiDAR检测

对于雷达点云图，首先会对每个点分配一个只值$X_i$
$$
X_i = max(p^r_{i-1}-p^r_i, p^r_i - p^r_{i+1}, 0)^{\gamma}
$$
这个值的作用是判断点$p^r_i$会不会突变，每个点分配到值后会根据阈值0.1去除掉低于此值的点，剩余的点进行以下操作

1. 使用RANSAC的平面模型找到平面上的点，平面上的点会被留下，以外的被去除
2. 使用RANSAC的线段模型找到标定板的边界，并且去除（文中说明了不去除边界点的危害，如图）

![去边界](https://i.loli.net/2021/11/12/qilCR1VdwOp69xU.png)

3. 使用RANSAC的球面模型找到四个圆洞（球面模型和平面模型的交界处就是洞）
4. 验证一下四个圆是否是上图中右边那样的，如果是算法结束，如果不是就进入第五步——剪枝
5. 本步主要是保留一些可能是圆洞的点，如下图，可以看到一个洞确定就可以预测其他洞的大体位置，只要有一个洞预测对了，就可以保留下所有的信息，剪枝完后再转到第3步。值得注意的是这一步剪枝的输入不是第2步的输出，而是第1步的输出，可能是考虑到边界检测的时候会出现一定误差导致圆洞点被删除

![image.png](https://i.loli.net/2021/11/12/v9xckLlPVqm8ip3.png)

### 位移计算

由小孔模型和相对位移模型可知，假设空间某点$\begin{bmatrix} X, Y, Z, 1 \end{bmatrix}^T$在相机坐标系下某点的位置为$\begin{bmatrix} x, y, w \end{bmatrix}^T$那么其像素坐标为$\begin{bmatrix} \frac{x}{w}, \frac{y}{w} \end{bmatrix}$此时可以得到运算公式为
$$
\begin{bmatrix}x \\ y \\ w \end{bmatrix} = P \begin{bmatrix}1 & 0 & 0 & t_x \\ 0 & 1 & 0 & t_y \\ 0 & 0 & 1 & t_z \\ 0 & 0 & 0 & 1 \end{bmatrix} \begin{bmatrix}X \\ Y \\ Z \\ 1\end{bmatrix}
$$
文中的P（相机内参设定为）$\begin{bmatrix}f & 0 & c_x & 0 \\ 0 & f & c_y & 0 \\0 & 0 & 0 & 1 \end{bmatrix}$但是由于我自己参考的其他书籍，在这里替换成$\begin{bmatrix}f & 0 & c_x & 0 \\ 0 & f & c_y & 0 \\0 & 0 & 1 & 0 \end{bmatrix}$，然后文中给出的其他参数计算公式为
$$
\begin{cases}
t_z = \frac{r_{3D}\cdot f}{r_{2D}-Z}\\
t_x = \frac{(x-c_x)(Z+t_z)}{f}-X\\
t_y = \frac{(x-c_y)(Z+t_z)}{f}-Y
\end{cases}
$$


但是我自己的计算公式为
$$
\begin{cases}
t_z = \frac{r_{3D}\cdot f}{r_{2D}}-Z\\
t_x = \frac{x-c_x(Z+t_z)}{f}-X\\
t_y = \frac{x-c_y(Z+t_z)}{f}-Y
\end{cases}
$$
​	其中的$r_{2D},r_{3D}$分别为RGB图像中圆的半径和雷达数据中的圆半径

## 细粒度微调（Fine Calibration）

如果要进行微调，首先要确定一个度量损失的函数，这里选择的是**《Automatic Online Calibration of Cameras and Lasers》**中基于“边”的损失函数

## RANSAC algorithm

**《Random sample consensus: a paradigm for model fitting with applications to image analysis and automated cartography》**

### 理解

>**RANSAC**(**RAN**dom **SA**mple **C**onsensus,随机采样一致)算法是从一组含有“外点”(outliers)的数据中正确估计数学模型参数的迭代算法。“外点”一般指的的数据中的噪声，比如说匹配中的误匹配和估计曲线中的离群点。所以，RANSAC也是一种“外点”检测算法。RANSAC算法是一种不确定算法，它只能在一种概率下产生结果，并且这个概率会随着迭代次数的增加而加大（之后会解释为什么这个算法是这样的）。RANSAC算最早是由Fischler和Bolles在SRI上提出用来解决LDP(Location Determination Proble)问题的。
>
>对于RANSAC算法来说一个**基本的假设**就是数据是由“内点”和“外点”组成的。“内点”就是组成模型参数的数据，“外点”就是不适合模型的数据。同时RANSAC假设：在给定一组含有少部分“内点”的数据，存在一个程序可以估计出符合“内点”的模型。

### 步骤

RANSAC是通过反复选择数据集去估计出模型，一直迭代到估计出认为比较好的模型。
具体的实现步骤可以分为以下几步：

1. 选择出可以估计出模型的最小数据集；(对于直线拟合来说就是两个点，对于计算Homography矩阵就是4个点)
2. 使用这个数据集来计算出数据模型；
3. 将所有数据带入这个模型，计算出“内点”的数目；(累加在一定误差范围内的适合当前迭代推出模型的数据)
4. 比较当前模型和之前推出的最好的模型的“内点“的数量，记录最大“内点”数的模型参数和“内点”数；
5. 重复1-4步，直到迭代结束或者当前模型已经足够好了(“内点数目大于一定数量”)。

### 最优参数选择

这里有一点就是迭代的次数我们应该选择多大呢？这个值是否可以事先知道应该设为多少呢？还是只能凭经验决定呢？ 这个值其实是可以估算出来的。下面我们就来推算一下。

假设“内点”在数据中的占比为$t$ 
$$
t = \frac{n_{inliers}}{n_{inliers} + n_{outliers}}
$$
那么我们每次计算模型使用$n$个点的情况下，选取的点至少有一个外点的情况就是
$$
1 - t^n
$$
也就是说，在迭代 ![[公式]](https://www.zhihu.com/equation?tex=k) 次的情况下，$(1 - t^n)^k$  就是 ![[公式]](https://www.zhihu.com/equation?tex=k) 次迭代计算模型都至少采样到一个“外点”去计算模型的概率。那么能采样到正确的$n$ 个点去计算出正确模型的概率就是

![[公式]](https://www.zhihu.com/equation?tex=P%3D1-%5Cleft%281-t%5E%7Bn%7D%5Cright%29%5E%7Bk%7D+%5C%5C)

通过上式，可以求得

![[公式]](https://www.zhihu.com/equation?tex=k%3D%5Cfrac%7B%5Clog+%281-P%29%7D%7B%5Clog+%5Cleft%281-t%5E%7Bn%7D%5Cright%29%7D++%5C%5C)



## Inverse Distance Transform

**《Automatic Online Calibration of Cameras and Lasers》**



## Sobel operator

检测图像边缘的算子，结合了高斯平滑和差分两种算子：

1. 水平梯度求解：

$\mathbf{G}_x = \begin{bmatrix}-1 & 0 & +1\\-2 & 0 & +2 \\-1 & 0 & +1\end{bmatrix} * \mathbf{I}$

2. 竖直梯度求解

$\mathbf{G}_y = \begin{bmatrix}-1 & -2 & -1\\0 & 0 & 0 \\+1 & +2 & +1\end{bmatrix} * \mathbf{I}$

3. 将两个方向相加

$\mathbf{G} = \begin{vmatrix} \mathbf{G}_x \end{vmatrix} + \begin{vmatrix} \mathbf{G}_y \end{vmatrix}$

