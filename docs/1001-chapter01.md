# 调节与中介分析 {#mome}



## 线性关系 {#linear}

### 调节与中介效果的检验步骤 {#momesteps}

自Baron和Kenny(1986)关于调节与中介效果的检验经典文章以来，调节与中介效果的检验已经非常成熟，甚至现在有所谓的调节的中介与中介的调节。

社会科学尤其是管理学，未来以后肯定会有大的方法上的突破，现有的管理学研究范式已经进入了相对瓶颈期。鉴于目前国内期刊上很多关于中介与调节的检验不是很正确，下文给出相对正确的检验步骤。

**调节效果的检验步骤**：

* 第一步，如果有控制变量，先放入控制变量与结果变量进行回归；
* 第二步，将自变量与控制变量一起与结果变量进行回归；
* 第三步，将控制变量、自变量、调节变量一起与结果变量进行回归；
* 第四步，将控制变量、自变量、调节变量、自变量与调节变量的乘积项一起与结果变量进行回归，自变量与调节变量的乘积项显著，则调节作用存在。

需要注意的是：在验证调节作用时，通常需要将自变量与调节变量中心化以降低共线性的可能性。

**中介效果的检验步骤**：

最有效最直接也最简单的方法是使用Bootstrapping，诸如SPSS、Mplus等软件都可以轻易实现，便捷高效。

**以下列出我所认为相对不错的中文论文供大家参考**：

[1] 方杰, 张敏强, 李晓鹏. 中介效应的三类区间估计方法[J]. 心理科学进展, 2011, 19(5):765-774.

[2] 刘冰, 齐蕾, 徐璐. 棍棒之下出“孝子”吗——员工职场偏差行为研究[J]. 南开管理评论, 2017, 20(3):182-192.

[3] 芦谢峰, 韩立敏. 中介变量、调节变量与协变量——概念、统计检验及其比较[J]. 心理科学, 2007, 30(4):934-936.

[4] 温忠麟, 张雷, 侯杰泰. 有中介的调节变量和有调节的中介变量[J]. 心理学报, 2006, 38(3):448-452.

其中，刘冰等(2017)这篇文章关于调节与中介的检验较为标准，所以我列出供大家参照借鉴。


### 管理学研究常用调节效应检验Mplus Code {#mocode}

1. 只有一个调节变量

![](figs/1111.png)

![](figs/1112.png)

```
USEVARIABLE = X W Y XW;
DEFINE:
  XW = X*W;
ANALYSIS:
  TYPE = GENERAL;
  ESTIMATOR =  ML;
  BOOTSTRAP = 5000;
MODEL:
  [Y](b0);
  Y ON X(b1);
  Y ON W(b2);
  Y ON XW(b3);
MODEL CONSTRAINT:
  NEW(LOW_W HIGH_W SIMP_LO SIMIP_HI DIFF);
  LOW_W = #LOWW; ! replace #LOWW in the code with your chosen low value of W
  HIGH_W = #HIGHW; ! replace #HIGHW in the code with your chosen high value of W
! Now calculate simple slopes for each value of W
  SIMP_LO = b1 + b3*LOW_W;
  SIMP_HI = b1 + b3*HIGH_W;
  DIFF = SIMP_HI - SIMP_LO;
OUTPUTE:
  STAND CINT(bcbootstrap);
```

2. 存在两个并列的调节变量

![](figs/1113.png)

