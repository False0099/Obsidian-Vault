我们的目标是找到我们的函数的最小值对应的 $x$,于是我们就可以把每一个 $x$ 对应的 y 叫做我们的**适应度**。

我们的遗传算法中存在下面的几个概念：
1. 初始化：


## 输出话：
我们首先向我们的数轴上生成一群或者一个点，我们把这些点叫做我们的**初始种群**

## 交叉：
我们的交叉就是在我们的现有个体的基础上进行小幅度的变动。

## 变异：
我们的编译就是随机对于我们额某一些点进行大幅度的变动。我们变异是为了保证我们不**过快的落入局部最优解**。

## 选择：
我们的选择就是以一定的方法选择比较好的七个或者新的点作为我们的**新一代种群**。作为我们的**新一轮初始种群**。

注意我们按照这一种算法求出来的解不能成为我们的**最优解**，只能称为一个**近优解**。