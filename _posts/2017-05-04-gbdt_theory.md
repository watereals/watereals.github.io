---
layout: default
title: 梯度提升决策树(GBDT)
mathjax: true
---

# 梯度提升算法

来源：https://github.com/liudragonfly/GBDT

Freidman提出了梯度提升算法，该方法是利用最速下降法的近似方法，其关键是利用损失函数的负梯度在当前模型的值

$$ -[{\partial L(y,f(x_i)) \over \partial f(x_i)}]_{f(x) = f_{m-1}(x)} $$

作为回归问题算法中的残差的近似值，拟合一个回归模型。

其算法流程如下：

1. $F_0(x) = argmin_\rho \sum _{i=1}^N L(y_i, \rho)$
2. For $m = 1$ to $M$ do:  
3. $\qquad \tilde y_i = -[{\partial L(y,F(x_i)) \over \partial F(x_i)}]_{F(x) = F_{m-1}(x)}, i = 1, N$
4. $\qquad a_m = argmin_{a,\beta}\sum_{i=1}^N[\tilde y_i - \beta h(x_i; a)]^2$ 
5. $\qquad \rho_m = argmin_\rho \sum_{i=1}^N L(y_i, F_{m-1}(x_i) + \rho h(x_i; a_m))$ 
6. $\qquad F_m(x) = F_{m-1}(x) + \rho_m h(x;a_m)$
7. endFor  
endAlgorighm

其中$h(x_i;a_m)$表示基本分类器（weak learner or base learner），4中$a_m$表示拟合负梯度能力最好的分类器参数  
负梯度只是表示下降的方向，但是下降多少没有确定，5中$\rho_m$可以认为是下降最快的步长，可以让Loss最小，可以用线性搜索的方式来估计$\rho_m$的值

为何这里不直接利用负梯度来调节，而是需要用一个分类器来拟合呢？因为这里的负梯度是在训练集上求出的，不能被泛化测试集中。我们的参数是在一个函数空间里面，不能使用例如SGD这样的求解方式。使用一个分类器来拟合，是一个泛化的方式。

## 回归树
当我们的基本分类器是一个包含J个节点的回归树时，回归树模型可以表示为  
$$h(x;\{b_j, R_j\}_1^J) = \sum_{b=j}^Jb_jI(x\in R_j) \qquad (8)$$ 

其中$\{ R_j \}_1^J$不相交的区域，它们的集合覆盖了预测值的空间，$\{ b_j \}_1^J$是叶子节点的值，可以认为是模型$h$的系数

利用回归树模型，算法流程6中的公式可以被替换为：
$$F_m(x) = F_{m-1}(x) + \rho_m \sum_{j=1}^J b_{jm}I(x \in R_{jm})\qquad (9)$$ 

其中$\{ R_{jm} \}_1^J$是第m次迭代生成的树所产生的区域。第m次迭代的树用来预测流程3中由流程4中平方误差产生的$\{\tilde y_i\}_i^N$  

$\{ b_{jm}\}$可以被表示为 $b_{jm} = ave_{x_i \in R_{jm}} \tilde y_i$ 即用平均值表示该叶子节点拟合的值

有了下降的方向，我们还需要最好的步长，缩放因子$\rho_m$是流程5中线性搜索方式的一种解决方案

从上面可以看出，我们是先求的$b_{jm}$，然后在求解$\rho_m$，我们能否同时求解呢？  
另$\gamma_{jm} = \rho_{m}b_{jm}$，公式9可以被表示为：
$$F_m(x) = F_{m-1}(x) + \sum_{j=1}^J \gamma_{jm}I(x \in R_{jm})\qquad (10)$$

通过优化如下公式来获取最优的系数$\gamma_{jm}$：
$$\{\gamma_{jm}\}_1^J = argmin_{\ \gamma_j {\ _1^J}}\sum_{i=1}^N L\left(y_i, F_{m-1}(x_i) + \sum_{j=1}^J\gamma_jI(x \in R_{jm})\right)\qquad (11)$$

由于回归树产生的叶子节点各个区域之间是不相交的，且所有的样本最终都会属于某个叶子节点，所以公式11可以表示为：
$$\gamma_{jm} = argmin_\gamma \sum_{x_i\in R_{jm}} L(y_i, F_{m-1}(x_i) + \gamma)$$

