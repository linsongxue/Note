# NeRF+显式头部形状

## 主要需要解决的问题

* 头部在拍摄的时候会发生微动
* NeRF训练生成SDF或者mesh
* （非必要）减轻SDF训练对mask的依赖

## 目前已有方法

**UNISURF**

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gzqq09u0qdj20qg0gu40x.jpg)

* 将volume density分布转化为Occupancy Net
* 主要是从NeRF训练得Occupancy Net（类SDF）
* 也解决了SDF训练对于mask的需求
* 主要方法是：先NeRF得到大体形状和粗略Occupancy Net--寻找光线交汇点--细致NeRF采样
* ==无法解决微动，不支持deformation==
* ==约束严格，只能针对刚体==

**NeuS**

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gzqvp188x0j20pk0em7cg.jpg)

* 主要是将SDF函数与NeRF中的Volume分布结合起来
* 开发一种函数形式，既符合SDF，又可以表示Volume的density分布
* 效果优于UNISURF
* ==无法解决微动，不支持deformation==
* ==还需要用到mask==

**Nerfies**

* 主要是解决面部微变
* 加入一个变化域来将每一帧映射到一个canonical field
* ==无法获得SDF或mesh==

**HyperNeRF**

* 解决了面部大变化
* ==无法获得SDF或mesh==

**HumanNeRF**

* 使用SMPL来补充变化域（类似于以SMPL模型来约束一个变化域）
* 解决身体变化
* ==无法获得SDF或mesh==

**SelfRecon**

* 使用变化域解决IDR在obsevation和canonical的非刚性变化
* 使用骨骼蒙皮完成刚性变化
* 使用骨骼蒙皮内部嵌套变化域完成整个身体变化
* ==没有使用NeRF==
* ==使用大量人体mesh建模，头部可能没有如此精细的mesh建模模型==

## 方法分析

1. HumanNeRF和SelfRecon都是使用传统mesh建模+MLP deformation field的方式来进行变化域的构建，而且后端的渲染也有较大区别，HumanNeRF使用NeRF而SelfRecon则使用IDR
2. 借鉴UNISURF和NeuS，可以使用Occupancy Net或者改造的SDF来表示volume density，其中NeuS直接使用SDF而且有较详细的推导
3. 借鉴Nerfies中的方法，使用纯MLP来完成Occupancy Net或者改造的SDF的变化域
4. NeuS和UNISURF也有结合空间，可以减轻对mask的依赖