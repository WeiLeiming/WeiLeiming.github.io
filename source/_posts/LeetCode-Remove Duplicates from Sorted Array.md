---
title: LeetCode--Remove Duplicates from Sorted Array
date: 2017-06-11 19:04:12
categories: Algorithm
tags:
	- Algorithm
	- LeetCode
typora-copy-images-to: ipic
---

# 26. Remove Duplicates from Sorted Array

## Description

Given a sorted array, remove the duplicates in place such that each element appear only *once* and return the new length.

Do not allocate extra space for another array, you must do this in place with constant memory.

<!-- more -->

## Example

Given input array *nums* = `[1,1,2]`,

Your function should return length = `2`, with the first two elements of *nums* being `1` and `2` respectively. It doesn't matter what you leave beyond the new length.

Input:

```swift
nums: [1, 1, 2]
```

answer:

```swift
nums: [1, 2, 2]
return: 2
```

## 解题思路

首先这是一个排好序的数组，要求是删除重复的元素。遍历数组，使用newIndex记录新数组的位置，如果index与newIndex的数组值不相等，则将index的数组值赋值给newIndex+1位置上，重复直至遍历结束。

## 代码演进

![E2AC9068-C87C-47F6-BC03-6DE8D5115F04](https://ws2.sinaimg.cn/large/006tKfTcly1fghj3ochjij30k7045t8y.jpg)

这个Run Time好像存在随机性。。。参考吧。。。

### 第一版

接前面27题，相似写法，第一次错误是因为没有判断数组长度为0的情况。

```swift
class Solution {
    func removeDuplicates(_ nums: inout [Int]) -> Int {
        let length = nums.count
        var newIndex = 0
        if nums.count == 0 {
            return 0
        }
        for index in 0..<length {
            if nums[newIndex] != nums[index] {
                newIndex += 1
                nums[newIndex] = nums[index]
            }
        }
        return newIndex + 1
    }
}
```

### 第二版

这一版只是发现了数组遍历不用从0开始，因为第一个元素肯定相等的。

```swift
class Solution {
    func removeDuplicates(_ nums: inout [Int]) -> Int {
        let length = nums.count
        var newIndex = 0
        if nums.count == 0 {
            return 0
        }
        for index in 1..<length {
            if nums[newIndex] != nums[index] {
                newIndex += 1
                nums[newIndex] = nums[index]
            }
        }
        return newIndex + 1
    }
}
```