给定当前$F_{m-1}(x_i)$，$\gamma_{jm}$可以作为叶子节点的值，该值可以看做是基于损失函数L的每个叶子节点的最理想的常数更新值，也可以认为$\gamma_{jm}$是即有下降方向又有下降步长的值。

综上，用回归树作为基本分类器的梯度提升算法流程可以如下表示：

1. $F_0(x) = argmin_\rho \sum _{i=1}^N L(y_i, \rho)$
2. For $m = 1$ to $M$ do:  
3. $\qquad \tilde y_i = -[{\partial L(y,F(x_i)) \over \partial F(x_i)}]_{F(x) = F_{m-1}(x)}, i = 1, N$  
4. $\qquad \{R_{jm}\}_1^J = J-terminal\, node\, tree(\{ \tilde y_i, x_i \}_i^N)$ 
5. $\qquad \gamma_{jm} = argmin_\gamma \sum_{x_i\in R_{jm}} L(y_i, F_{m-1}(x_i) + \gamma)$
6. $\qquad F_m(x) = F_{m-1}(x) + \sum_{j=1}^J \gamma_{jm}I(x \in R_{jm})$
7. endFor  
endAlgorighm

其中3是计算残差（利用损失函数的负梯度在当前模型的值作为残差的近似值），4是拟合一颗含有J个叶子节点的回归树，5是估计回归树叶子节点的值

下面我们看一下二元分类、多元分类、回归中残差的计算、叶子节点值的估计。

### Two-class logistic regression and classification

我们用negative binomial log-likehood作为我们的损失函数：
$$L(y, F) = log(1 + exp(-2yF)), y \in {-1, 1}\qquad (12)$$

其中
$$F(x) = {1\over2}log\left[{Pr(y=1|x) \over Pr(y=-1|x)}\right]\qquad (13)$$
公式13是logit函数，log odds

如上公式是Freidman的论文中使用的公式，我认为使用在逻辑回归中常见的$L(y, F) = ylogF + (1-y)log(1-F)$，其中$F(z) ={ 1\over{1+exp(-z)}}$也可以

计算残差
$$\tilde y_i = -[{\partial L(y,F(x_i)) \over \partial F(x_i)}]_{F(x) = F_{m-1}(x)} = {2y_i\over 1+exp(2y_iF_{m-1}(x_i))}\qquad(14)$$

叶子节点值的估计：
$$\gamma_jm = argmin_\gamma \sum_{x_i \in R_{jm}} log(1+exp(-2y_i(F_{m-1}(x_i) + \gamma)))\qquad (15)$$

可以通过一步Newton-Raphson来近似公式15，估计结果为：
$$\gamma_{jm} = {\sum_{x_i \in R_{jm}}\tilde y_i \over {\sum_{x_i \in R_{jm}}}|\tilde y_i|(2-|\tilde y_i|)}$$

最终得到的$F_M(x)$与对数几率 log-odds相关，我们可以用来进行概率估计
$$F(x) = {1\over2}log\left({p \over 1-p}\right)$$
$$e^{2F(x)} = {p\over(1-p)}$$
$$P_+(x) = p = {e^{2F(x)}\over 1+e^{2F(x)}} = {1\over1+e^{-2F(x)}}$$
$$P_-(x) = 1-p = {1\over1+e^{2F(x)}}$$

有了概率之后，我们接下来就可以利用概率进行分类

### Multi-class logistic regression and classification

我们使用multi-class log-loss作为损失函数：
$$L(\{y_k, F_k(x)\}_1^K) = -\sum_{k=1}^K y_klogp_k(x)\qquad(16)$$

其中使用softmax来计算概率：
$$p_k(x) = exp(F_k(x)) / \sum_{l=1}^Kexp(F_l(x))\qquad(17)$$

从公式17可以得出，对于多分类问题，我们需要为每个类别创建一颗回归树$F_l(x)\, l=1,2,...,k$

计算残差：

$$\tilde y_{ik} = -[{\partial L(\{y_{il},F_l(x_i)\}_{l=1}^K) \over \partial F_k(x_i)}]_{\{F_l(x) = F_{l, m-1(x)}\}_1^K} = y_{ik} - p_{k,m-1(x_i)}\qquad (18)$$

