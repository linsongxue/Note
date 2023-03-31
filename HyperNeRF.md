# HyperNeRF

## 工作背景

**Nerfies**解决了细微面部变化情况下的人头重建问题，但是其对于面部拓扑结构变化的场景无能为力，因为Nefies只能隐式表达连续的变化，无法解决突变，即面部拓扑结构的变化，为此本文提出了高维NeRF即HyperNeRF

## 主要思想

原有的Nerfies通过一个变换域将变化场景（obsevation frame）中的点变换映射到经典场（canonical frame）中，==但是这种方法只能适用于变化场景和经典场的差距不是拓扑结构级别的情况，例如可以将椭圆变换映射为圆，但无法将两个半圆变换映射为一个圆==。由于无法在一个经典NeRF中模拟出拓扑结构的变化，所以本文想到将NeRF的渲染空间升高维度，==从原来3维提升到n维，原来是一个静态3维空间，现在是一系列静态3维空间==，举例来说：**假设仅仅升高一个维度，那么在新维度的某一个定值下，应该是一个完整的NeRF，以此类推可以增加任意维度。**这种方式被叫做Level-set，例如一个3维物体可以被切出2维形状，那么4+维的东西切出一个3维NeRF是理论可行的

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxzoy6qk2cj21320lqtfa.jpg)

## 工作描述

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxzp6r833sj21ve0owql5.jpg)

大体过程如上图所示，主要是在Nerfies的变化域基础上加入了一个环境切分面——即在第4+维度上选择一个定值来获得一个完整的NeRF。可以看到在canonical hyper-sapce中确定一个NeRF需要高维度的值**w**，而确定一个具体点则还需要变换域（deformation field），本文工作整体上可以用以下三个式子表示
$$
\mathbf{x}'=T(\mathbf{x},\mathbf{\omega}_i)\\
\mathbf{w}=H(\mathbf{x},\mathbf{\omega}_i)\\
(\mathbf{c},\sigma)=F(\mathbf{x}',\mathbf{w},\mathbf{d},\mathbf{\psi}_i)
$$

#### 可变切平面

对于Level-set任务，轴对称切平面和可变切平面都可以获得同样的结果，如下图。所以本文还可以使用可变切平面去获得渲染信息，效果也在下面展示

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxzr4hj96ej20um0jctgr.jpg)

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxzr6k79z4j21rq0ms7wh.jpg)

#### 网络结构

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxzr7j86woj20q4146dnt.jpg)