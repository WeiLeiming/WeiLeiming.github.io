---
title: LeetCode--Merge Sorted Array
date: 2017-06-15 09:46:47
categories: Algorithm
tags:
	- Algorithm
	- LeetCode
typora-copy-images-to: ipic
---

# 88. Merge Sorted Array

## Description

Given two sorted integer arrays *nums1* and *nums2*, merge *nums2* into *nums1* as one sorted array.

## Example

**Note:**

You may assume that *nums1* has enough space (size that is greater or equal to *m* + *n*) to hold additional elements from *nums2*. The number of elements initialized in *nums1* and *nums2* are *m* and *n* respectively.

<!-- more -->

Input:

```swift
nums1: [3,0]
m: 1
nums2: [1]
n: 1
```

answer:

```swift
nums1: [1,3]
```
## 解题思路

nums1和nums2都已排好序。所以只需要从最大数往前遍历比较即可。

## 代码

### 一、

这题是比较简单的，写法如下。至于判断条件先后产生的不同写法，我也就懒得赘述了。

```swift
class Solution {
    func merge(_ nums1: inout [Int], _ m: Int, _ nums2: [Int], _ n: Int) {
        var i = m - 1
        var j = n - 1
        var k = m + n - 1
        
        while k >= 0 {
            if i >= 0 && j >= 0 {
                if nums1[i] > nums2[j] {
                    nums1[k] = nums1[i]
                    i -= 1
                } else {
                    nums1[k] = nums2[j]
                    j -= 1
                }
            } else if i >= 0 {
                nums1[k] = nums1[i]
                i -= 1
            } else if j >= 0 {
                nums1[k] = nums2[j]
                j -= 1
            }
            k -= 1
        }
    }
}
```