我们假定共分为3类，那么logloss为：
$$L = -y_1log{exp(F_1(x))\over exp(F_1(x)) + exp(F_1(x)) + exp(F_1(x))} -y_2log{exp(F_2(x))\over exp(F_1(x)) + exp(F_1(x)) + exp(F_1(x))} -y_3log{exp(F_3(x))\over exp(F_1(x)) + exp(F_1(x)) + exp(F_1(x))}$$

$${\partial L \over \partial F_1(x)} = -y_1 + y_1p_1 + y_2p_2 + y_3p_3$$
$${\partial L \over \partial F_2(x)} = y_1p_1 - y_2 + y_2p_2 + y_3p_3$$
$${\partial L \over \partial F_3(x)} = y_1p_1 + y_2p_2 - y_3 + y_3p_3$$

如果当期样本的类别为(1,0,0)，那么
$${\partial L \over \partial F_1(x)} = -1 + p1$$
$${\partial L \over \partial F_2(x)} = p_2$$
$${\partial L \over \partial F_3(x)} = p_3$$

取负梯度，则
$$-{\partial L \over \partial F_1(x)} = 1 - p_1$$
$$-{\partial L \over \partial F_2(x)} = -p_2 = 0 - p_2$$
$$-{\partial L \over \partial F_3(x)} = -p_3 = 0 - p_3$$

符合公式18中的$\tilde y_{ik} = y_{ik} - p_{k,m-1(x_i)}$

叶子节点值的估计：
$$\{r_{jkm}\} = argmin_{\gamma_{jk}}\sum_{i=1}^N \sum_{k=1}^K \phi \left( y_{ik}, F_{k,m-1}(x_i) + \sum_{j=1}^J\gamma_{jk}I(x_i \in R_{jm})\}\right)\qquad(19)$$

可以通过一步Newton-Raphson来近似公式19，估计结果为：
$$\gamma_{jkm} = {K-1\over K}{\sum_{x_i \in R_{jkm}}\tilde y_{ik} \over {\sum_{x_i \in R_{jkm}}}|\tilde y_{ik}|(1-|\tilde y_{ik}|)}$$

### Regression

我们使用Least-squares作为损失函数：
$$L(y, F) = {(y-F)^2\over 2}$$

计算残差：
$$\tilde y_i = -[{\partial L(y,F(x_i)) \over \partial F(x_i)}]_{F(x) = F_{m-1}(x)} = {y_i - F_{m-1}(x_i)}\qquad(20)$$

叶子节点值的估计：
$$\gamma_{jm} = argmin_\gamma \sum_{x_i \in R_{jm}} {1\over 2}(y_i - (F_{m-1}(x_i) + \gamma))^2\qquad (21)$$
$$\gamma_{jm} = argmin_\gamma \sum_{x_i \in R_{jm}} {1\over 2}(y_i - F_{m-1}(x_i) - \gamma)^2$$
$$\gamma_{jm} = argmin_\gamma \sum_{x_i \in R_{jm}} {1\over 2}(\tilde y_i -  \gamma)^2$$

容易得出以下结果：
$$\gamma_{jm} = ave_{x_i \in R_{jm}} \tilde y_i$$

####回归树的创建

拟合残数是一个回归问题，所以在分割样本时，我们不会采用基尼指数（Gini）、信息增益（IG）等用于分类的标准。  
我们可以选用MSE(mean square error impurity criterion)作为分割样本的标准。
也可是采用Friedman在论文中的the least-squares improvement criterion，公式如下：
$$i_2(R_l, R_r) = {w_lw_r\over w_l + w_r}(\bar y_l - \bar y_r)^2$$

其中$\bar y_l \, \bar y_r$分别是左右孩子的平均值，$w_l \, w_r$分别是左右孩子对应的权重和

本文是针对具体的损失函数进行的相关推导，泛化能力差，大家可以参考xgboost作者的这篇[文章](http://www.52cs.org/?p=429)，作者进行了更加一般的推导，这一个抽象的形式对于实现机器学习工具也是非常有帮助的。

引用：  
Greedy Function Approximation: A Gradient Boosting Machine