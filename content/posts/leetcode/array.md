---
title: "[ Leetcode ] Array"
date: 2024-03-28
lastmod: 2024-03-28
draft: false
authors: ["nofear195"]
description: ""
categories: ["Leetcode"]
tags: ["Leetcode","Python"]
lightgallery: true

---


<!--more-->

## Operations

- 主要用 `list` 實現 Array 的各種操作
- 有提供 `Array` module，不常用，主要用於儲存大量數據時，記憶體優化方面

```python
# initial
arr = [1, 2, 3]

# operations
arr.append(4) # [1, 2, 3, 4]
app.pop() # [1, 2, 3]
len(app) # 3
arr.sort() # [1, 2, 3]
arr.reverse() # [3, 2, 1]

# shallow copy 
# (大多用於複製原始 array 中 immutable 值 ex: integer, string )
arr.copy()
arr[:]

# traversal
for value in arr:
  print(value)

for index, value in enumerate(arr):
  print(index, value)
```

## Classic Questions

### Binary Search

- 使用時機：內容為`有序`且`無重複元素`
- 注意邊界條件：右方閉闔區域
- [704. Binary Search](https://leetcode.com/problems/binary-search)

  ```Python
  # 1. [left, right]
  def search(nums: List[int], target: int) -> int:
    left, right = 0 ,len(nums) - 1

    while left <= right: 
      mid = left + (right - left) // 2
      if nums[mid] > target:
        right = mid - 1 # 重點
      elif nums[mid] < target:
        left = mid + 1 # 重點
      else:
        return mid
    
    return -1

  # 2. `[left, right)`
  def search(nums: List[int], target: int) -> int:
    left, right = 0 ,len(nums)

    while left < right:
      mid = left + (right - left) // 2
      if nums[mid] > target:
        right = mid # 重點
      elif nums[mid] < target:
        left = mid + 1 # 重點
      else:
        return mid
    
    return -1
  ```

  - Time complaxity : O(logn)
  - Space complaxity : O(1)
- Related
  - [35. Search Insert Position
](https://leetcode.com/problems/search-insert-position/description/)
  - [34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/description/)
  - [69. Sqrt(x)](https://leetcode.com/problems/sqrtx/description/)
  - [367. Valid Perfect Square](https://leetcode.com/problems/valid-perfect-square/description/)

### Two pointers

- 使用快慢兩個 pointers (slow, fast) 在一個 for loop 中，完成兩個 for loop 的工作量

- [27. Remove Element](https://leetcode.com/problems/remove-element)

  ```Python
  def removeElement(nums: List[int], val: int) -> int:
    slow = fast = 0

    while fast < len(nums):
      if nums[fast] != val:
        nums[slow] = nums[fast]
        slow += 1
      fast += 1

    return slow
  ```

  - Time complaxity : O(n)
  - Space complaxity : O(1)
- Related
  - [26. Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array)
  - [283. Move Zeroes](https://leetcode.com/problems/move-zeroes)
  - [844. Backspace String Compare](https://leetcode.com/problems/backspace-string-compare)
  - [977. Squares of a Sorted Array](https://leetcode.com/problems/squares-of-a-sorted-array)

### Sliding window

- two pointers 應用之一，根據當前子序列情況，動態調整子序列的起始與終止位置，從而降低時間複雜度
- key points:
  1. 子序列的內容為何
  2. 如何移動 window 的起始位置
  3. 如何移動 window 的終止位置
- [209. Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum)

  ```Python
  def minSubArrayLen(target: int, nums: List[int]) -> int:
    res = float('inf')
    i = j = 0
    sum = 0

    for j in range(len(nums)): # 如何移動 window 的起始位置
      sum += nums[j] # 子序列的內容為何

      while sum > target: # 如何移動 window 的終止位置
        sumLength = j - i + 1
        res = min(res, sumLength)
        i += 1 # 重點

    # 重點: 確認res被赋值有無
    if res != float('inf'): 
      return res
    else:
      return 0
  ```

  - Time complaxity : O(n)
  - Space complaxity : O(1)
- Related
  - [904. Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets)
  - [76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring)

## Reference

[代码随想录](https://github.com/youngyangyang04/leetcode-master)
