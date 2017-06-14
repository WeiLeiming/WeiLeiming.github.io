---
title: LeetCode--Pascal's Triangle
date: 2017-06-14 09:37:16
categories: Algorithm
tags:
	- Algorithm
	- LeetCode
typora-copy-images-to: ipic
---

# 118. Pascal's Triangle

## Description

Given *numRows*, generate the first *numRows* of Pascal's triangle.

## Example

For example, given *numRows* = 5,

Return

```swift
[
     [1],
    [1,1],
   [1,2,1],
  [1,3,3,1],
 [1,4,6,4,1]
]
```

<!-- more -->

## 解题思路

先找规律

- 首先这是一个二维数组
- 第n层有n个元素
- 每层第一个和最后一个元素为1
- 从第3层开始，除开第一个和最后个元素，中间元素的值都为上一层中的元素相加而来，具体式子为A\[i][j] = A\[i-1][j-1] + A\[i-1][j]

## 代码

### 一、

依照找出的规律，定义了一个二维数组来存储，每位初始化为1，再计算中间的元素值。

```swift
class Solution {
    func generate(_ numRows: Int) -> [[Int]] {
        var result: [Array<Int>] = []
        
        for i in 0..<numRows {
            let subArray = Array.init(repeating: 1, count: i+1)
            result.append(subArray)

            if i > 1 {
                for j in 1..<i {
                    result[i][j] = result[i - 1][j - 1] + result[i - 1][j]
                }
            }
        }
        return result
    }
}
```

### 二、

重新改了下，提取了前面层出来判断。

```swift
class Solution {
    func generate(_ numRows: Int) -> [[Int]] {
        if numRows == 0 {
            return []
        } else if numRows == 1 {
            return [[1]]
        } else if numRows == 2 {
            return [[1], [1, 1]]
        }
        
        var result: [Array<Int>] = [[1], [1, 1]]
        
        for i in 2..<numRows {
            let subArray = Array(repeating: 1, count: i+1)
            result.append(subArray)

            for j in 1..<i {
                result[i][j] = result[i - 1][j - 1] + result[i - 1][j]
            }
        }
        return result
    }
}
```
### 三、

这是另外一种方式，大同小异。

```swift
class Solution {
    func generate(_ numRows: Int) -> [[Int]] {
        if numRows == 0 {
            return []
        } else if numRows == 1 {
            return [[1]]
        } else if numRows == 2 {
            return [[1], [1, 1]]
        }
        
        var result: [Array<Int>] = [[1], [1, 1]]
        
        for i in 2..<numRows {
            var subArray = [1]

            for j in 1..<i {
                subArray.append(result[i - 1][j - 1] + result[i - 1][j])
            }
            subArray.append(1)
            result.append(subArray)
        }
        
        return result
    }
}
```