# 线性代数

| 符号 |                             含义                             |
| :--: | :----------------------------------------------------------: |
|  A   |                           普通矩阵                           |
|  U   |                     上三角矩阵（Upper）                      |
|  L   |              下三角矩阵（Lower），且对角线均为1              |
|  R   | 简化列梯形矩阵$\begin{bmatrix} \mathbf{I}, \mathbf{F} \\ \mathbf{0}, \mathbf{0}\end{bmatrix}$ |
|  I   |                           单位矩阵                           |
|  F   |                         自由变量矩阵                         |

## C05 转置 置换

**置换（permutation）矩阵 ![[公式]](https://www.zhihu.com/equation?tex=P) 是方阵， ![[公式]](https://www.zhihu.com/equation?tex=P) 的每一行和每一列都有且仅有一个1，其余均为0。**我们在高斯消元法中提到过的交换第i行和第j行的矩阵 ![[公式]](https://www.zhihu.com/equation?tex=P_%7Bij%7D) 就是置换矩阵，例如![[公式]](https://www.zhihu.com/equation?tex=P_%7B23%7D%3D%5Cbegin%7Bbmatrix%7D1%260%260%5C%5C0%260%261%5C%5C0%261%260%5Cend%7Bbmatrix%7D+)； ![[公式]](https://www.zhihu.com/equation?tex=P_%7B23%7DA) 将 ![[公式]](https://www.zhihu.com/equation?tex=A) 的第2行和3行交换，例如： ![[公式]](https://www.zhihu.com/equation?tex=%5Cbegin%7Bbmatrix%7D1%260%260%5C%5C0%260%261%5C%5C0%261%260%5Cend%7Bbmatrix%7D+%5Cbegin%7Bbmatrix%7D1%262%263%5C%5C4%265%266%5C%5C7%268%269%5Cend%7Bbmatrix%7D+%3D%5Cbegin%7Bbmatrix%7D1%262%263%5C%5C7%268%269%5C%5C4%265%266%5Cend%7Bbmatrix%7D) ；另一方面，右乘置换矩阵可以重新排列矩阵的列，如 ![[公式]](https://www.zhihu.com/equation?tex=AP_%7B23%7D) 将 ![[公式]](https://www.zhihu.com/equation?tex=A) 的第2列和第3列交换： ![[公式]](https://www.zhihu.com/equation?tex=%5Cbegin%7Bbmatrix%7D1%262%263%5C%5C4%265%266%5C%5C7%268%269%5Cend%7Bbmatrix%7D+%5Cbegin%7Bbmatrix%7D1%260%260%5C%5C0%260%261%5C%5C0%261%260%5Cend%7Bbmatrix%7D+%3D%5Cbegin%7Bbmatrix%7D1%263%262%5C%5C4%266%265%5C%5C7%269%268%5Cend%7Bbmatrix%7D) 。

**性质**

* n维矩阵有n！个置换矩阵
* 置换矩阵乘积仍为置换矩阵
* 置换矩阵都是可逆的， ![[公式]](https://www.zhihu.com/equation?tex=P%5E%7B-1%7D%3DP%5E%7BT%7D)

## C06 列空间和零空间

* 列空间内向量的个数（列空间维数） = 矩阵主列数目 = 矩阵的==秩==
* 零空间内向量的个数（零空间维数） = 自由变量的数目

## C07 求解$\mathbf{Ax}=\mathbf{0}$ 主变量 特解

将 $\mathbf{A}$ 经过行变换得到阶梯矩阵，类似于$\begin{bmatrix} \mathbf{1}, 2,3,4 \\ 0,\mathbf{3},4,5 \\ 0,0,0,0\end{bmatrix}$，其中每一行第一个不为零（加粗表示）的数字，这一列为主要变量，剩余列为自由变量，自由变量所在的列，其对应的未知数，在确定值的时候可以自由选择（因为其组合不论是什么，主要变量都可以将其补为0）

求解$\mathbf{Ax}=\mathbf{0}$，可以先将普通矩阵化简为$\mathbf{R}$，即得到$\begin{bmatrix} \mathbf{I}, \mathbf{F} \\ \mathbf{0}, \mathbf{0}\end{bmatrix} \mathbf{x}=\mathbf{0}$，可以快速得到$\mathbf{x}=\begin{bmatrix} \mathbf{-F}\\ \mathbf{I}\end{bmatrix}$，如果一个$\mathbf{R}$没有自由变量矩阵，则$\mathbf{x}=\mathbf{0}$

## C08 $\mathbf{Ax}=\mathbf{b}$解的结构

求解$\mathbf{Ax}=\mathbf{b}$，（有解的条件是$\mathbf{b}$是$\mathbf{A}$的列空间线性组合，或者每一行都有主元$r\ge m$）首先求解一个$\mathbf{Ax}=\mathbf{0}$的通解，然后用通解的线性组合加上特解（将所有自由变量设为0，求特解）：

设$\mathbf{Ax}=\mathbf{0}$得解为$x_{n}$，$\mathbf{Ax}=\mathbf{b}$的特解为$x_{p}$，因此所有解可以看做$x_{n}+c \cdot x_{p}$

## C09 线性相关性 基 维数

关于$\mathbf{A}$的基本定义：

* 线性相关：$\mathbf{Ax}=\mathbf{0}$的解除了全零向量，还有其他的 -->  $\mathbf{A}$的零空间除了全零向量还有其他元素
* 线性无关：$\mathbf{Ax}=\mathbf{0}$的解只有全零向量 --> $\mathbf{A}$的零空间只有全零向量
* 向量空间的基（basis）：基是一组向量，且满足两个主要性质 ==1.==组内向量线性无关；==2.==组内向量可以生成一个空间（即组内向量组成的矩阵可逆）
* 一组基内向量个数：等于向量维度

零空间向量的维数=自由变量的数目

## C10 四个基本子空间($m \times n$矩阵)

空间：由基向量组合成的向量集合

1. 列空间：$C(\mathbf{A}) \in R^m$，纬度为r（秩），基为主列向量，需要注意$C(\mathbf{A}) \not = C(\mathbf{R})$
2. 零空间：$N(\mathbf{A})\in R^n$，纬度为n-r，基为特解（将某一个自由变量取1，剩余自由变量取0后回带结果）
3. 行空间：$C(\mathbf{A}^T)\in R^n$，纬度为r（秩）,$C(\mathbf{A}^T) = C(\mathbf{R}^T)$==直观理解为简化梯形矩阵是原矩阵行变换得到的，因此行空间一定是等价的==，因此基为$\mathbf{R}$的前r行
4. （行）左零空间：$N(\mathbf{A}^T)\in R^m$，纬度为m-r，求$N(\mathbf{A}^T)$的基不太容易，需要使用==**高斯-亚当消元法**==（将矩阵和一个单位阵组成增广矩阵，基础行变换使得左边矩阵成为单位矩阵，右侧即位逆）获得$\mathbf{EA}=\mathbf{R}$，然后从$\mathbf{E}$中获得一行能让矩阵行组合为0

## C11 矩阵空间，秩1矩阵，小世界图

空间维度 = 基中向量个数

矩阵空间就是以矩阵为基本元素的空间

所有秩 = 1的矩阵都可以分解为一列 $\times$ 一行，而且n个秩 = 1的矩阵可以组合成一个秩 = n的矩阵

秩 = n 的矩阵并不能组成一个空间

## C12 图和网络

线性代数的一个实际应用就是解释图

视频中以基尔霍夫定律为例讲解了图的矩阵表示形式

==图中环的数 = 图的边数 - （图节点数 - 1）==

欧拉公式$\uparrow$，对任何拓扑图都适用

## C14 正交向量与子空间

==**正交**== 

子空间S和子空间T正交 = S内的任意向量和T内的任意向量夹角为90度（如果两个字空间有相交，那么不可能正交）

行空间 $\bot$ 零空间 

列空间 $\bot$ 左零空间

rank of $\mathbf{A}^T\mathbf{A}$ = rank of $\mathbf{A}$

$\mathbf{A}^T\mathbf{A}$ 可逆当且仅当 $\mathbf{A}$ 各列线性无关

## C15 子空间投影

![70b5161bly1h3w1ad4bxgj20q40dg0w9](./images/70b5161bly1h3w1ad4bxgj20q40dg0w9.jpeg)



在此假设下可解得
$$
\begin{cases}
x = \frac{\mathbf{a}^T \mathbf{b}}{\mathbf{a}^T \mathbf{a}}~or~(\mathbf{A}^T \mathbf{A})^{-1}\mathbf{A}^T \mathbf{b}\\
\mathbf{p} = \mathbf{a} \frac{\mathbf{a}^T \mathbf{b}}{\mathbf{a}^T \mathbf{a}}~or~\mathbf{A}(\mathbf{A}^T \mathbf{A})^{-1}\mathbf{A}^T \mathbf{b} \\
\mathbf{p} = \mathbf{P}\cdot \mathbf{b},~where~\mathbf{P}=\frac{\mathbf{a}~\mathbf{a}^T}{\mathbf{a}^T \mathbf{a}}~or~\mathbf{A}(\mathbf{A}^T \mathbf{A})^{-1}\mathbf{A}^T
\end{cases}
$$
其中$\mathbf{P}$表示投影矩阵（同理，投影到该平面正交平面的$\mathbf{I} - \mathbf{P}$也是投影矩阵），$\mathbf{a}$表示向量，$\mathbf{A}$表示矩阵，其性质：

* $\mathbf{P}$的列空间表示与$\mathbf{a}$共线的点
* $\mathbf{P}$秩为1
* $\mathbf{P}^T = \mathbf{P}$
* $\mathbf{P}^2 = \mathbf{P}$（所以其特征值只能为0或者1）

==投影主要是为了解决$\mathbf{Ax}=\mathbf{b}$无解的问题，先将$\mathbf{b}$投影为$\mathbf{p}$上，求一个近似解：具体就是在$\mathbf{Ax}=\mathbf{b}$左右两边同时乘以$\mathbf{A}^T$==

## C16 投影矩阵，最小二乘

$\mathbf{P}=\mathbf{A}(\mathbf{A}^T \mathbf{A})^{-1}\mathbf{A}^T$是将向量投影到$\mathbf{A}$的列空间$C(\mathbf{A}) \in R^m$的投影矩阵；如果把将向量投影到$\mathbf{A}$的左零空间$N(\mathbf{A}^T)\in R^m$（$C(\mathbf{A}) \in R^m$的正交空间）则其投影向量为$\mathbf{I} - \mathbf{P}$，其数值可以看做是投影带来的误差

投影到==满秩矩阵的列空间===单位矩阵

**==$\mathbf{A}^T\mathbf{A}$ 可逆当且仅当 $\mathbf{A}$ 各列线性无关，这也是投影矩阵成立的前提条件==**

**最小二乘**

参考上文==投影主要是为了解决$\mathbf{Ax}=\mathbf{b}$无解的问题，先将$\mathbf{b}$投影为$\mathbf{p}$上，求一个近似解：具体就是在$\mathbf{Ax}=\mathbf{b}$左右两边同时乘以$\mathbf{A}^T$==

## C17 正交矩阵 Schmidt正交化

**正交矩阵**$\mathbf{Q}$：$\mathbf{Q}$的每一列都相互正交，模均为1，且$\mathbf{A}^T \cdot \mathbf{A} = \mathbf{I}$，不论对方阵还是矩阵都成立。其要求矩阵既要正交也要标准

将普通矩阵正交化会带来极大便利，例如$\mathbf{P}=\mathbf{Q}(\mathbf{Q}^T \mathbf{Q})^{-1}\mathbf{Q}^T = \mathbf{Q}\mathbf{Q}^T$，$\mathbf{Q}^T\mathbf{Qx}=\mathbf{Q}^T\mathbf{b} \rightarrow \mathbf{x}=\mathbf{Q}^T\mathbf{b}$

**格拉姆-施密特正交化法**：

对于3个线性不相关的向量$\mathbf{a},\mathbf{b},\mathbf{c} \rightarrow$

求3个向量的正交形式$\mathbf{A},\mathbf{B},\mathbf{C}$：第一个向量$\mathbf{A}=\mathbf{a}$，取原值；第二个向量$\mathbf{B}$则是将$\mathbf{b}$减去其在$\mathbf{A}$上的投影，即$\mathbf{B} = \mathbf{b} - \frac{\mathbf{A}^T\mathbf{b}}{\mathbf{A}^T\mathbf{A}}\mathbf{A}$；同理，第三个向量$\mathbf{C}$则是将$\mathbf{c}$减去其在$\mathbf{a},\mathbf{b}$上的投影，即$\mathbf{C} = \mathbf{c} - \frac{\mathbf{A}^T\mathbf{c}}{\mathbf{A}^T\mathbf{A}}\mathbf{A}- \frac{\mathbf{B}^T\mathbf{c}}{\mathbf{B}^T\mathbf{B}}\mathbf{B} \rightarrow $

对$\mathbf{A},\mathbf{B},\mathbf{C}$归一化，n个向量同理三个向量

**线性代数角度看正交化**

$\mathbf{A} = \mathbf{Q}\cdot \mathbf{U}$，表示一个矩阵进行正交化分解后可以看做是正交矩阵和一个上三角矩阵的乘积，上三角矩阵主要作为消元操作（为何是上三角矩阵，主要是一个正则化向量仅依靠其前面的向量）

## C18 行列式及其性质

**行列式性质**

1. 单位矩阵的行列式为1，$det(\mathbf{I})=1$

2. 交换矩阵的行，行列式取反数

3. 

   * $$
     \begin{vmatrix}
     ta, tb\\
     c, d
     \end{vmatrix}
     =
     t\begin{vmatrix}
     a, b\\
     c, d
     \end{vmatrix}
     $$

   * $$
     \begin{vmatrix}
     a+a', b + b'\\
     c, d
     \end{vmatrix}
     =
     \begin{vmatrix}
     a, b\\
     c, d
     \end{vmatrix}
     +
     \begin{vmatrix}
     a', b'\\
     c, d
     \end{vmatrix}
     $$

4. 两行相同，行列式为0

5. $$
   \begin{vmatrix}a, b\\c-l\cdot a, d-l\cdot b\end{vmatrix}=\begin{vmatrix}a, b\\c, d\end{vmatrix}
   $$

6. 矩阵有一行为0，行列式为0
7. $det(\mathbf{U}) = \prod diag(\mathbf{U})$，因此可以先使用基本行变化将矩阵变为上三角矩阵，再求行列式
8. 如果行列式为0，则矩阵为奇异阵（存在零空间）；不为0，则可逆
9. $det(\mathbf{AB}) = det(\mathbf{A})\cdot det(\mathbf{B})$，仅对方阵成立
10. $det(\mathbf{A}) = det(\mathbf{A}^T)$

## C19 行列式公式 代数余子式

对于一个$n\times n$矩阵$\mathbf{A}$，

$det(\mathbf{A}) = \sum^{n!}\pm (a_{1\alpha}\cdot a_{2\beta}…a_{n\omega})~where~(\alpha, \beta,…,\omega)~is~a~sort~of~1~to~n$

正负取决于$(\alpha, \beta, …,\omega)$的排列顺序，如果能偶数步调整为$(1, 2, …, n)$则取正号；奇数步调整为则取负号
$$
\begin{bmatrix}
+,-,+,-,……\\
-,+,-,+,……\\
……
\end{bmatrix}
$$


**代数余子式**：将某一元素的所在行、所在列都消去然后剩余部分的行列式就是代数余子式（还要考虑正负）

$det(\mathbf{A}) = \sum^{n}_{i}a_{1i}c_{1i}$

## C20 克拉莫法则 逆矩阵 体积

**逆矩阵**

$\mathbf{A}^{-1} = \frac{\mathbf{A}^*}{det(\mathbf{A})}$其中$\mathbf{A}^*=\begin{bmatrix} c_{11}, c_{12}, …, c_{1n}\\……\\ c_{n1}, c_{n2}, …, c_{nn}\end{bmatrix}^T$是伴随矩阵，其中$c_{ij}$是$a_{ij}$的代数余子式

**克拉莫法则**

$\mathbf{Ax}=\mathbf{b} \rightarrow \mathbf{x} = \mathbf{A}^{-1}\mathbf{b}$

$\mathbf{x} = \begin{bmatrix}x_1\\x_2\\…\\x_n \end{bmatrix} \rightarrow x_i = \frac{det(\mathbf{B}_i)}{det(\mathbf{A})}$

其中$\mathbf{B}_i$表示将$\mathbf{A}$的第$i$列替换为$\mathbf{b}$

**体积**

行列式 = 一个箱子的体积

正交方阵的行列式为1

## C21 特征值 特征向量

**特征值、特征向量**

$\mathbf{Ax}= \lambda\mathbf{x}$：$\mathbf{x}$是特征向量，$\lambda$是特征值

**特征向量特征值和投影的关系**

可以将$\mathbf{A}$看作一个投影矩阵，其将向量$\mathbf{x}$投影到$\mathbf{A}$的列向量空间后还可以得到$\mathbf{x}$，说明$\mathbf{x}$要么本来就在$\mathbf{A}$的列向量空间，要么就是与$\mathbf{A}$的列向量空间垂直
$$
\begin{cases}
\mathbf{Ax}=\mathbf{x}\\
\mathbf{Ax}=\mathbf{0}
\end{cases}
$$
**特征值的性质**

* 一个n维矩阵有n个特征值$\lambda_1, \lambda_2,…, \lambda_n$，且$\lambda_1+\lambda_2+…+\lambda_n = trace(\mathbf{A})$，且$\lambda_1\cdot \lambda_2\cdot…\cdot \lambda_n = det(\mathbf{A})$
* 假设$\mathbf{A}$的特征值为$\lambda_1, \lambda_2,…, \lambda_n$，特征向量为$\mathbf{x}_1, \mathbf{x}_2, …, \mathbf{x}_n$，那么$\mathbf{A}+n\mathbf{I}$的特征值为$\lambda_1+n, \lambda_2+n,…, \lambda_n+n$，特征向量为$\mathbf{x}_1, \mathbf{x}_2, …, \mathbf{x}_n$
* 一个矩阵的秩=其非0特征值个数（零空间维度=0特征值数量）

**解特征值**

$\begin{aligned} \mathbf{Ax}&=\lambda\mathbf{x}\\(\mathbf{A}-\lambda\mathbf{I}) \mathbf{x}&=\mathbf{0}\end{aligned}$

## C22 对角化 幂

**对角化**

意义：方便求幂

前提条件：矩阵$\mathbf{A}$的特征向量$\mathbf{x}_1, \mathbf{x}_2, …, \mathbf{x}_n$线性无关（特征值$\lambda_1, \lambda_2,…, \lambda_n$有重复也行）

对于一个矩阵$\mathbf{A}$，如果其存在$n$个==线性无关==特征向量$\mathbf{x}_1, \mathbf{x}_2, …, \mathbf{x}_n$，特征值为$\lambda_1, \lambda_2,…, \lambda_n$，那么设$\mathbf{S} = \begin{bmatrix} \mathbf{x}_1, \mathbf{x}_2,…, \mathbf{x}_n\end{bmatrix},~\mathbf{\Lambda} = \begin{bmatrix} \lambda_1, 0, …, 0 \\ 0, \lambda_2,…,0 \\ …… \\0,0,…,\lambda_n\end{bmatrix},\mathbf{\Lambda}$是对角矩阵
$$
\begin{aligned}
\mathbf{AS} &= \mathbf{A}\begin{bmatrix} \mathbf{x}_1, \mathbf{x}_2,…, \mathbf{x}_n \end{bmatrix}\\
&=\begin{bmatrix} \lambda_1\mathbf{x}_1, \lambda_2\mathbf{x}_2,…, \lambda_n\mathbf{x}_n \end{bmatrix}\\
&=\mathbf{S\Lambda}\\
\mathbf{S}^{-1}\mathbf{AS} &= \mathbf{\Lambda}\\
\mathbf{A} &= \mathbf{S}\mathbf{\Lambda}\mathbf{S}^{-1}\\
\mathbf{A}^n &= \mathbf{S}\mathbf{\Lambda}^n\mathbf{S}^{-1}
\end{aligned}
$$
结果：$\mathbf{A}^n$的特征向量为$\mathbf{x}_1, \mathbf{x}_2, …, \mathbf{x}_n$，特征值$\lambda_1^n, \lambda_2^n,…, \lambda_n^n$，因此投影矩阵的特征值仅能为1或者0

**一个简单例子**

从$\mathbf{u}_0$开始，假设$\mathbf{u}_{k+1} = \mathbf{A}\mathbf{u}_k$

假设$\mathbf{u}_0 = c_1\mathbf{x}_1+…+c_n\mathbf{x}_n = \mathbf{S}\mathbf{c},~where~\mathbf{c}~is ~linear~combination~of~ c_1…c_n$

$\mathbf{u}_{k+1} = \mathbf{A}\mathbf{u}_k \\ \rightarrow \mathbf{u}_k = \mathbf{A}^k\mathbf{u}_0\\ \rightarrow \mathbf{u}_k = \mathbf{S\Lambda}^k\mathbf{S}^{-1}\cdot\mathbf{S}\mathbf{c} = \mathbf{S\Lambda}^k\mathbf{c}=\mathbf{\Lambda}^k\mathbf{Sc} = \sum_{i}^{n}c_i\lambda_i^k\cdot\mathbf{x}_i$

$\mathbf{c}$由特值带入求出

## C23 微分方程 $e^{\mathbf{A}t}$

**解微分方程**

$\frac{d\mathbf{u}}{dt} = \mathbf{A}\mathbf{u}$的解一般是指数形式：$\mathbf{u}(t) =e^{\mathbf{A}t}\mathbf{u}(0)= \begin{bmatrix}e^{\lambda_1t},0,…,0\\ 0,e^{\lambda_2t},…,0\\……\\0,0,…,e^{\lambda_nt}\end{bmatrix}\mathbf{Sc}$，$\mathbf{c}$由特值带入求出，即

所有特征值都为负时，解收敛

**矩阵的幂**

$\begin{aligned} e^{\mathbf{A}t} &=\mathbf{I} + \frac{(\mathbf{A}t)^2}{2!}+…+\frac{(\mathbf{A}t)^n}{n!} \\ &= \mathbf{S}\mathbf{S}^{-1} + \frac{(\mathbf{S\Lambda}\mathbf{S}^{-1})^2t^2}{2!}+…+\frac{(\mathbf{S\Lambda}\mathbf{S}^{-1})^nt^n}{n!}\\&=\mathbf{S}(\mathbf{I} + \frac{(\mathbf{\Lambda}t)^2}{2!}+…+\frac{(\mathbf{\Lambda}t)^n}{n!})\mathbf{S}^{-1}\\&=\mathbf{S}^{-1}e^{\mathbf{\Lambda}t}\mathbf{S}^{-1}\\&=\mathbf{S}\begin{bmatrix}e^{\lambda_1t},0,…,0\\ 0,e^{\lambda_2t},…,0\\……\\0,0,…,e^{\lambda_nt}\end{bmatrix}\mathbf{S}^{-1}\end{aligned}$

！！！前提：可以对角化

## C24 特征值应用：马尔可夫矩阵、傅立叶级数

**马尔可夫矩阵**

==性质==：所有元素均为正数；每一列的和为1

==特点==：有一个$\lambda=1$的特征值；其他特征值均$\lt 1$

$\mathbf{A}-\mathbf{I}$是一个奇异矩阵，因为各行相加为0了（因为每列的和为1，那么对角线元素减去1之后就是剩余行和的相反数）

**傅立叶级数**

前提：假设一个向量$\mathbf{v}=x_1\mathbf{q}_1+x_2\mathbf{q}_2+…+x_n\mathbf{q}_n=\mathbf{Qx}$，则$\mathbf{q}_1^T\mathbf{v}=x_1\mathbf{q}_1^T\mathbf{q}_1+\mathbf{0}+…+\mathbf{0}=x_1$

设$f(x)=a_0+a_1\cdot cosx+b_1\cdot sinx+a_1\cdot cos2x+b_1\cdot sin2x+……$，定义函数内积为$f^Tg=\int f(x)g(x)dx$，则
$$
\begin{aligned}
f^Tcosx&=\int_{0}^{2\pi} a_0\cdot cosx+a_1\cdot cos^2x+b_1\cdot sinx\cdot cosx+a_1\cdot cos2x\cdot cosx+b_1\cdot sin2x\cdot cosx+……\\&=\int_{0}^{2\pi} 0+a_1\cdot cos^2x+0+……\\&=a_1\cdot \pi
\end{aligned}
$$

## C26 对称矩阵及正定性

**对称矩阵$\mathbf{A}^T=\mathbf{A}$**

* 所有特征值均为==实数==
* 可以选择一套特征向量，彼此==垂直==（$\mathbf{A}=\mathbf{S\Lambda S}^{-1} \rightarrow \mathbf{A}^T=\mathbf{S}^{-T}\mathbf{\Lambda}^T\mathbf{S}^{T}\rightarrow \mathbf{S}^{-1}=\mathbf{S}^T$，所以$\mathbf{S}$是正交矩阵）
* 根据上面两条性质可知，$\mathbf{A}=\mathbf{Q\Lambda Q}^{-1} = \mathbf{Q\Lambda Q}^{T}=\lambda_1\mathbf{q}_1\mathbf{q}_1^T+\lambda_2\mathbf{q}_2\mathbf{q}_2^T+…+\lambda_n\mathbf{q}_n\mathbf{q}_n^T$，由于$\mathbf{q}^T\mathbf{q}=1$，所以投影矩阵$\frac{\mathbf{qq}^T}{\mathbf{q}^T\mathbf{q}} = \mathbf{qq}^T$，所以对称矩阵是由一系列投影矩阵相加得到
* 主元的正负性与特征值一致

**正定矩阵**

特性：==1.==是对称矩阵；==2.==所有特征值均为正数；==3.==所有主元均为正数；==4.==从左上开始每个子矩阵的行列式均为正数

**反对称矩阵** **$\mathbf{A}^T=\mathbf{-A}$**

其特征向量也相互正交（$\mathbf{A}\mathbf{A}^T=\mathbf{A}^T\mathbf{A}$则特征向量相互正交）

## C27 复数矩阵 快速傅立叶变换

**复数矩阵**

对于一个复数矩阵$\mathbf{z} = \begin{bmatrix}z_1\\z_2\\…\\z_n \end{bmatrix}$，求其模长需要$||\mathbf{z}||=\bar{\mathbf{z}}^T\mathbf{z}$，可以简写为$\bar{\mathbf{z}}^T\mathbf{z}=\mathbf{z}^H\mathbf{z}$。

同理可以定义==内积===$\mathbf{y}^H\mathbf{x}$

==对称矩阵==$\bar{\mathbf{A}}^T=\mathbf{A} \rightarrow \mathbf{A}^H=\mathbf{A}$

==正交==$\mathbf{q}_i^H\mathbf{q}_j=0$

==复正交矩阵==$\mathbf{Q}^H\mathbf{Q}=I$，也叫做酉矩阵

**傅立叶矩阵**
$$
\mathbf{F}_n = 
\begin{bmatrix}
1&1&1&…&1\\
1&w&w^2&…&w^{(n-1)}\\
1&w^2&w^4&…&w^{2(n-1)}\\
…&…&…&…&…\\
1&w^{n-1}&w^{2(n-1)}&…&w^{(n-1)^2}\\
\end{bmatrix}
\\
F_{ij}=w^{ij}, i = 0,1,...,(n-1)\\
w^n=1
$$
例：
$$
\mathbf{F}_4=
\begin{bmatrix}
1&1&1&1\\
1&i&-1&-i\\
1&-1&1&-1\\
1&-i&-1&i\\
\end{bmatrix}
$$


==性质==：

* $\mathbf{F}$是对称矩阵：$\mathbf{F}^H=\mathbf{F}$

* $\mathbf{F}$是正交矩阵：$\mathbf{F}^H\mathbf{F}=\mathbf{I}$

==意义==：

将矩阵乘法的复杂度从$n^2$降低到$\frac{n}{2}log_{2}^n$

## C28 正定矩阵 最小值

**正定矩阵**

*特性就可以证明正定性*

对任意$\mathbf{x}$有$\mathbf{x}^T\mathbf{Ax}\gt0$，则$\mathbf{A}$为正定矩阵（用配方法配出平方项相加）

对于$\begin{bmatrix} a&b\\b&c \end{bmatrix}$，$\mathbf{x}^T\mathbf{Ax}=ax_1^2+2bx_1x_2+cx_2^2$

$\mathbf{A}_{m\times n}$：$\mathbf{A}^T\mathbf{A}$是==半正定==（有特征值=0）矩阵，如果秩为n，则为==正定矩阵==

**极小值判定**

对于函数$f(x,y)$，其极小值判定条件可以分为一阶导数部分和二阶导数部分

* 一阶导数：$\begin{bmatrix} \frac{\partial f}{\partial x}\\\frac{\partial f}{\partial y} \end{bmatrix}=0$
* 二阶导数：$\begin{bmatrix} \frac{\partial^2 f}{\partial x^2} & \frac{\partial^2 f}{\partial y \partial x} \\\frac{\partial^2 f}{\partial x \partial y}&\frac{\partial^2 f}{\partial y^2}\end{bmatrix}$是正定矩阵

## C29 相似矩阵 若尔当型

**相似**

$\mathbf{A}$相似于$\mathbf{B}$：存在$\mathbf{M}$使得$\mathbf{B}=\mathbf{M}^{-1}\mathbf{AM}$

* 相似矩阵都拥有相同的特征值
* 特征向量不相同，$\mathbf{B}$的特征向量为$\mathbf{M}^{-1}\mathbf{x}$
* 对角矩阵只与自身相似

**若尔当型**

对角矩阵（特征值全为$\lambda$）的对角线旁边填充1。若尔当标准矩阵是最接近对角矩阵，但是不与对角矩阵相似的矩阵

任何矩阵都可以相似于一个若尔当矩阵，若尔当矩阵由各个若尔当块组成

## C30 奇异值分解

**奇异值分解的根本目的就是将一个矩阵分解到四个空间**

假设一个矩阵$\mathbf{A}_{m\times n}$(假设$m>n$)，其意义是==将行空间一组正交基映射到列空间中==，现在要寻找行空间内的一组==正交基==$\mathbf{v}_1,\mathbf{v}_2,…,\mathbf{v}_r$，使得经过矩阵$\mathbf{A}_{m\times n}$映射后变为列空间一组==正交基==$\mathbf{u}_1,\mathbf{u}_2,…,\mathbf{u}_r$，即$\lambda_1\mathbf{u}_1=\mathbf{Av}_1, …, \lambda_r\mathbf{u}_r=\mathbf{Av}_r$，此外由于四个空间的互补性，可以知道在零空间存在一组基$\mathbf{v}_{r+1},…,\mathbf{v}_n$使得$\mathbf{0}=\mathbf{Av}_{r+1}, …, \mathbf{0}=\mathbf{Av}_n$，左零空间内存在一组基$\mathbf{u}_{r+1},…,\mathbf{u}_{m}$，令$\mathbf{V}=\begin{bmatrix}\mathbf{v}_1,\mathbf{v}_2,…,\mathbf{v}_n \end{bmatrix}$同时$\mathbf{U}=\begin{bmatrix}\mathbf{u}_1,\mathbf{u}_2,…,\mathbf{u}_m \end{bmatrix}$
$$
\mathbf{A}\cdot\begin{bmatrix}\mathbf{v}_1,\mathbf{v}_2,…,\mathbf{v}_n \end{bmatrix}=\begin{bmatrix} \lambda_1\mathbf{u}_1, \lambda_2\mathbf{u}_2, ..., \lambda_r\mathbf{u}_r, \mathbf{0},...,\mathbf{0}\end{bmatrix}=\begin{bmatrix}\mathbf{u}_1, \mathbf{u}_2,...,\mathbf{u}_m \end{bmatrix}\cdot\begin{bmatrix} \lambda_1, 0, ......, 0\\ 0, \lambda_2,......,0 \\ ......\\0, ..., \lambda_r, ...,0\\ .......\\0,...,0,...,0\end{bmatrix}_{m\times n}\\
\mathbf{AV}=\mathbf{U\varSigma }\\
\mathbf{A}=\mathbf{U\varSigma V}^{-1}\\
\mathbf{A}=\mathbf{U\varSigma V}^{T}\\
\mathbf{A}^T\mathbf{A} = \mathbf{V\varSigma}^T\mathbf{U}^{T} \mathbf{U\varSigma V}^{T}=\mathbf{V\varSigma}^2\mathbf{V}^{T}\\
\mathbf{A}\mathbf{A}^T = \mathbf{U\varSigma V}^{T} \mathbf{V\varSigma}^T\mathbf{U}^{T}=\mathbf{U\varSigma}^2\mathbf{U}^{T}
$$
其中$\mathbf{V}^T=\mathbf{V}^{-1}$并且$\mathbf{U}^T=\mathbf{U}^{-1}$

可见$\mathbf{U\varSigma V}$由$\mathbf{A}^T\mathbf{A}$和$\mathbf{A}\mathbf{A}^T$分解得到，且$\mathbf{U V}$均为正交矩阵
$$
\mathbf{A}^T\mathbf{A}=\mathbf{V\varSigma}^2\mathbf{V}^{T}
$$
可以看做方阵的对角化（C22），因此$\mathbf{V}$就是$\mathbf{A}^T\mathbf{A}$矩阵的特征向量矩阵，而$\mathbf{\varSigma}^2$则是特征值对角矩阵

值得注意的是，如果$\mathbf{A}^T\mathbf{A}$或$\mathbf{A}\mathbf{A}^T$有特征值为0，那么其对应的特征向量应该在零空间或者左零空间内

## C31 线性变换及对应矩阵

**线性变化**

对加法和数乘封闭

向量左乘一个$\mathbf{A}$相当于进行线性变换，假设原空间的一组基$\mathbf{v}_1, \mathbf{v}_2,…,\mathbf{v}_n$，目标空间的一组基$\mathbf{w}_1, \mathbf{w}_2,…,\mathbf{w}_n$，线性变换的确定可以根据
$$
\mathbf{Av}_1=a_{11}\mathbf{w}_1+a_{21}\mathbf{w}_2+…+a_{n1}\mathbf{w}_n\\
\mathbf{Av}_2=a_{12}\mathbf{w}_1+a_{22}\mathbf{w}_2+…+a_{n2}\mathbf{w}_n\\
A = \begin{bmatrix} a_{11}&a_{12}&&a_{1n} \\
a_{21}&a_{22}&&a_{2n}\\
&\\
a_{n1}&a_{n2}&&a_{nn}\end{bmatrix}
$$
==如果使用特征向量作为变换后的基，则变换矩阵为对角矩阵，且对角线上是特征值==

## C32 基变换 图像压缩

**基变换**

如果一个向量可以通过线性变换$\mathbf{A}$和$\mathbf{B}$，得到两套基表示的坐标，那么$\mathbf{A}$和$\mathbf{B}$是相似矩阵