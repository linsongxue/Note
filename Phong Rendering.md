# Phong Rendering

![image.png](http://tva1.sinaimg.cn/large/006Wf9gugy1h26rqgikboj30mf0bh76g.jpg)

phong渲染主要是将环境光（ambient）、漫反射（diffuse）和镜面反射（specular）相结合

其中所有系数均为常数，另外需要用到表面法向量$\mathbf{n}$，光线方向$\mathbf{l}$和计算得到的$\mathbf{h}$

## 环境光

物体颜色$\times$环境光系数即可
$$
AmbiColor=k_a*Color
$$


## 漫反射

![image.png](http://tva1.sinaimg.cn/large/70b5161bgy1h2a1ne4h0tj20cf08y0t1.jpg)

两种漫反射模型：一种为Lambert模型（上）,一种为Half-Lambert模型（下）
$$
DiffColor=(Color * k_d)*max(0, \mathbf{n}\cdot\mathbf{l})
$$

$$
DiffColor=(Color * k_d)*(a * \mathbf{n}\cdot\mathbf{l} + \beta)
$$

## 镜面反射

**Phong反射**

![image.png](http://tva1.sinaimg.cn/large/70b5161bgy1h2a1xecp8tj20cf08y3z3.jpg)
$$
SpecColor=(Color*k_s)*max(0, \mathbf{v}\cdot \mathbf{r})^{\gamma}\\
\mathbf{r}=\mathbf{l}-2*(\mathbf{n} \cdot \mathbf{l})*\mathbf{n}
$$
其中$\mathbf{v}$表示观察方向，$\mathbf{r}$表示光线反射方向，$\gamma$取值越大光斑越明显

**Blinn-Phong**

![image.png](http://tva1.sinaimg.cn/large/70b5161bgy1h2a2a8alhzj20cz06a0t3.jpg)
$$
SpecColor=(Color*k_s)*max(0, \mathbf{n}\cdot \mathbf{h})^{\gamma}\\
\mathbf{h}=\frac{\mathbf{v}+\mathbf{l}}{|\mathbf{v}+\mathbf{l}|}
$$

## 参考资料

[知乎：Phong氏着色模型](https://zhuanlan.zhihu.com/p/72216510)

