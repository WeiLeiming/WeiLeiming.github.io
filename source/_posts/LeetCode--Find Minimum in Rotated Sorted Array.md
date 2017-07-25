---
title: LeetCode--Find Minimum in Rotated Sorted Array
date: 2017-07-25 14:47:20
categories: Algorithm
tags:
	- Algorithm
	- LeetCode
typora-copy-images-to: ipic
---

# 153. Find Minimum in Rotated Sorted Array

## Description

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e., `0 1 2 4 5 6 7` might become `4 5 6 7 0 1 2`).

Find the minimum element.

You may assume no duplicate exists in the array.

## Example

input:

```swift
nums: [4 5 6 7 0 1 2]
```

output:

```swift
return: 0
```

<!-- more -->

## 解题思路

首先输入的是一个没有重复元素的升序数组进行轮转后的数组。所以如果一个区间nums[startIndex] < nums[endIndex]，那么该区间就一定是升序的，最小值就是nums[startIndex]。

采用二分法，获取中间元素的值，nums[middleIndex]，middleIndex = (startIndex + endIndex) / 2

- 如果nums[middleIndex] > nums[startIndex]，那么最小值在右半区域。
- 如果nums[middleIndex] < nums[startIndex]，那么最小值在左半区域。

## 代码

### 一、

```swift
class Solution {
    func findMin(_ nums: [Int]) -> Int {
        let count = nums.count
        if count == 0 {
            return 0
        } else if count == 1 {
            return nums[0]
        } else if count == 2 {
            return min(nums[0], nums[1])
        }
        
        var startIndex = 0
        var endIndex = count - 1
        
        while startIndex < endIndex {
            if nums[startIndex] < nums[endIndex] {
                return nums[startIndex]
            }
            
            let middleIndex = (startIndex + endIndex) / 2
            if nums[middleIndex] > nums[startIndex] {
                startIndex = middleIndex + 1
            } else {
                endIndex = middleIndex
            }
        }
        return nums[startIndex]
    }
}
```