![](figs/1114.png)
```
USEVARIABLES = X W Y XW XZ;
DEFINE: 
  XW = X*W; 
  XZ = X*Z;
ANALYSIS: 
  TYPE = GENERAL; 
  ESTIMATOR = ML; 
  BOOTSTRAP = 5000;
MODEL:
  [Y](b0); 
  Y ON X(b1); 
  Y ON W(b2); 
  Y ON Z(b3); 
  Y ON XW(b4); 
  Y ON XZ(b5);
 MODEL CONSTRAINT: 
   NEW(LOW_W HIGH_W LOW_Z HIGH_Z LOW_LOZ HIW_LOZ LOW_HIZ HIW_HIZ DIFF1 DIFF2 DIFF3 DIFF4 DIFF5 DIFF6);
   LOW_W = #LOWW; ! replace #LOWW in the code with your chosen low value of W 
   HIGH_W = #HIGHW; ! replace #HIGHW in the code with your chosen high value of W
   LOW_Z = #LOWZ; ! replace #LOWZ in the code with your chosen low value of Z
   HIGH_Z = #HIGHZ; ! replace #HIGHZ in the code with your chosen high value of Z
 ! Now calc simple slopes for each value of W and Z
   LOW_LOZ = b1 + b4*LOW_W + b5*LOW_Z;
   HIW_LOZ = b1 + b4*HIGH_W + b5*LOW_Z;
   LOW_HIZ = b1 + b4*LOW_W + b5*HIGH_Z;
   HIW_HIZ = b1 + b4*HIGH_W + b5*HIGH_Z;
   DIFF1 = LOW_LOZ - HIW_LOZ;
   DIFF2 = LOW_LOZ - LOW_HIZ;
   DIFF3 = LOW_LOZ - HIW_HIZ;
   DIFF4 = HIW_LOZ - LOW_HIZ;
   DIFF5 = HIW_LOZ - HIW_HIZ;
   DIFF6 = LOW_HIZ - HIW_HIZ;
OUTPUT: 
  STAND CINT(bcbootstrap);
```

3. 调节的调节效应

![](figs/1115.png)

![](figs/1116.png)

```
USEVARIABLES = X W Y XW XZ WZ XWZ;
DEFINE: 
  XW = X*W; 
  XZ = X*Z; 
  WZ = W*Z; 
  XWZ = X*W*Z;
ANALYSIS: 
  TYPE = GENERAL; 
  ESTIMATOR = ML; 
  BOOTSTRAP = 5000;
MODEL: 
  [Y] (b0); 
  Y ON X(b1); 
  Y ON W(b2); 
  Y ON Z(b3); 
  Y ON XW(b4); 
  Y ON XZ(b5); 
  Y ON WZ(b6); 
  Y ON XWZ(b7);
  MODEL CONSTRAINT: 
  NEW(LOW_W HIGH_W LOW_Z HIGH_Z LOW_LOZ HIW_LOZ LOW_HIZ HIW_HIZ DIFF1 DIFF2 DIFF3 DIFF4 DIFF5 DIFF6);
  LOW_W = #LOWW; ! replace #LOWW in the code with your chosen low value of W
  HIGH_W = #HIGHW; ! replace #HIGHW in the code with your chosen high value of W
  LOW_Z = #LOWZ; ! replace #LOWZ in the code with your chosen low value of Z
  HIGH_Z = #HIGHZ; ! replace #HIGHZ in the code with your chosen high value of Z
! Now calc simple slopes for each value of W and Z
  LOW_LOZ = b1 + b4*LOW_W + b5*LOW_Z + b7*LOW_W*LOW_Z;
  HIW_LOZ = b1 + b4*HIGH_W + b5*LOW_Z + b7*HIGH_W*LOW_Z;
  LOW_HIZ = b1 + b4*LOW_W + b5*HIGH_Z + b7*LOW_W*HIGH_Z;
  HIW_HIZ = b1 + b4*HIGH_W + b5*HIGH_Z + b7*HIGH_W*HIGH_Z;
   DIFF1 = LOW_LOZ - HIW_LOZ;
   DIFF2 = LOW_LOZ - LOW_HIZ;
   DIFF3 = LOW_LOZ - HIW_HIZ;
   DIFF4 = HIW_LOZ - LOW_HIZ;
   DIFF5 = HIW_LOZ - HIW_HIZ;
   DIFF6 = LOW_HIZ - HIW_HIZ;
OUTPUTE:
  STAND CINT(bcbootstrap);
```

### 常见中介效应 {#normme}

**重点**：

首先介绍只有一个中介变量的单中介模型；然后介绍具有多个中介变量的并列中介模型；最后介绍具有多个中介变量的中介链模型。

