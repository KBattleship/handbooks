---
title: "[ 算法 ] 12.6 动态规划"
date: 2020-06-30T21:10:47+08:00
lastmod: 2020-06-30T21:10:47+08:00
keywords: ['guava']
description: ""
tags: ['java','guava','java tools']
categories: ['Java']
author: ""
---
# 动态规划
> **动态规划(Dynamic Programming)**,每次决策依赖于当前决策状态，随即又伴随着状态偏移。决策序列就是在变化的状态中生成的(适用于多阶段优化问题)。

## 1 算法原理
### 1.1 基本思想
+ 基本思想是将待求解问题分解成若干个子问题，先求解子问题，然后从这些子问题的解得到原问题的最优解。
+ 通过保存下子问题的解到表中，从而避免重叠子问题的重复计算，优化计算过程时间。
+ 动态规划类似但不同于[分治算法](12_7_divide_and_conquer.md)。

    > 一句话概述：动态规划-->各子问题重叠。分治算法-->各子问题独立。
    算法导论： 动态规划要求其子问题既要独立又要重叠。如果同一个问题的两个子问题不共享资源，则它们就是独立的。对两个子问题来说，如果它们确实是相同的子问题，只是作为不同问题的子问题出现的话，则是重叠的。
+ 动态规划算法具有很多种，但是计算子问题值的填表形式是同一种。

### 1.2 算法策略

## 2 算法实现
### 2.1 适用场景
> 适用于求解具有某种最优性质的问题。

### 2.2 动图演示
### 2.3 代码实现