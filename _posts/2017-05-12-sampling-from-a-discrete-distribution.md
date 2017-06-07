---
layout: post
title: Sampling from a discrete distribution
mathjax: true
---

# Sampling from a discrete distribution

GitHub: [sampling-method](https://github.com/watereals/learning/tree/master/sampling-method)
参考这篇文章[这篇文章](http://www.keithschwarz.com/darts-dice-coins/)

## 一般算法

时间复杂度 O(M * log(N)), 空间复杂度 O(2 * N)

Algorithm: Simulating a Loaded Die with a Fair Die

Initialization:

1. Find the LCM of the denominators of the probabilities $p_0,p_1,...,p_{n−1}$; call it $L$.
2. Allocate an array $A$ of size $L$ mapping outcomes of the fair die roll to the original die roll.
3. For each side $i$ of the original die, in any order:
3.1 Set the next $L*p_i$ entries of $A$ to be $i$.

Generation:

1. Generate a fair die roll from an $L$-sided die; call the side $S$.
2. Return $A[S]$.

## 累积法

时间复杂度 O(M * log(N)), 空间复杂度 O(2 * N)

Algorithm: Roulette Wheel Selection

Initialization:

1. Allocate an array $A$ of size $n$
2. Set $A[0]=p_0$.
3. For each probability $i$ from 1 to $n−1$:
    + 3.1 Set $A[i]=A[i−1]+p_i$

Generation:

1. Generate a uniformly-random value $x$ in the range $[0,1)$
2. Using a binary search, find the index $i$ of the smallest element in $A$ larger than $x$.
3. Return $i$.

## 剩余法

与累积法对应，剩余法应运而生。

时间复杂度 O(M * N), 空间复杂度 O(N)

Algorithm: Loaded Die from Biased Coins

Initialization:

1. Store the probabilities $p_i$ for later use.

Generation:

1. Set $mass=1$
2. For $i=0$ to $n−1$:
    + 2.1 Flip a biased coin with probability $p_i / mass$ of coming up heads.
    + 2.2 If it comes up heads, return $i$.
    + 2.3 Otherwise, set $mass=mass−p_i$

## Alias Method

Algorithm: (Unstable) Vose's Alias Method

Caution: This algorithm suffers from numerical inaccuracies. A more numerically sound algorithm is given later.

Initialization:

1. Create arrays $Alias$ and $Prob$, each of size $n$.
2. Create two worklists, Small and Large.
3. Multiply each probability by $n$.
4. For each scaled probability $ p_i $:
    + If $ p_i<1 $, add i to $Small$.
    + Otherwise ($ p_i≥1 $), add $ i $ to $Large$.
5. While $Small$ is not empty:
    + Remove the first element from $Small$; call it $l$.
    + Remove the first element from $Large$; call it $g$.
    + Set $Prob[l]=p_l$.
    + Set $Alias[l]=g$.
    + Set $p_g:=p_g−(1−p_l)$.
    + If $p_g<1$, add $g$ to $Small$.
    + Otherwise ($p_g≥1$), add $g$ to $Large$.
6. While $Large$ is not empty:
    + Remove the first element from $Large$; call it $g$.
    + Set $Prob[g]=1$.

Generation:

1. Generate a fair die roll from an nn-sided die; call the side $i$.
2. Flip a biased coin that comes up heads with probability $Prob[i]$.
3. If the coin comes up "heads," return $i$.
4. Otherwise, return $Alias[i]$.

## Reference:

[Darts, Dice, and Coins: Sampling from a Discrete Distribution](http://www.keithschwarz.com/darts-dice-coins/)
看懂这个就会了

[The Alias Method: Efficient Sampling with Many Discrete Outcomes](https://hips.seas.harvard.edu/blog/2013/03/03/the-alias-method-efficient-sampling-with-many-discrete-outcomes/)

http://blog.csdn.net/sky_zhe/article/details/10051967

http://www.cnblogs.com/younggun/p/3249772.html

http://www.cnblogs.com/miloyip/archive/2010/04/21/1717109.html
