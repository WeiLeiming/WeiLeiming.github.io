---
title: LeetCode--Two Sum
date: 2017-06-26 10:19:00
categories: Algorithm
tags:
	- Algorithm
	- LeetCode
typora-copy-images-to: ipic
---

# 1. Two Sum

## Description

Given an array of integers, return **indices** of the two numbers such that they add up to a specific target.

You may assume that each input would have **exactly** one solution, and you may not use the *same* element twice.

## Example

```
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```

<!-- more -->

## 解题思路

这题要解很简单，没啥好说的。最后无非是一个优化的问题。

## 代码

### 一、

脑子最先浮现的想法，两个for循环搞定，时间复杂度为O(n2)

```swift
class Solution {
    func twoSum(_ nums: [Int], _ target: Int) -> [Int] {
        let count = nums.count
        for i in 0..<count {
            for j in (i+1)..<count {
                if nums[i] + nums[j] == target {
                    return [i, j]
                }
            }
        }
        return []
    }
}
```
### 二、

显然上一个不是我们想要的答案。是否可以将时间复杂度从O(n2)降到O(n)呢，答案当然是肯定的，牺牲空间来换。方法那就是使用字典，将数组元素及对应的下标放入字典中，数组元素为键，下标为值，然后只需要去取`target - num`对应的下标即可，一次循环就够了。

```swift
class Solution {
    func twoSum(_ nums: [Int], _ target: Int) -> [Int] {
        let count = nums.count
        var temp = [Int: Int]()
        for i in 0..<count {
            let num = nums[i]
            if let j = temp[target - num] {
                return [j, i]
            } else {
                temp[num] = i
            }
        }
        return []
    }
}
```

### 三、

当然，既然都用到了下标，还有数组元素，我们可以用用官方的API。

```swift
class Solution {
    func twoSum(_ nums: [Int], _ target: Int) -> [Int] {
        var temp = [Int: Int]()
        for (i, num) in nums.enumerated() {
            if let j = temp[target - num] {
                return [j, i]
            } else {
                temp[num] = i
            }
        }
        return []
    }
}
```