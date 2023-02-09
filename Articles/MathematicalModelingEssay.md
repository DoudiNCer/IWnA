# 数学建模

## 规划模型

### 基础知识

#### 向量计算

&emsp;&emsp;向量可看成不同维度上的数的集合：
$$
x=[x_1, x_2, x_3, \cdots x_n], y=[y_1, y_2, y_3, \cdots y_n]
$$

```python
import numpy as np

x = np.array([1, 2, 3])
y = np.array([4, 5, 6])
```

&emsp;&emsp;向量加法：
$$
x+y=[x_1+y_1, x_2+y_2, x_3+y_3, \cdots x_n+y_n]
$$

```python
sum = x + y
```

&emsp;&emsp;向量数乘：
$$
ax=[ax_1, ax_2, ax_3, \cdots ax_n]
$$

```python
tx = 2 * x
```

&emsp;&emsp;向量点乘（内积）：
$$
x \cdot y = \sum_{i=1}^{n} x_iy_i
$$

```python
e = np.dot(x, y)
```

&emsp;&emsp;向量的模：
$$
|x|=\sqrt{x_1^2+x_2^2+x_3^2+ \cdots +x_n^2}
$$


&emsp;&emsp;向量叉乘（外积）：
$$
x \times  = [x_1y_1, x_2y_2, x_3y_3, \cdots x_ny_n]
$$

```python
plus = x * y
```

&emsp;&emsp;向量夹角：
$$
\cos \theta = \frac{x \cdot y}{|x| \cdot |y|}
$$

#### 矩阵与行列式

&emsp;&emsp;矩阵是向量的集合：
$$
M = \begin{pmatrix}  
  a_{11} & \cdots & a_{1n} \\  
  \vdots & \ddots & \vdots \\  
  a_{m1} & \cdots & a_{mn}  
\end{pmatrix}
$$

```python
import numpy as np

M = np.array([[1,2], [3,4]])
```

&emsp;&emsp;矩阵加法与向量加法类似，矩阵乘法使用点乘。

&emsp;&emsp;转置矩阵：
$$
M^T = \begin{pmatrix}  
  a_{11} & \cdots & a_{m1} \\  
  \vdots & \ddots & \vdots \\  
  a_{1n} & \cdots & a_{mn}  
\end{pmatrix}
$$

```python
Mt = M.T
```

&emsp;&emsp;行列式可看成矩阵的“模”：
$$
|M| =\begin{vmatrix}  
  a_{11} & \cdots & a_{1n} \\  
  \vdots & \ddots & \vdots \\  
  a_{m1} & \cdots & a_{mn}  
\end{vmatrix}
$$

```python
Mm = np.linalg.det(M)
```

&emsp;&emsp;伴随矩阵：
$$
M^*=\begin{pmatrix}  
  M_{11} & \cdots & M_{1n} \\  
  \vdots & \ddots & \vdots \\  
  M_{m1} & \cdots & M_{mn}  
\end{pmatrix}
$$


&emsp;&emsp;逆矩阵：
$$
M^{-1} = \frac{1}{|M|}M^*
$$

```python
Mn = np.linalg.inv(M)
```

&emsp;&emsp;矩阵的秩：

```python
RM = np.linalg.matrix_rank(M)
```

#### 线性方程组求解

&emsp;&emsp;数值解：

```python
import numpy as np

A = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
b = np.array([1, 4, 7])
inv_A = np.linalg.inv(A)
x = inv_A.dot(b)
# 以上两行可简写为：
x = np.linalg.solve(A, b)
```

&emsp;&emsp;符号解：

```python
from sympy import symbols, Eq, solve

x, y, z = symbols('x, y, z')
eqs = [Eq(x + 2 * y + 3 * z, 1),
       Eq(4 * x + 5 * y + 6 * z, 4),
       Eq(7 * x + 8 * y + 9 * z, 7)]
X = solve(eqs, [x, y, z])
```

### 标准形式与求解策略

&emsp;&emsp;线性规划问题的“规范形式”如下：
$$
\min c^Tx \\
s.t.\begin{cases}
  Ax \le b \\
  Aeq \cdot x = bex \\
  lb \le x \le rb
\end{cases}
$$
&emsp;&emsp;分别对应目标函数、问题约束条件（方程/不等式）和自变量的约束条件。

