---
title: LeetCode--Pascal's Triangle II
date: 2017-06-14 10:49:25
categories: Algorithm
tags:
	- Algorithm
	- LeetCode
typora-copy-images-to: ipic
---

# 119. Pascal's Triangle II

## Description

Given an index *k*, return the *k*th row of the Pascal's triangle.

## Example

For example, given *k* = 3,

Return `[1,3,3,1]`.

**Note:**

Could you optimize your algorithm to use only *O*(*k*) extra space?

<!-- more -->

```swift
[
     [1],
    [1,1],
   [1,2,1],
  [1,3,3,1],
 [1,4,6,4,1]
]
```

## 解题思路

这题紧接上一118题，不过现在只需要第k层的数组，空间只有*O*(*k*)，意思是只能用一个一维数组进行计算。上一题中计算公式为A\[i][j] = A\[i-1][j-1] + A\[i-1][j]。在这一题中只能将上一层的数据放在这一层中进行计算，所以计算公式为A\[i] = A\[i-1] + A\[i]。

例如，需要计算第三层得到[1, 3, 3, 1]时，初始数组A为[1, 2, 1, 1]，其中[1, 2, 1]为第二层数组的值。那么A[1] = A[0] + A[1]，A[2] = A[1] + A[2]，发现顺序计算时，后面会用到前面改变的值，所以改为倒序计算。

## 代码

### 一、

申明一个rowIndex+1长度，元素值为1的数组。从第二层开始计算。

```swift
class Solution {
    func getRow(_ rowIndex: Int) -> [Int] {
        var result = Array(repeating: 1, count: rowIndex+1)
        if rowIndex < 2 {
            return result
        }
        
        for i in 2...rowIndex {
            for j in (1..<i).reversed() {
                result[j] = result[j-1] + result[j]
            }
        }
        return result
    }
}
```
