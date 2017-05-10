---
layout: post
title: Laplace Smoothing
mathjax: true
---

# 概率的拉普拉斯平滑

##问题

一个盒子里装着若干个白球和黑球，现在依次从中拿球，拿了N次，其中白球为m个，则再拿依次球是白球的概率是多少？

##频率主义

直接根据条件，

$$p=P(x=w)=m/N$$

它实际上可以使用最大似然估计方法来计算。假设从盒子里取出的球为白球的概率为$p$，则：

$$L(p)=C_N^mp^m(1-p)^{N-m} ∝ p^m(1-p)^{N-m}$$

求$log$，有：

$$l(p)=mlog(p)+(N-m)log(1-p)$$

求最大值，需要导数为0，因此：

$$m/p-(N-m)/(1-p)=0$$

求得：

$$p=m/N$$

##概率修正（Laplace修正）

上面的计算过程，实际上没有使用已有的信息，当$m=0$时，根据上面的计算方法，我们得到$p=0$,很显然，这非常不合理，因为我们已经知道盒子里有白球，$p$肯定大于0。
Laplace修正假设，**在执行实际的试验之前，我们假设已经有两次试验，一次成功和一次失败。**那么根据频率计算可得：

$$p=P(x=w)=(m+1)/(N+2)$$

同样使用最大似然估计方法来计算白球的概率。假设从盒子里取出的球为白球的概率为$p$，则

$$L(p)=C_{N+2}^{m+1}p^{m+1}(1-p)^{(N+1-m)} ∝ p^{m+1}(1-p)^{N+1-m}$$

求$log$，有：

$$l(p)=(m+1)log(p)+(N+1-m)log(1-p)$$

求最大值，需要导数为0，因此：

$$(m+1)/p-(N+1-m)/(1-p)=0$$

求得：

$$p=(m+1)/(N+2)$$

###Laplace–Bayes estimator

基于频率的laplace估计技术是对概率的一种平滑处理，比如A可能出现的值有${a1,a2....at}(1<=k<=t)$，即$|Values(A)|=t$,则使用laplace平滑的概率估计值是在概率计算的分子上+1，分母上加分子属性的个数。例如：

概率                 | 概率的频率估计                            | 概率的的频率估计（使用laplace平滑）                                       
---------------------|-------------------------------------------|---------------------------------------------------------------------------
$P(A=a_k)$           | $Counts(A=a_k)/Counts(all)$               | $(Counts(A=a_k)+1)/(Counts(all)+ \mid Values(A) \mid )$                   
$P(A=a_k \mid C=cj)$ | $Counts(A=a_k \land C=c_j)/Counts(C=c_j)$ | $(Counts(A=a_k \land C=c_j)+ 1) / (Counts(C=c_j) + \mid Values(A) \mid )$ 

###参考

1. [Probability estimation](http://www.temida.si/~bojan/probability_estimation.php)
包括（not only）
    * Relative frequency （相关频率估计，又称最大似然估计）
    * Laplace's law of succession （Laplace平滑规则）
    * Bayesian m-estimate (m估计)
2. Wikipedia: [Laplace's law](http://en.wikipedia.org/wiki/Laplace%27s_law) 转到了 [Rule of succession](http://en.wikipedia.org/wiki/Rule_of_succession)
Rule of succession: a smoothing technique accounting for unseen data.主要讲Laplace平滑规则。
3. [Bayes estimator](http://en.wikipedia.org/wiki/Bayes_estimator)
贝叶斯估计理论。