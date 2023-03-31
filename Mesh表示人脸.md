# 单目视频重建人头

**《Neural Head Avatars from Monocular RGB Videos》**

## 补充知识

### SMPL

参考链接：

[SMPL模型Shape和Pose参数](https://wap.sciencenet.cn/blog-465130-1177111.html)

[SMPL模型学习](https://www.cnblogs.com/sariel-sakura/p/14321818.html)

SMPL（Skinned Multi-Person Linear Model）是一种裸体的（skinned），基于顶点（vertex-based）的人体三维模型，能够精确地表示人体的不同形状（shape）和姿态（pose）。SMPL适用于动画领域，可以随姿态变化自然的变形，并伴随软组织的自然运动。SMPL与现有的许多图形渲染管线都是兼容的。SMPL是一种可学习的模型，通过训练可以更好地拟合人体的形状和不同姿态下的形变。

[讲解原文](https://www.cnblogs.com/sariel-sakura/p/14321818.html)

1. **Vertex(顶点)**：动画模型可以看成多个小三角形（四边形）组成，每个小三角形就可以看成一个顶点。顶点越多，动画模型越精细。SMPL给出人体的6890个点
2. **骨骼点**：人体的一些关节点，类似于人体姿态估计的关键点。每个骨骼点都由一个三元组作为参数去控制（可以查看欧拉角，四元数相关概念）
3. **蒙皮**：将模型从一个姿态转变为另一个姿态，使用的转换矩阵叫做蒙皮矩阵。
4. **骨骼蒙皮（Rig）**：建立骨骼点和顶点的关联关系。每个骨骼点会关联许多顶点，并且每一个顶点权重不一样。通过这种关联关系，就可以通过控制骨骼点的旋转向量来控制整个人运动。
5. **纹理贴图**：动画人体模型的表面纹理，即衣服裤子这些。
6. **texture map**：将3D多边形网格表面的纹理展开到2D平面，得到纹理图像
7. **混合形状（BlendShape）**：控制动画角色运动有两种，一种是上面说的利用Rig，还有一种是利用BlendShape。比如：生成一种笑脸和正常脸，那么通过BlendShape就可以自动生成二者过渡的动画。这种方式相比于利用Rig，可以不定义骨骼点，比较方便。它指相对于base shape的变形（deformation），这种deformation是通常被表示为顶点的偏移量（vertex displacements），是由某种参数有关的function确定的
8. **混合蒙皮技术（Blend Skinning） **：一种模型网格（mesh）随内在的骨骼结构（skeletal structure）变形的方法。网格的每个顶点（vertex）对于不同的关节点有不同的影响权重（weighted influence），顶点在变形时，形变量与这个权重相关
9. **LBS（Linear Blend Skinning ）**：线性混合蒙皮。使用最广泛，但是在关节处会产生不真实的变形
10. **DQBS（dual-quaternion blend skinning ）**：双四元数混合蒙皮。
11. **顶点权重(vertex weights)**：用于变形网格mesh
12. **uv map**：将3D多边形网格展开到2D平面得到 UV图像
13. **拓扑(topology)**：重新拓扑是将高分辨率模型转换为可用于动画的较小模型的过程。两个mesh拓扑结构相同是指两个mesh上面任一个三角面片的三个顶点的ID是一样的（如某一个三角面片三个顶点是2,5,8；另一个mesh上也必有一个2,5,8组成的三角面片）
14. **pose-dependent blend shape**:姿势相关的混合变形
15. **regressor from shape to joint locations**: 形状到关节位置的回归函数

主要思想：==所有模型都可以表示为一个基础模型（base）和在基础模型上的变化组合而成，主要有姿势（pose）参数和形状参数（shape）==

* **pose(常用$\theta$表示)**

  将人体分为24个关节（joint），每个关节都有三个自由度，可以绕着x，y，z轴旋转，总共有75（24*3+3，表示24个关节的旋转以及整体的旋转），常用运动数描述，表示子关节点相对于父关节点的旋转

  ![image.png](http://tva1.sinaimg.cn/large/70b5161bgy1gxdfvn24nuj20mh0n2q7k.jpg)

  绕某个关节点三个方向旋转后的示意图

  ![image.png](http://tva1.sinaimg.cn/large/70b5161bgy1gxdfv2w21sj21hg0rjtrf.jpg)

  ![image.png](http://tva1.sinaimg.cn/large/70b5161bgy1gxdfwok1dwj21hg0rjwx3.jpg)

* **shape(常用$\beta$表示)**

  控制模型的胖瘦，常用10维（有50维，原文开源10）向量来表示，每个维度表示一种形状变化

  ![image.png](http://tva1.sinaimg.cn/large/70b5161bgy1gxdg2ceh8rj21hc0q17db.jpg)

  ![image.png](http://tva1.sinaimg.cn/large/70b5161bgy1gxdg3eg0fgj21hc0q1aky.jpg)

  > 0 代表整个人体的胖瘦和大小，初始为0的情况下，正数变瘦小，负数变大胖（±5）
  > 1 侧面压缩拉伸，正数压缩
  > 2 正数变胖大
  > 3 负数肚子变大很多，人体缩小
  > 4 代表 chest、hip、abdomen的大小，初始为0的情况下，正数变大，负数变小（±5）
  > 5 负数表示大肚子+整体变瘦
  > 6 正数表示肚子变得特别大的情况下，其他部位非常瘦小
  > 7 正数表示身体被纵向挤压
  > 8 正数表示横向表胖
  > 9 正数表示肩膀变宽

* **函数**

  ==N=6089：表示顶点数==

  ==K=24：表示关节数==

  1. ==SPML function==将形状和位姿参数映射成顶点，$\Theta$表示学习到的参数。
     $$
     M(\vec{\beta},\vec{\theta};\Theta)=W(T_p(\vec{\beta},\vec{\theta}),~J(\vec{\beta}),~\vec{\theta},~\omega)\\M:\Reals^{|\vec{\beta}|\times|\vec{\theta}|}\Rarr\Reals^{3N}\\\vec{\beta},\vec{\theta}\rarr M
     $$

  2. ==标准线性蒙皮函数（the standard linear blend skinning function）==
     $$
     W(T_p,~J,~\vec{\theta},~\omega)\\
     W:\R^{3N\times3K\times|\vec{\beta}|\times|\vec{\theta}|}\Rarr\Reals^{3N}
     $$
     
  3. ==基础模型==
     $$
     T_p(\vec{\beta},\vec{\theta})=\overline{T}+B_S(\vec{\beta})+B_P(\vec{\theta})\\
     T_p,\overline{T},B_S(\vec{\beta}),B_P(\vec{\theta})\in\R^{3N}
     $$
     其中$\overline{T}$表示SMPL模板提供的初始化，$B_S(\vec{\beta}),B_P(\vec{\theta})$表示由shape和pose引起的模板变化
  
     ![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxec5qmbx9j20fk0botb7.jpg)
  
  4. ==姿势预测==
     $$
     J(\vec{\beta}):\R^{|\vec{\beta}|}\Rarr \R^{3K}
     $$
     
  5. ==权重矩阵==
     $$
     \omega \in \R^{K\times N}
     $$
     表示每个关节对于顶点的影响

### FLAME

**《Learning a model of facial shape and expression from 4D scans》**

在SMPL的基础上，使其适用于头部的建模，使用4个关节（男女各不同）

![image.png](http://tva1.sinaimg.cn/large/70b5161bly1gxed72wg0dj215s0gm169.jpg)

加入了表情变量，使原来的SMPL函数变为FLAME函数
$$
M(\vec{\beta},\vec{\theta},\vec{\psi})=W(T_p(\vec{\beta},\vec{\theta},\vec{\psi}),~J(\vec{\beta}),~\vec{\theta},~\omega)\\
T_p(\vec{\beta},\vec{\theta},\vec{\psi})=\overline{T}+B_S(\vec{\beta})+B_P(\vec{\theta})+B_E(\vec{\psi})
$$
==N=5023：表示顶点数==

==K=4：表示关节数==