需要说明的是，以上中介效应模型的分类名称是我结合自身的理解命名，大家可能不太熟悉，明白就好。比如`链式中介`、`连续中介`等，其实就是我所阐述的`中介链`。

开始之前，还想结合阅读的文献以及自己的理解，想跟大家阐述一下间接效应与中介效应的区别。

简言之，间接效应(Indirect Effect)实质上等同于中介效应(Mediation Effect)。只不过提及间接效应的假设时，通常不会提主效应(X→Y)，只会提X→M→Y的关系假设。此外，心理统计类论文通常只会出现indirect effect，而不会出现mediation effect。~~我的推测是，当有direct effect的说法后，说indirect effect可能更直觉。~~

1. 单中介模型的Mplus code

![](figs/1131.png)

![](figs/1132.png)

```
USEVARIABLES = X M Y;

ANALYSIS:
  TYPE = GENERAL;
  ESTIMATOR = ML;
  BOOTSTRAP = 10000;

! In model statement name each path using parentheses
MODEL:
  Y ON M (b1);
  Y ON X (cdash); ! direct effect of X on Y
  M ON X (a1);
! Use model constraint to calculate indirect and total effect
  MODEL CONSTRAINT:
  NEW(a1b1 TOTAL);
    a1b1 = a1*b1; ! Indirect effect of X on Y via M
    TOTAL = a1*b1 + cdash; ! Total effect of X on Y

OUTPUT:
  STAND CINT(bcbootstrap);
```

2. 并列中介模型的Mplus code

以常见的双重并列中介为例：

![](figs/1133.png)

![](figs/1134.png)

```
USEVARIABLES = X M1 M2 Y;

ANALYSIS:
  TYPE = GENERAL;
  ESTIMATOR = ML;
  BOOTSTRAP = 10000;

! In model statement name each path using parentheses
MODEL:
  Y ON M1 (b1);
  Y ON M2 (b2);
  Y ON X (cdash); ! direct effect of X on Y
  M1 ON X (a1);
  M2 ON X (a2);
! Use model constraint to calculate indirect and total effect
  MODEL CONSTRAINT:
  NEW(a1b1 a2b2 TOTAL);
    a1b1 = a1*b1; ! Indirect effect of X on Y via M1
    a2b2 = a2*b2; ! Indirect effect of X on Y via M2
    TOTAL = a1*b1 + a2*b2 + cdash; ! Total effect of X on Y

OUTPUT:
  STAND CINT(bcbootstrap);
```

3. 中介链模型的Mplus code


![](figs/1135.png)

![](figs/1136.png)

```
USEVARIABLES = X M1 M2 Y;

ANALYSIS:
  TYPE = GENERAL;
  ESTIMATOR = ML;
  BOOTSTRAP = 10000;

! In model statement name each path usingparentheses
MODEL:
  Y ON X (cdash); ! direct effect of X on Y
  Y ON M1 (b1);
  Y ON M2 (b2);
  M1 ON X (a1);
  M2 ON X (a2);
  M2 ON M1 (d1);
! Use model constraint to calculate specific indirectpaths and total indirect effect
  MODEL CONSTRAINT:
  NEW(a1b1 a2b2 a1d1b2 TOTALIND TOTAL);
    a1b1 = a1*b1; ! Specific indirect effect of X on Y via M1
    a2b2 = a2*b2; ! Specific indirect effect of X on Y via M2
    a1d1b2 = a1*d1*b2; ! Specific indirect effect of X on Y via M1 andM2
    TOTALIND = a1*b1 + a2*b2 + a1*d1*b2; ! Total indirect effect of X onY via M1, M2
    TOTAL = a1*b1 + a2*b2 + a1*d1*b2 + cdash; ! Total effect of X on Y

OUTPUT:
  STAND CINT(bcbootstrap);
```

### 第一阶段有调节的中介、第二阶段有调节的中介、两阶段有调节的中介 {#firstmome}

本文主要介绍了第一阶段有调节的中介效应(first-stage moderated mediation)、第二阶段有调节的中介效应(second-stage moderated mediation)和两阶段有调节的中介效应(dual-stage moderated mediation)的统计学原理，并将统计学逻辑通过Mplus软件写出Code出来。

