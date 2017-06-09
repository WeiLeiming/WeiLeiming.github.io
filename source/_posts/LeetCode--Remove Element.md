---
title: LeetCode--Remove Element
date: 2017-06-09 10:17:19
categories: Algorithm
tags:
	- Algorithm
	- LeetCode
typora-copy-images-to: ipic
---

# 27. Remove Element

## Description

Given an array and a value, remove all instances of that value in place and return the new length.

Do not allocate extra space for another array, you must do this in place with constant memory.

The order of elements can be changed. It doesn't matter what you leave beyond the new length.

<!-- more -->

## Example

Given input array *nums* = `[3,2,2,3]`, *val* = `3`

Your function should return length = 2, with the first two elements of *nums* being 2.

Input:

```swift
nums: [3,2,2,3]
val: 3
```

answer:

```swift
nums: [2,2]
return: 2
```

## 解题思路

遍历数组，使用count记录数组中不等于value的数量，同时将当前位置的值复制到count位置上，直至遍历结束。这是count就是新数组的长度，而nums前count位就是新数组。

## 代码演进

![DA9E2FA9-8DA5-4C1F-B30B-35001520FBFF](https://ws4.sinaimg.cn/large/006tNbRwly1fger3682f6j30lm08aq3n.jpg)

截取了中间一部分节点

### 第一版

很自然的先想到了Swift数组的`enumerated()`方法，这也是运行时间36ms的写法。

```swift
class Solution {
    func removeElement(_ nums: inout [Int], _ val: Int) -> Int {
        var count = 0
        for (index, num) in nums.enumerated() {
            if num != val {
                nums[count] = num
                count += 1
            }
        }
        return count
    }
}
```

### 第二版

去掉了enumerated()方法，改用取数组长度遍历，时间缩至22ms。

```swift
class Solution {
    func removeElement(_ nums: inout [Int], _ val: Int) -> Int {
        var count = 0
        var length = nums.count
        for index in 0..<length {
            if nums[index] != val {
                nums[count] = nums[index]
                count += 1
            }
        }
        return count
    }
}
```

### 第三版

发现nums[index]每次读取了两次，使用中间变量读取一次，最后耗时降低至了19ms。

```swift
class Solution {
    func removeElement(_ nums: inout [Int], _ val: Int) -> Int {
        var count = 0
        var length = nums.count
        for index in 0..<length {
            let num = nums[index]
            if num != val {
                nums[count] = num
                count += 1
            }
        }
        return count
    }
}
```