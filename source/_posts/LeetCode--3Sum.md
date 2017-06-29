---
title: LeetCode--3Sum
date: 2017-06-28 14:41:41
categories: Algorithm
tags:
	- Algorithm
	- LeetCode
typora-copy-images-to: ipic
---

# 15. 3Sum

## Description

Given an array *S* of *n* integers, are there elements *a*, *b*, *c* in *S* such that *a* + *b* + *c* = 0? Find all unique triplets in the array which gives the sum of zero.

**Note:** The solution set must not contain duplicate triplets.

## Example

```
For example, given array S = [-1, 0, 1, 2, -1, -4],

A solution set is:
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```

<!-- more -->

## 解题思路

[1. Two Sum](http://willwei.me/2017/06/26/LeetCode--Two%20Sum/)的衍生题，难度相对也就高了些。先看看题目要求：

- 返回的数组是排过序的
- 没有重复的答案数组

接下来就好做了，先排序，然后找出符合要求的答案数组，再去重。

## 代码

### 一、

这是我没经大脑直接写出来的解法。

```swift
class Solution {
    func threeSum(_ nums: [Int]) -> [[Int]] {
        if nums.count < 3 {
            return []
        }
        
        let sortedNums = nums.sorted(by: <)
        let count = sortedNums.count
        var result: [[Int]] = []
        
        for i in 0..<(count-2) {
            for j in (i+1)..<count {
                for k in (j+1)..<count {
                    if sortedNums[i] + sortedNums[j] + sortedNums[k] == 0 {
                        let arr = [sortedNums[i], sortedNums[j], sortedNums[k]]
                        var itemRepeat = false
                        for item in result {
                            if item[0] == arr[0] && item[1] == arr[1] && item[2] == arr[2] {
                                itemRepeat = true
                            }
                        }
                        if !itemRepeat {
                            result.append(arr)
                        }
                    }
                }
            }
        }
        return result
    }
}
```
果然最后悲剧了，[LeetCode](https://leetcode.com/)毫不留情的打回。

![80718BD5-A7A1-4BDD-B062-16372F04B4DF](https://ws4.sinaimg.cn/large/006tKfTcly1fh0x3pyr0rj30w306hae4.jpg)

### 二、

痛定思痛，以降低时间复杂度为己任。主要改进了两点，一个是两个指针的位置，另一个是去重。

```swift
class Solution {
    func threeSum(_ nums: [Int]) -> [[Int]] {
        if nums.count < 3 {
            return []
        }
        
        let sortedNums = nums.sorted(by: <)
        let count = sortedNums.count
        var result: [[Int]] = []
        var i = 0
        
        while i < count-2 {
            var j = i + 1
            var k = count - 1
            while j < k {
                let sum = sortedNums[i] + sortedNums[j] + sortedNums[k]
                if sum == 0 {
                    let arr = [sortedNums[i], sortedNums[j], sortedNums[k]]
                    result.append(arr)
                    j += 1
                    k -= 1
                    while j < k && sortedNums[j-1] == sortedNums[j] {
                        j += 1
                    }
                    while j < k && sortedNums[k] == sortedNums[k+1] {
                        k -= 1
                    }
                } else if sum < 0 {
                    j += 1
                } else {
                    k -= 1
                }
            }
            while i < count-1 && sortedNums[i] == sortedNums[i+1] {
                i += 1
            }
            i += 1
        }
        return result
    }
}
```
