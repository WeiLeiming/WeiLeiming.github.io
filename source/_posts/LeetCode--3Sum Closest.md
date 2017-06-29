---
title: LeetCode--3Sum Closest
date: 2017-06-29 16:17:13
categories: Algorithm
tags:
	- Algorithm
	- LeetCode
typora-copy-images-to: ipic
---

# 16. 3Sum Closest

## Description

Given an array *S* of *n* integers, find three integers in *S* such that the sum is closest to a given number, target. Return the sum of the three integers. You may assume that each input would have exactly one solution.

## Example

```
For example, given array S = {-1 2 1 -4}, and target = 1.

The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
```

<!-- more -->

## 解题思路

紧接上一题[15. 3Sum](http://willwei.me/2017/06/28/LeetCode--3Sum/)，难度相当，解法类似，去掉去重，增加变量来计算判断查找的值和给定的值的差值就行，就不在赘言了。

## 代码

### 一、

```swift
class Solution {
    func threeSumClosest(_ nums: [Int], _ target: Int) -> Int {
        if nums.count < 3 {
            return 0
        }
        
        let sortedNums = nums.sorted(by: <)
        let count = sortedNums.count
        var distance = Int.max
        var result = 0
        var i = 0
        
        while i < count-2 {
            var j = i + 1
            var k = count - 1
            while j < k {
                let tmpSum = sortedNums[i] + sortedNums[j] + sortedNums[k]
                var tmpDistance = 0
                if tmpSum < target {
                    tmpDistance = target - tmpSum
                    if tmpDistance < distance {
                        distance = tmpDistance
                        result = tmpSum
                    }
                    j += 1
                } else if tmpSum > target {
                    tmpDistance = tmpSum - target
                    if tmpDistance < distance {
                        distance = tmpDistance
                        result = tmpSum
                    }
                    k -= 1
                } else {
                    return target
                }
            }
            i += 1
        }
        return result
    }
}
```