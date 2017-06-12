---
title: LeetCode--Remove Duplicates from Sorted Array II
date: 2017-06-12 10:18:27
categories: Algorithm
tags:
	- Algorithm
	- LeetCode
typora-copy-images-to: ipic
---

# 80. Remove Duplicates from Sorted Array II

## Description

Given a sorted array, remove the duplicates in place such that each element appear only *once* and return the new length.

Do not allocate extra space for another array, you must do this in place with constant memory.

Follow up for "Remove Duplicates":

What if duplicates are allowed at most *twice*?

<!-- more -->

## Example

Given sorted array *nums* = `[1,1,1,2,2,3]`,

Your function should return length = `5`, with the first five elements of *nums* being `1`, `1`, `2`, `2` and `3`. It doesn't matter what you leave beyond the new length.

Input:

```swift
nums: [1, 1, 1, 2, 2, 3]
```

answer:

```swift
nums: [1, 1, 2, 2, 3]
return: 5
```

## 解题思路

紧接26题，不过现在可以允许最多两次重复元素存在，在26题的基础上，增加一个计数变量来记录重复的次数，跳过重复次数大于2的数。

## 代码

### 一、

在26题的基础上增加了次数判断。

```swift
class Solution {
    func removeDuplicates(_ nums: inout [Int]) -> Int {
        if nums.count == 0 {
            return 0
        }
        
        let length = nums.count
        var newIndex = 0
        var num = 0
        
        for index in 1..<length {
            if nums[newIndex] != nums[index] {
                num = 0
                newIndex += 1
                nums[newIndex] = nums[index]
            } else {
                num += 1
                if num < 2 {
                    newIndex += 1
                    nums[newIndex] = nums[index]
                }
            }
        }
        return newIndex + 1
    }
}
```

### 二、

发现了一个不错的写法。

```swift
class Solution {
    func removeDuplicates(_ nums: inout [Int]) -> Int {
        if nums.count == 0 {
            return 0
        }
        
        var newIndex = 0
        
        for num in nums {
            if newIndex < 2 || num > nums[newIndex - 2] {
                nums[newIndex] = num
                newIndex += 1
            }
        }
        return newIndex
    }
}
```