> e.g.:
> $$
> \max z = 2x_1 + 3x_2 - 5x_3 \\
> s.t. = 
> \begin{cases}
>   x_1 + x_2 + x_3 = 7 \\
>   2x_1 - 5x_2 +x_3 \ge 10 \\
>   x_1 + 3x_2 +x_3 \le 12 \\
>   x_1, x_2, x_3 > 0
> \end{cases}
> $$
>
> ```python
> import numpy as np
> from scipy.optimize import linprog
> 
> c = np.array([-2, -3, 5])   # 求极小值加负号
> A = np.array([[-2, 5, -1], [1, 3, 1]])  # 小于等于，加负号
> b = np.array([-10,12])
> Aeq = np.array([[1, 1, 1]])
> beq = np.array([7])
> x1, x2, x3 = (0, None), (0, None), (0, None)    # 自变量范围
> res = linprog(c, A, b, Aeq, beq, bounds=(x1, x2, x3))
> print(res)
> ```

&emsp;&emsp;求解规划问题现在有两种方法：单纯形状法和蒙特卡洛法：

- 单纯形法：固定变量，不断变换基向量求方程组的解代入，若都不是最优解则更新迭代现阶段的解。要求约束关系为等式且所有变量非负，需要引入松弛变量
- 蒙特卡洛法：在可行域内随机生成大量数据点，求这些数据点的分布规律。由于计算量大且精确度不高，适合求解非线性问题

### 非线性规划问题

&emsp;&emsp;在线性规划问题基础上，若目标函数非线性或问题约束条件非线性，该问题则转化为非线性规划问题，其“标准形式”为：
$$
\min f(x) \\
s.t.\begin{cases}
  Ax \le b \\
  Aeq \cdot x = bex \\
  C(x) \le 0 \\
  Ceq(x) = 0 \\
  lb \le x \le rb
\end{cases}
$$
&emsp;&emsp;对于非线性方程，可使用拉格朗日乘数法和 KKT：
$$
\mathcal{L}(x, y, \lambda, \mu)=f(x) + \lambda  + \mu C(x)\\
s.t.\begin{cases}
  \left.\dfrac{d\mathcal{L}}{dx}\right|_{x=x^*}=0\\
  \lambda \neq 0\\
  \mu \ge 0\\
  Ceq(x^*) = 0\\
  C(x^*) \le 0\\
  \mu C(x^*) \le 0
\end{cases}
$$

> e.g.
> $$
> \min F\\
> s.t.\begin{cases}
> F = F_{G1} + F_{G2} + F_{G3}\\
> F_{G1}=4+0.3P_{G1}+0.0007P_{G1}^2, 100 \le P_{G1} \le 200\\
> F_{G2}=3+0.32P_{G2}+0.0004P_{G2}^2, 120 \le P_{G2} \le 250\\
> F_{G3}=3.5+0.3P_{G3}+0.00045P_{G3}^2, 150 \le P_{G3} \le 300\\
> P_{G1} + P_{G2} + P_{G3} = 700
> \end{cases}
> $$
>
> ```python
> # scipy Version
> import numpy as np
> from scipy.optimize import minimize
> # 目标函数
> def F(x):
>     return 4.0+0.3*x[0]+0.0007*x[0]**2 + 3+0.32*x[1]+0.0004*x[1]**2 + 3.5+0.2*x[2]+0.00045*x[2]**2
> 
> # 约束条件，分 eq（等于） 和 ineq（大于等于）
> cons = ({"type": "eq", "fun": lambda x: x[0] + x[1] + x[2] - 700})
> 
> b1, b2, b3 = (100, 200), (120, 250), (150, 300)
> x0 = np.array([100, 200, 400])
> res = minimize(F, x0, method="L-BFGS-B", bounds=(b1, b2, b3), constraints=cons)
> print(res)
> ```
>
> ```python
> # 遗传算法
> from sko.GA import GA
> def F(x):
>     return 4.0+0.3*x[0]+0.0007*x[0]**2 + 3+0.32*x[1]+0.0004*x[1]**2 + 3.5+0.2*x[2]+0.00045*x[2]**2
> 
> cons = lambda x: x[0] + x[1] + x[2] - 700
> ga = GA(func=F, n_dim=3, size_pop=500, max_iter=1000, constraint_eq=[cons], lb=[100, 120, 150], ub=[200, 250, 300])
> best_x, best_y = ga.run()
> print("best_x: ", best_x, "\nbest_y: ", best_y)
> ```

### 整数规划

&emsp;&emsp;前面的问题考虑的是连续的情况，但实际生产中有些问题本身是离散的，这时采用连续的思维解决问题可能非常麻烦，也可能不符合实际需求。

&emsp;&emsp;另外，计算机解决连续问题的本质其实是解决离散问题，通过定量计算逼近结果。

&emsp;&emsp;全部变量限制为整数的问题称为纯整数规划，否则为混合整数规划。变量只取0或1的为0-1整数规划。

&emsp;&emsp;整数规划问题的求解有分支定界法、匈牙利法、

### 动态规划

> 没学算法，呜呜呜