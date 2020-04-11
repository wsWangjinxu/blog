---
title: 【leetcode刷题笔记】1.二分查找的变种
date: 2020-04-11 22:16:34
tags: 二分查找
category: Algorithm
---
二分查找是一个非常经典的查找算法。需要特别注意的点是要查找的内容一定是要线性存储，而且必须是有序的。下面是两道二分查找的变种题目：
+ [33. 搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)
+ [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)


## 搜索旋转排序数组
实现思路：这道题的数据非常有特点，数据在某个特定范围是有序的。中间有一个高点，比右边紧挨着的一个元素大。所以可以找到这个点，在左右两边分别进行二分查找。
找点的关键是要分清楚到底是在左边坡，还是在右边坡。
下面是代码实现：
```js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var search = function (nums, target) {
  // 只有一个元素
  if(nums[0] === target) {
      return 0
  }
  let left = 0;
  let right = nums.length - 1;
  let point = findPoint(left, right, nums)
  // 在旋转点左侧查找
  let leftRes = binarySearch(0, point, nums, target)
  // 在旋转点右侧查找
  let rightRes = binarySearch(point + 1, nums.length - 1, nums, target)
  return leftRes === -1 ? rightRes : leftRes
};

function binarySearch(left, right, nums, target) {
  let mid;
  while (left <= right) {
    mid = Math.floor((left + right) / 2);
    if (nums[mid] === target) {
      return mid;
    } else if (nums[mid] < target) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }
  return -1;
}

function findPoint(left, right, nums) {
    if(nums[left] < nums[right]) {
        return 0
    }
    let mid
    while(left <= right) {
        mid = Math.floor((left + right)/2)
        if(nums[mid] > nums[mid + 1]) {
            return mid
        } else {
            // 在右坡
            if(nums[mid] < nums[left] ) {
                right = mid - 1
            } else {
                // 在左坡
                left = mid + 1
            }
        }
    }
    return 0
}
```
## 在排序数组中查找元素的第一个和最后一个位置
实现思路：这道题目中要查找的target可能有多个，要想找到两边的初始值，需要在二分点，继续向左或者向右递归查找，如果没有找到，就分别将左、右端点的值返回，作为最靠近两端的值。
下面是代码实现：
```js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var searchRange = function(nums, target) {
    let left = 0
    let right = nums.length - 1
    let mid 
    let result = []
    // 第一次找到一个值
    let firstSearch = search(left, right, nums, target)
    if(firstSearch === false) {
        // 没有找到值
        return [-1, -1]
    } else {    
        // 尽可能向右找
        let resRight = search(firstSearch, right, nums, target, firstSearch - 1)
        // 尽可能向左找
        let resLeft = search(left, firstSearch - 2, nums, target, firstSearch - 1)
        return [resLeft -1, resRight - 1]
    }
};

function search(left, right, nums, target, preResult) {
    let mid
    while(left <= right) {
        mid = Math.floor((left + right) / 2)
        if(nums[mid] === target) {
            if(preResult < mid) {
                return search(mid + 1, right, nums, target, mid)
            } else {
                return search(left, mid - 1, nums, target, mid)
            }
        } else if(nums[mid] < target) {
            left = mid + 1
        } else {
            right = mid - 1
        }
    }
    return preResult + 1 || false
}
```
