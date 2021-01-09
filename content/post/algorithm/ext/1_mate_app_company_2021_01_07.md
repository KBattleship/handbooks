---
title: "[ Interview ] 1.面试之MateAPP"
date: 2021-01-07T01:13:47+08:00
lastmod: 2021-01-07T01:13:47+08:00
keywords: ['interview']
description: ""
tags: ['interview','笔试','algorithm']
categories: ['Algorithm']
author: ""
---
# 面试之MateAPP

## 1.笔试

```java
/**
  * 给定一个二叉树, 检查它是否是镜像对称的
  * 例如以下是镜像对称的
  *      1
  *     / \
  *    2   2
  *   / \ / \
  *  3  4 4  3
  *
  * 下面这个则不是镜像对称的
  *      1
  *     / \
  *    2   2
  *     \   \
  *      3   3
  *
  * TreeNode类的定义:
  *
  * @param TreeNode 一颗二叉树
  * @return boolean 是否是对称的
  */

// 以下给出TreeNode类, 请勿修改
static class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int x) { val = x; }
}
```
**解题思路**

**解答代码**


```java
/**
  * 对任意一个Map<String, Object>, 其 key 为 String,
  * 其 value 为 Map<String, Object> Object[] Number String 中的任意一种,
  * 显然叶子节点是 value 类型为 Number 或 String的节点,
  * 将 Map 转为多条字符串, 每条字符串表达其中一个叶子节点,
  * 比如:
  * {"a":{"b":["v",2,{"c":0}]},"d":[1,null,3]}
  * 将转化为以下这些字符串
  * a.b[0] = v
  * a.b[1] = 2
  * a.b[2].c = 0
  * d[0] = 1
  * d[1] = null
  * d[2] = 3
  *
  * @param map 上述的 map
  * @return 所有的字符串
  */
```
**解题思路**

**解答代码**

```java
/**
  * 注意! 本题不要遍历二维数组. 要求时间复杂度严格低于n^2, 否则视为不得分
  *
  * 现有一个n\*n的二维正整数数组nums，每行元素保证递增，每列元素保证递增，
    求某正整数x是否存在于该二维数组中，需要尽量优化时间和空间复杂度；
  * @param int[][] nums
  * @param int x 目标数
  * @return boolean
  */
```

**解题思路**

**解答代码**
