---
title: "[ Leetcode ] Hash Table"
date: 2024-04-04
lastmod: 2024-04-04
draft: false
authors: ["nofear195"]
description: ""
categories: ["Leetcode"]
tags: ["Leetcode","Python"]
lightgallery: true

---


<!--more-->

## Operations

- 額外建立的一個對照表格，用於快速判斷某一元素是否有出現過，或是統計出現的次數
- 因應使用情景會採用三種資料結構
  1. array : 特定時機，在有限空間下，如用長度為 26 的陣列儲存各字母出現次數
  2. set : 紀錄變數是否出現過 (減少用 array 儲存時大多值為空時，造成的儲存空間浪費)
  3. Map : 紀錄變數是否出現過以及紀錄出現次數

```python
# 作爲字母出現頻率的表格
record = [0] * 26
# 用相對於 'a','A' ASCII 數字，找到其他字母儲存的 index
record[ ord('b') - ord('a')] += 1 # b 位置的值加一

# set , list 互轉
list({1, 2, 3, 4, 5})
set([1, 2, 3, 3, 4, 5, 5])

# dict 基本操作

## initialization
m_dict = {} 
y_dict = {key1: value1, ...}

## element accessings
y_dict[key1] = value
value = y_dict[key1]
value = y_dict(key1, 'default_value')

## lterating
for key in y_dict:
  print(key)
for value in y_dict.values():
  print(value)
for key, value in y_dict.items():
  print(key, value)

## checking membership
if key in y_dict:

## size
len(y_dict)

# set 基本操作

## initialization
m_set = set()
y_set = {element1, element2, ...}

## element accessings
y_set.add(element)

## lterating
for element in y_set:
  print(element)

## checking membership
if element in y_set:

## size
len(y_set)

```

## Classic Questions

### Array as a Hash Table

- [242. Valid Anagram](https://leetcode.com/problems/valid-anagram)

  ```Python
  def sAnagram(s: str, t: str) -> bool:
    record = [0] * 26 # store frequent

    for i in s:
      record(ord(i) - ord("a")) += 1
    for i in t:
      record(ord(i) - ord("a")) -= 1
    for i in range(26):
      if record[i] != 0: return False

    return True
  ```

  - Time complaxity : O(n)
  - Space complaxity : O(1)
- Related
  - [383. Ransom Note](https://leetcode.com/problems/ransom-note)
  - [49. Group Anagrams](https://leetcode.com/problems/group-anagrams)
  - [438. Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string)
  - [1002. Find Common Characters](https://leetcode.com/problems/find-common-characters)

### Set as a Hash Table

- [202. Happy Number](https://leetcode.com/problems/happy-number)

  ```Python
  def isHappy(n: int) -> bool:
    
    def getSum(n):
      total = 0
      while n:
        total += (n % 10)**2
        n //= 10
      return total

    record = set()
    while n not in record:
      if n == 1: return True
      record.add(n)
      n = getSum(n)

    return False
  ```

  - Time complaxity : O(logn)
  - Space complaxity : O(logn)
- Related
  - [349. Intersection of Two Arrays](https://leetcode.com/problems/intersection-of-two-arrays)
  - [350. Intersection of Two Arrays II](https://leetcode.com/problems/intersection-of-two-arrays-ii)

### Map as a Hash Table

- [454. 4Sum II](https://leetcode.com/problems/4sum-ii)

  - input 為四個獨立的 list，不需考慮重複的四個元素組合為 0 情形

  ```Python
  def fourSumCount(nums1: List[int], nums2: List[int], nums3: List[int], nums4: List[int]) -> int:
    table = {}
    count = 0

    for n1 in nums1:
      for n2 in nums2:
        key = n1 + n2
        table[key] = table.get(key, 0) + 1
    
    for n3 in nums3:
      for n4 in nums4:
        key = - (n3 + n4)
        if key in table:
          count += table[key]
    return count
  ```

  - Time complaxity : O(n^2)
  - Space complaxity : O(n^2) (worst case: A, B 值不相同，組合數量 n^2)
- Related
  - [1. Two Sum](https://leetcode.com/problems/two-sum)

- Map 與 two pointers 使用權衡

- [15. 3Sum](https://leetcode.com/problems/3sum)

  - 在同一個 list 的組合需考慮重複組合的情形，適用雙指針效率較高

  ```Python
  def threeSum(self, nums: List[int]) -> List[List[int]]:
    ans = []
    nums.sort() # 先排序為接下的指針比較做準備

    for i in range(len(nums)):
      if nums[i] > 0:
        return ans
      if i > 0 and nums[i] == nums[i - 1]: # 去除重複情形
        continue
      
      left = i + 1
      right = len(nums) - 1
      while left < right:
        Sum = nums[i] + nums[left] + nums[right] 
        if Sum > 0:
          right -= 1
        elif Sum < 0:
          left += 1
        else:
          ans.append([nums[i], nums[left], nums[right]])

          # 去除重複情形
          while left < right and nums[right] == nums[right - 1]: 
            right -= 1
          while left < right and nums[left] == nums[left + 1]:
            left += 1
          
          right -= 1
          left += 1

    return res
  ```

  - Time complaxity : O(n^2)
  - Space complaxity : O(1)
- Related
  - [18. 4Sum](https://leetcode.com/problems/4sum)

## Reference

[代码随想录](https://github.com/youngyangyang04/leetcode-master)