通过一份原始数据，手把手带你写出上述的三类有调节的中介效应。原始数据和Mplus Code在文末的链接中。

1 第一阶段有调节的中介效应

1.1 原理

第一阶段有调节的中介效应是指$x$→$m$→$y$的中介效应被调节变量$w$所调节了，且调节效应发生在$x$→$m$之间。概念模型如下图所示：

![](figs/1141.png)

首先构建方程(1)：

$$
m=a_{0}+a_{1}x+a_{2}w+a_{3}xw(1)
$$

其次构建方程(2)：

$$
y=b_{0}+b_{1}m+c^{'}x(2)
$$

将方程(1)带入(2)：

$$
y=b_{0}+b_{1}(a_{0}+a_{1}x+a_{2}w+a_{3}xw)+c^{'}x(3)
$$

整理得：

$$
y=(b_{0}+a_{0}b_{1}+a_{2}b_{1}w)+(a_{1}b_{1}+a_{3}b_{1}w+c^{'})x
$$

我们只关心一次项系数$a_{1}b_{1}+a_{3}b_{1}w+c^{'}$，其中$a_{1}b_{1}+a_{3}b_{1}w$就是第一阶段有调节的中介效应，系数$c^{'}$是$x$→$y$的直接效应。

本质上，第一阶段有调节的中介效应可以通过偏导数求得：

首先对方程(1)求偏导：

$$
\frac{\partial m}{\partial x}=a_{1}+a_{3}w
$$

对方程(2)求偏导: 

$$
\frac{\partial y}{\partial x}=b_{1}
$$

所以第一阶段有调节的中介效应为：

$$
\frac{\partial m}{\partial x} {\times} \frac{\partial y}{\partial x}=(a_{1}+a_{3}w)b_{1}
$$

1.2 实例

研究假设模型如下：

![](figs/1142.png)

Depressed Affect在Economic Stress和Withdrawal
Intentions中起中介作用，Entrepreneurial self-efficacy扮演了第一阶段的有中介的调节作用。

`关于本文所用原始数据来源说明`：

estress.sav和estress.csv，Mplus以estress.csv进行分析。原始数据来源于：

Pollack, J. M., Vanepps, E. M., & Hayes, A. F. The moderating role of social ties on entrepreneurs’ depressed affect and withdrawal intentions in response to economic stress[J]. Journal of Organziational Behavior, 2012, 33: 789-810.

因为作者提供的原始数据与文章有出入，所以我根据本推文所要解决的问题分别构建了三个研究假设模型。可能结果不会显著，但我只是利用数据来练习本文所要阐述的研究方法。

原始数据的变量如下：

| 变量类型| 描述| 
|:------|:-----------|
| 控制变量| tenure sex age |
| 自变量  | estress(Economic Stress,其中心化变量为estress_mc) |
| 调节变量| ese(Entrepreneurial self-efficacy,其中心化变量为ese_mc) |
| 中介变量| affect(Depressed Affect,其中心化变量为affect_mc) |
| 因变量  | withdraw(Withdrawal Intentions) |

其他两个所构建的假设模型使用的数据也是这一数据。

关于原始数据中的`控制变量`：

控制变量的作用在于控制一些潜在的因素对你所要探究的变量所造成的影响。其实，控制变量既可以对中介变量进行回归(即认为控制变量会对中介变量产生影响)，也可以对因变量进行回归(即认为控制变量会对因变量产生影响)。到底对中介变量还是因变量回归，是根据理论和你的argument所决定的。

在接下来的分析中，我将原始数据中的控制变量认为会对中介变量造成影响。

Mplus软件对应第一阶段有调节的中介效应语法如下：

```
TITLE: first-stage moderated mediation;

DATA: FILE = estress.csv;

VARIABLE:
NAMES = tenure estress affect withdraw sex age ese estress_mc affect_mc ese_mc;

USEVAR = tenure sex age estress_mc affect_mc withdraw ese_mc interaction; !放入新生成变量

DEFINE:     !定义交互项
interaction = estress_mc*ese_mc; 

ANALYS:
TYPE = GENERAL;
ESTIMATOR = ML;
BOOTSTRAP = 1000;

MODEL:
affect_mc ON tenure
             sex
             age
             estress_mc(a1)
             ese_mc(a2)
             interaction(a3);

withdraw ON affect_mc(b1);
            estress_mc;

withdraw ON affect_mc(b1);
            estress_mc;

MODEL CONSTRAINT:
NEW(LOW HIGH DIFF);
LOW = (a1 + a3*(-0.94464))*b1;     !调节变量为低时
HIGH = (a1 + a3*0.94464)*b1;       !调节变量为高时
DIFF = HIGH - LOW;

OUTPUT:
SAMPSTAT STDYX CINTERVAL(BCBOOTSTRAP);
```

输出结果如下：

![](figs/1143.png)

我们主要关心画红线处新生成的三个参数的显著性，可以看出在ese_mc为低时，estress_mc→affect_mc→withdraw的中介效应显著(Index=0.218, p<0.001)；在ese_mc为高时，estress_mc→affect_mc→withdraw的中介效应不显著(Index=0.051, p>0.1)；ese_mc为高和为低时，中介效应的差异显著(Index=-0.167, p<0.1)。

一般情况下，我们比较关心上述结果的95%置信区间，在下图中体现：

![](figs/1144.png)

然后，我们可以将需要的结果复制到EXCEL中进行整理，然后再粘贴到Word中，形成期刊所需要的格式。

2 第一阶段有调节的中介效应

2.1 原理

第二阶段有调节的中介效应是指$x$→$m$→$y$的中介效应被调节变量$w$所调节了，且调节效应发生在$m$→$y$之间。概念模型如下图所示：

![](figs/1145.png)

首先构建方程(1)：

$$
m=a_{0}+a_{1}x(1)
$$

其次构建方程(2)：

$$
y=b_{0}+b_{1}m+b_{2}w+b_{3}mw+c^{'}x(2)
$$

将方程(1)带入(2)：

$$
y=b_{0}+b_{1}(a_{0}+a_{1}x)+b_{2}w+b_{3}(a_{0}+a_{1}x)w+c^{'}x(3)
$$

整理得：

$$
y=(b_{0}+a_{0}b_{1}+b_{2}w+a_{0}b_{3}w)+(a_{1}b_{1}+a_{1}b_{3}w+c^{'})x
$$

我们只关心一次项系数$a_{1}b_{1}+a_{1}b_{3}w+c^{'}$，其中$a_{1}b_{1}+a_{1}b_{3}w$=$a_{1}(b_{1}+b_{3}w)$就是第二阶段有调节的中介效应，系数$c^{'}$是$x$→$y$的直接效应。

本质上，第二阶段有调节的中介效应可以通过偏导数求得：

首先对方程(1)求偏导：

$$
\frac{\partial m}{\partial x}=a_{1}
$$

对方程(2)求偏导: 

$$
\frac{\partial y}{\partial x}=b_{1}+b_{3}w
$$

所以第二阶段有调节的中介效应为：

$$
\frac{\partial m}{\partial x} {\times} \frac{\partial y}{\partial x}=a_{1}(b_{1}+b_{3}w)
$$

2.2 实例

研究假设模型如下：

![](figs/1146.png)

Depressed Affect在Economic Stress和Withdrawal Intentions中起中介作用，Entrepreneurial self-efficacy扮演了第二阶段的有中介的调节作用。

Mplus软件对应第二阶段有调节的中介效应语法如下：

```
TITLE: second-stage moderated mediation;

DATA: FILE = estress.csv;

VARIABLE:
NAMES = tenure estress affect withdraw sex age ese estress_mc affect_mc ese_mc;

USEVAR = tenure sex age estress_mc affect_mc withdraw ese_mc interaction; !定义新生成变量

DEFINE:      !定义交互项
interaction = affect_mc*ese_mc;

ANALYS:
TYPE = GENERAL;
ESTIMATOR = ML;
BOOTSTRAP = 1000;

MODEL:
affect_mc ON tenure
             sex
             age
             estress_mc(a1);

withdraw ON affect_mc(b1)
            ese_mc(b2)
            interaction(b3)
            estress_mc;

MODEL CONSTRAINT:
NEW(LOW HIGH DIFF);
LOW = a1*(b1 + b3*(-0.94464));       !当调节变量为低时
HIGH = a1*(b1 + b3*(0.94464));       !当调节变量为高时
DIFF = HIGH - LOW;

OUTPUT:
SAMPSTAT STDYX CINTERVAL(BCBOOTSTRAP);
```

输出结果如下：

![](figs/1147.png)

![](figs/1148.png)

由上面的两张图片的结果可以看出，在ese_mc为低时，estress_mc→affect_mc→withdraw的中介效应显著(Index=0.110, p<0.01)，95%CI=[0.048, 0.183]；在ese_mc为高时，estress_mc→affect_mc→withdraw的中介效应显著(Index=0.152, p<0.001), 95%CI=[0.089, 0.243]；ese_mc为高和为低时，中介效应的差异不显著(Index=0.042, p>0.1), 95%CI=[-0.018, 0.116]。

3 第一阶段有调节的中介效应

3.1 原理

第二阶段有调节的中介效应是指$x$→$m$→$y$的中介效应被调节变量$w$所调节了，且调节效应发生在$x$→$m$和$m$→$y$之间。概念模型如下图所示：

![](figs/1149.png)

首先构建方程(1)：

$$
m=a_{0}+a_{1}x+a_{2}w+a_{3}xw(1)
$$

其次构建方程(2)：

$$
y=b_{0}+b_{1}m+b_{2}w+b_{3}mw+c^{'}x(2)
$$

将方程(1)带入(2)：

$$
y=b_{0}+b_{1}(a_{0}+a_{1}x+a_{2}w+a_{3}xw)+b_{2}w+b_{3}(a_{0}+a_{1}x+a_{2}w+a_{3}xw)w+c^{'}x(3)
$$

整理得：

$$
y=(b_{0}+a_{0}b_{1}+a_{2}b_{1}w+b_{2}w+a_{0}b_{3}w+a_{2}b_{3}w^{2})+(a_{1}b_{1}+a_{3}b_{1}w+a_{1}b_{3}w+a_{3}b_{3}w^{2}+c^{'})x
$$

我们只关心一次项系数$a_{1}b_{1}+a_{3}b_{1}w+a_{1}b_{3}w+a_{3}b_{3}w^{2}+c^{'}$，其中，$a_{1}b_{1}+a_{3}b_{1}w+a_{1}b_{3}w+a_{3}b_{3}w^{2}$=$(a_{1}+a_{3}w)(b_{1}+b_{3}w)$是两阶段有调节的中介效应，系数$c^{'}$是$x$→$y$的直接效应。

本质上，两阶段有调节的中介效应可以通过偏导数求得：

首先对方程(1)求偏导：

$$
\frac{\partial m}{\partial x}=a_{1}+a_{3}w
$$

对方程(2)求偏导: 

$$
\frac{\partial y}{\partial x}=b_{1}+b_{3}w
$$

所以两阶段有调节的中介效应为：

$$
\frac{\partial m}{\partial x} {\times} \frac{\partial y}{\partial x}=(a_{1}+a_{3}w)(b_{1}+b_{3}w)
$$

3.2 实例

研究假设模型如下：

![](figs/11410.png)

Depressed Affect在Economic Stress和Withdrawal Intentions中起中介作用，Entrepreneurial self-efficacy扮演了两阶段的有中介的调节作用。

Mplus软件对应两阶段有调节的中介效应语法如下：

```
TITLE: dual-stage moderated mediation;

DATA: FILE = estress.csv;

VARIABLE:
NAMES = tenure estress affect withdraw sex age ese estress_mc affect_mc ese_mc;

USEVAR = tenure sex age estress_mc affect_mc withdraw ese_mc int1 int2; !放入新生成变量

DEFINE:       !定义交互项
int1 = estress_mc*ese_mc;
int2 = affect_mc*ese_mc;

ANALYS:
TYPE = GENERAL;
ESTIMATOR = ML;
BOOTSTRAP = 1000;

MODEL:
affect_mc ON tenure
             sex
             age
             estress_mc(a1)
             ese_mc(a2)
             int1(a3);

withdraw ON affect_mc(b1)
            ese_mc(b2)
            int2(b3)
            estress_mc;

MODEL CONSTRAINT:
NEW(LOW HIGH DIFF);
LOW = (a1+a3*(-0.94464))*(b1+b3*(-0.94464));     !调节变量为低时
HIGH = (a1 + a3*0.94464) * (b1 + b3*0.94464);    !调节变量为高时
DIFF = HIGH - LOW;

OUTPUT:
SAMPSTAT STDYX CINTERVAL(BCBOOTSTRAP);
```

结果输出如下：

![](figs/11411.png)

![](figs/11412.png)

由上面的两张图片的结果可以看出，在ese_mc为低时，estress_mc→affect_mc→withdraw的中介效应显著(Index=0.189, p<0.01)，95%CI=[0.084, 0.330]；在ese_mc为高时，estress_mc→affect_mc→withdraw的中介效应不显著(Index=0.061, p>0.1), 95%CI=[-0.045, 0.163]；ese_mc为高和为低时，中介效应的差异不显著(Index=-0.128, p>0.1), 95%CI=[-0.342, 0.020]。

### 单层次有中介的调节效应 {#memo}

1 Tpye I Mediated Moderation

1.1 统计方程式

![](figs/1151.png)

$$
m=a_{0}+a_{1}x+a_{2}w+a_{3}xw(1)
$$

$$
y=b_{0}+b_{1}x+b_{2}m+b_{3}w+b_{4}xw(2)
$$

将(1)带入(2)，整理得：

$$
y=(b_{0}+a_{0}b_{2})+(b_{1}+a_{1}b_{2})x+(a_{2}b_{2}+b_{3})w+(b_{4}+a_{3}b_{2})xw(3)
$$

其中，$b_{4}$为调节效应，$a_{3}b_{2}$为有中介的调节效应。

1.2 在Mplus中的实现

```
TITLE:
Tpye I Mediated Moderation;

DEFINE:
xw=(x-3)*(w-4); !x和w的中心化后的相互项 
centering x(grandmean); 
centering m(grandmean); 
centering w(grandmean); 

DATA:
file = example.csv;

VARIABLE:
names = x m w y;
usevar = x m w y xw;

ANALUSIS：
bootstrap = 10000;

MODEL:
m on x w
     xw(a);
y on m(b)
     x w xw;

MODEL CONSTRAINT:
new(ind);
ind = a*b;

OUTPUTE:
sampstat;
cinterval(bcbootstrap);
```
2 Tpye II Mediated Moderation

2.1 统计方程式

![](figs/1152.png)

```
TITLE:
Tpye II Mediated Moderation;

DEFINE:
xw=(x-3)*(w-4); !x和w的中心化后的相互项 
xm=(x-3)*(m-2.5); !x和m的中心化后的相互项 
centering x(grandmean); 
centering m(grandmean); 
centering w(grandmean); 

DATA:
file = example.csv;

VARIABLE:
names = x m w y;
usevar = x m w y xw xm;

ANALUSIS：
bootstrap = 10000;

MODEL:
m on w(a)
     x;
y on xm(b)
     m x w xw;

MODEL CONSTRAINT:
new(ind);
ind = a*b;

OUTPUTE:
sampstat;
cinterval(bcbootstrap);
```

参考文献：

刘东,张震,汪默(2012). 被调节的中介和被中介的调节：理论构建与模型检验. 见: 陈晓萍, 徐淑英, 樊景立(主编), 组织与管理研究的实证方法(pp.553-587). 北京: 北京大学出版社. 

## 非线性关系 {#nonlinear}















## 绘制调节效应图 {#modplot}

### 简单斜率和调节效应图系列：SPSS+Mplus+Excel策略



<iframe width="560" height="315" src="https://www.bilibili.com/video/BV1ga4y1e7wZ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
