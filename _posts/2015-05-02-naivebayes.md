---
layout: post
title: NavieBayes
mathjax: true
---

# 朴素贝叶斯分类

## 1. 概率与数理统计基础

### 1.1 贝叶斯定理

条件概率公式：
$$P(B \mid A)=P(AB)/P(A)$$
因此，由于
$$P(AB)=P(B \mid A)*P(A)$$
和
$$P(AB)=P(A \mid B)*P(B)$$
可以得到
$$P(B|A)=P(A \mid B)*P(B)/P(A)$$
这就是贝叶斯定理。

###1.2 概率的laplace估计

基于频率的laplace估计技术是对概率的一种平滑处理，比如A可能出现的值有${a1,a2....at}(1<=k<=t)$，即$|Values(A)|=t$,则使用laplace平滑的概率估计值是在概率计算的分子上+1，分母上加分子属性的个数。例如：

概率                 | 概率的频率估计                            | 概率的的频率估计（使用laplace平滑）                                       
---------------------|-------------------------------------------|---------------------------------------------------------------------------
$P(A=a_k)$           | $Counts(A=a_k)/Counts(all)$               | $(Counts(A=a_k)+1)/(Counts(all)+ \mid Values(A) \mid )$                   
$P(A=a_k \mid C=cj)$ | $Counts(A=a_k \land C=c_j)/Counts(C=c_j)$ | $(Counts(A=a_k \land C=c_j)+ 1) / (Counts(C=c_j) + \mid Values(A) \mid )$ 

###1.3 概率的m估计

略

## 2. 贝叶斯分类模型

### 2.1 朴素贝叶斯分类

假设有$n$个样本，$m$个属性，$k$个类,则$1<=j<=k$，我们的目标是找出

$$P(C=c_j \mid x) = P(C=c_j \mid A1=a1_i,A2=a2_i,...Am=am_i)$$

所对应的最大的类。根据贝叶斯公式，有

$$P(C=cj \mid A1=a1_i,A2=a2_i,...Am=am_i)=P（A1=a1_i,A2=a2_i,...Am=am_i \mid C=c_j)P(C=cj)/P(A1=a1_i,A2=a2_i,...Am=am_i)$$

从中可以看出属于各个类的概率的分母$P(A1=a1i,A2=a2i,...Am=ami)$是一样的，因此，有

$$P(C=cj \mid A1=a1_i,A2=a2_i,...Am=am_i) ∝ P（A1=a1_i,A2=a2_i,...Am=am_i \mid C=c_j)P(C=c_j)$$

因此我们仅需计算概率$P(C=c_j)$和$P（A1=a1_i,A2=a2_i,...Am=am_i|C=c_j)$。
其中

$$P(C=c_j)=Counts(C=c_j)/Counts(all)$$

而$P(A1=a1_i,A2=a2_i,...Am=am_i \mid C=c_j)$不太好计算,因为需要求各个属性的联合概率。

朴素贝叶斯分类理论假设在给定类标的条件下，所有属性取值之间都是相互独立的．这一假设称为“条件独立性假设”。
在条件独立假设之下,
$$ P(A1=a1_i,A2=a2_i,...Am=am_i \mid C=c_j)=P(A1=a1_i \mid C=c_j)*P(A2=a2_i \mid C=c_j)...P(Am=am_i \mid C=c_j) $$

其中每一个概率都可以按照下面介绍的数值属性或名称属性的计算方法得到。
因此最终我们只需要选择$P(A1=a1_i \mid C=c_j)*P(A2=a2_i \mid C=cj)...P(Am=am_i \mid C=c_j)P(C=c_j)$值最大的j所对应的类即可。

### 2.2 名称性属性处理

使用laplace估计各个属性值得概率，比如上面的条件概率

$$P(Ax=ax_i \mid C=c_j)=Counts(Ax=ax_i \land C=c_j)/Counts(C=c_j), 1<=x<=m$$

### 2.3 数值型属性处理

首先，计算出属性的均值和方差，然后根据正态分布的规范密度函数，计算相应的概率。

$$P(Ax=ax_i \mid C=c_j)=g(ax_i,\mu,\sigma)={1 \over {\sqrt{2 \pi}\sigma}} \cdot e^{-{(ax_i-\mu)^2 \over {2\sigma^2}} }$$

$g(ax_i,\mu,\sigma)$为属性A的Gauss规范密度函数，$\mu$和$\sigma$分别为训练样本中类标值为$c_j$的属性A的所有训练样本的均值和方差。

### 2.4 一个例子

## 3. 贝叶斯分类拓展

TAN：假设每个属性都只受且最多只受一个属性的影响。
贝叶斯网络：一个属性可以受多个属性影响，组成一个属性的网络。