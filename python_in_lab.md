# README

本 Worksheet 在 VSCode 中食用效果最佳。

<br>

# 安装 `SymPy`

详见 [Installation - SymPy 1.14.0 documentation](https://docs.sympy.org/latest/install.html).

<br>

# 符号运算和 `SymPy`

诸如 `math`, `decimal` 和 `NumPy` 的数值计算库可以为我们快速求出某个式子的值或某个方程的数值解。但在数值解为无理数、无穷级数等无法通过有限计算求出精确值的情况下，数值计算只能给出*近似解*。

<br>

> _*Example 1*_ — 平方根问题
>
> 给定一个正实数 $x$，化简 $\sqrt{x}$.

如果使用 Python 自带数学库中的 `math.sqrt` 函数的话，我们只能得到近似解：

```py
>>> import math
>>> math.sqrt(8)
2.82842712475
```

这在最后求值时很有用，但是如果我们的需求是推导某个结论的过程中对某个式子进行开根的话，那么代入近似值很可能会产生*精度误差*，并导致结果无法化简或无法求得最终结果的精确形式。

这种情况下，我们需要引入符号计算库。同样，我们用 `SymPy` 库中的开根函数 `sympy.sqrt` 对 $8$ 开根号：

```py
>>> import sympy
>>> sympy.sqrt(8)
2*sqrt(2)
```

`SymPy` 库自动保留了不能够被写成有理数形式的平方根。

符号运算库的功能不仅限于此。`SymPy` 可以保持数学表达式的符号形式，并进行精确的代数运算和化简。

<br>

> _*Example 2.1*_
>
> 使用 `SymPy` 定义*符号*（`symbols`）和带符号的表达式.

假如我们想要定义 $x+2y$，我们首先需要将 $x$ 和 $y$ 定义为 `symbols`。

```py
>>> from sympy import symbols
>>> x, y = symbols('x y')
```

这样 `SymPy` 在进行运算的时候就不会把 `x` 和 `y` 作为带有数值的变量，而是当作符号参加运算。更具体地来讲，现在 `x` 变量的值是 `x` 这个数学符号，而 `y` 变量的值是 `y` 这个数学符号。变量和它对应数学符号的名字不需要相同，你甚至可以反其道行之，把 `x` 和 `y` 的变量名和符号名交换着来定义。

接下来我们定义 $x+2y$ 的表达式并输出：

```py
>>> f = x + 2*y
>>> expr
x + 2*y
```

<br>

> _*Example 2.2*_
>
> 按照喜好对 1.2.1 中的表达式进行四则运算、乘方、开方等变换；并通过 `sympy.expand` 和 `sympy.factor` 进行表达式展开和因式分解.

在这里，假设我们想在 $(x+2*y)$ 前面乘一个 $x$，我们只需要：

```py
>>> g = x*f
>>> g
x*(x + 2*y)
```

注意`SymPy`在默认情况下不会自动化简你的操作。也就是说，你可以以极高的自由度对这些表达式进行变换。比如说，我们可以用 `expand` 来对 `g` 进行括号展开：

```py
>>> from sympy import expand
>>> expanded_g = expand(g)
>>> expanded_g
x**2 + 2*x*y
```

相似地，假如我们想对式子进行因式分解，只需要使用 `factor` 函数：

```py
>>> from sympy import factor
>>> factor(expanded_g)
x*(x + 2*y)
```

在 JI 的荣誉高等数学 (II) 课上，霍伯格教授侧重分析，而略过了在实际工程中有用的极限、积分计算。使用 `SymPy` 库，我们可以轻松地列出多变量表达式，对其进行求全导、偏导、定积分、不定积分或者求其在某处的极限。详见 [Calculus - SymPy 1.14.0 documentation](https://docs.sympy.org/latest/tutorials/intro-tutorial/calculus.html).

同样地，在屈子杰教授和门德斯教授的荣誉物理 (I) 中，不会解微分方程也是常有的痛点。`Sympy` 库的功能甚至涵盖解线性方程组、一元高次方程、微分方程、求矩阵的特征值。入门详见 [Solving Equations - SymPy 1.14.0 documentation](https://docs.sympy.org/latest/guides/solving/index.html#solving-guide)、说明文档详见 [Solvers - SymPy 1.14.0 documentation](https://docs.sympy.org/latest/tutorials/intro-tutorial/solvers.html).

由于时间关系，上述内容在此不做过多赘述，感兴趣的听众可以自行翻阅说明文档进行查看。

<br>

# Exercise 1 — 在 VP141 Lab 2 中使用 `SymPy` 辅助不确定性量化

在物理实验中对同一物理量进行多次测量时，每个测量值之间都会有微小的变化。我们可以用*不确定度* $U$ 来表征这组数据的不能确定的范围。

不确定度的产生分为两种情况：人为（如估读、甚至是心情不好）因素产生的误差，和仪器本身测量时会产生的误差。我们分别称其为 _A 类_ 和 _B 类不确定度_.

<br>

> _Exercise 1.1_ — 多次测量时的不确定性量化
>
> 假设在某次实验中，我们对物理量 $x$ 进行了 $N=6$ 次测量，其中第 $i$ 次测量的测量值是 $x_i,\ i\in\{1,\dots,6\}$. $\text{\ \ }$已知该测量方式所能产生的最大绝对偏差，即 B 类不确定度为 $\Delta_\text{dev}$；置信度为 95% 时，六次测量的分布因子为 $t_{0.95,\ 6}=2.57$.
>
> 请定量分析 $x$ 的不确定性.

首先我们求出这组数据的算术平均值 $\bar{x}$：

$$\bar{x}=\frac{1}{N}\sum_{i=1}^Nx_i.$$

接下来通过统计学方法，我们可以得出这组数据的标准差 $s_x$：

$$s_x=\sqrt{\frac{1}{N}\sum_{i=1}^N(x_i-\bar{x})^2}.$$

我们可以得出这组数据的 A 类不确定度 $U_{x,A}$：

$$U_{x,A}=s_x\cdot\frac{t_{0.95,\ 6}}{\sqrt{N}}.$$

结合仪器测量的最大误差 $U_{x,B}=\Delta_{\text{dev}}$，我们使用平方开根合成，得到 $x$ 的不确定性 $U_x$：

$$U_x=\sqrt{U_{x,A}^2+U_{x,B}^2}.$$

这一部分的代码可以用 `NumPy` 实现，在此不做赘述。

<br>

> _Exercise 1.2_ — 合成标准不确定度
>
> 假设在某次物理实验中，我们被要求用公式
> $$\rho=\frac{m}{\pi r^2h}$$
> 测量某个圆柱体（可能是导线）的密度.$\text{\ \ }$现在我们通过 3.1 中给出的方法分别得到了 $m,\ r,\ h,\ U_m,\ U_r,\ U_h$.
>
> 请定量分析 $\rho$ 的合成标准不确定度 $U_\rho$.

首先我们将这六个值定义为 `sympy.symbols`：

```py
>>> from sympy import symbols
>>> m, r, h, u_m, u_r, u_h = symbols('m r h u_m u_r u_h')
```

设
$$\rho(m,r,h)=\frac{m}{\pi r^2h}.$$
我们在 Python 中将这一函数关系也定义出来：

```py
>>> from sympy import pi # 从 SymPy 库中导入圆周率常数 pi
>>> rho = m / (pi * r**2 * h)
```

由于 $m,r,h$ 的测量彼此独立，我们可以通过以下公式计算 $\rho$ 的合成标准不确定度 $U_\rho$：
$$U_\rho=\sqrt{(\frac{\partial\rho}{\partial m})^2U_m^2+(\frac{\partial\rho}{\partial r})^2U_r^2+(\frac{\partial\rho}{\partial h})^2U_h^2}.$$

我们可以通过 `SymPy` 的偏导函数自动求出 $\rho$ 关于 $x, m, h$ 的偏导，并通过这些偏导数得到 $U_\rho$：

```py
>>> from sympy import diff, sqrt
>>> drho_dm = diff(rho, m)
>>> drho_dr = diff(rho, r)
>>> drho_dh = diff(rho, h)
>>>
>>> u_rho = sqrt(
        (drho_dm*u_m)**2 +
        (drho_dr*u_r)**2 +
        (drho_dh*u_h)**2
    )
```

接下来，我们通过 Python 中的字典数据结构将我们的测量数据和之前的符号变量对应起来，并代入上面计算得到的 `rho` 和`u_rho`：

```py
>>> data = {
        m:   __,   r: __,   h: __,
        u_m: __, u_r: __, u_h: __
    }
# 在下划线中填入测量和计算得到的结果

>>> val_rho = rho.subs(data).evalf()
>>> val_u_rho = u_rho.subs(data).evalf()
# .subs()  用来代入数据
# .evalf() 用来将结果转换为浮点数
```

<br>

# Exercise 2 — 在 VP150/VP160 中使用 `SymPy` 求受迫阻尼振动的振子运动轨迹的解析解

为了展示 `SymPy` 对于我们苦逼工科生来说有多有用，下面我们结合我们都最喜欢的振动，给一个硬核一点的例子：

<br>

> _Exercise 2_ — 求受迫阻尼振动的振子运动轨迹的解析解
>
> 现在在一维空间中有一个做受迫阻尼运动的弹簧振子，振子质量为 $m$，阻尼系数为 $c$，弹簧劲度系数为 $k$.
>
> 驱动力大小可以表示为时间的方程 $F=F_0\cos(\omega t)$，其中 $F_0$ 为最大驱动力，$\omega$ 为驱动力的角频率.
>
> 求该振子位移随时间变化的函数的通解.

根据牛顿第二定律，我们可以将位移 $x$、速度 $\dot{x}$ 和加速度 $\ddot{x}$ 表示为二阶线性非齐次常微分方程：

$$
\begin{align*}
&& m\ddot{x}&=F_0\cos(\omega t)-c\dot x-kx.\\
\iff&& \ddot{x}&=\frac{F_0}{m}\cos(\omega t)-\frac{c}{m}\dot x-\frac{k}{m}x.\\
:\iff&& \ddot{x}&=f_0\cos(\omega t)-2\beta\dot x-\omega_0^2x,
\end{align*}
$$

其中为简化计算，我们定义了阻尼因子 $\beta=\frac{c}{2m}$；单位质量驱动力 $f_0=\frac{F_0}{m}$ 弹簧振子的固有频率 $\omega_0=\sqrt\frac{k}{m}$。

值得注意的是，这里 $x$ 是一个关于时间的隐函数，即 $x(t)$ 的简写，其中 $t$ 为时间。我们不能把 $x$ 当作一个变量，而是一个函数来喂给 `SymPy`。我们还需要声明 $x$ 的定义域为 $\mathbb{R}$，即 $t$ 为实数：

```py
>>> from sympy import symbols, Function
>>>
>>> t = symbols('t', real=True)
>>> omega0, beta, omega, f0 = symbols('omega0 beta omega f0', positive=True)
>>> x = Function('x')
```

将上述微分方程展开：
$$\frac{\text{d}^2}{\text{d}t^2}{x(t)}=f_0\cos(\omega t)-2\beta\cdot\frac{\text{d}}{\text{d}t}{x(t)}-\omega_0^2\cdot x(t),$$
并在 `SymPy` 中定义该微分方程：

```py
>>> from sympy import Eq, diff, cos
>>>
>>> ode = Eq( x(t).diff(t,2), f0*cos(omega*t) - 2*beta*x(t).diff(t) - omega0**2*x(t) )
```

使用 `SymPy` 中的 `init_printing(use_unicode=True)` 设置，我们可以用 Unicode 字符美观地输出我们的变量和方程们：

```py
>>> from sympy import init_printing
>>> init_printing(use_unicode=True)
>>>
>>> ode
  2
 d                d                          2
───(x(t)) = - 2⋅β⋅──(x(t)) + f₀⋅cos(ω⋅t) - ω₀ ⋅x(t)
  2               dt
dt
```

直接使用 `SymPy` 中求解微分方程的功能即可得到 `ode` 的通解：

```py
>>> from sympy import dsolve
>>> sol = dsolve(ode, x(t))
```

输出结果如下：

![](https://cdn.picui.cn/vip/2025/07/12/68716abe5d574.png)

<br>

# 参考文献

- [SymPy 1.14.0 Documentation](https://docs.sympy.org/latest/index.html)
- [slides -- uncertainty analysis [rev.2.4]](https://oc.sjtu.edu.cn/courses/80071/files/folder/handbooks%20and%20tutorials?preview=11165223)
