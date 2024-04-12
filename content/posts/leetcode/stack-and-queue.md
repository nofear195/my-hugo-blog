---
title: "[ Leetcode ] Stack and Queue"
date: 2024-04-06
lastmod: 2024-04-06
draft: false
authors: ["nofear195"]
description: ""
categories: ["Leetcode"]
tags: ["Leetcode","Python"]
lightgallery: true

---


<!--more-->

## Operations

- Stack & Queue 為抽象的資料結構，程式語言不實作，用其他結構去實現，即 container adapters
- 一般會用 container class 實作 Stack & Queue，最常用 Deque 實作

### Deque (double-ended queue)

- 為雙邊的 queue 尾巴結構，常用來實現 Stack & Queue
- 包含在 `collections` 的模組中 (概念類似 C++ 的 STL (Standard Template Library) 會提供此結構)

```Python
from collections import deque

# Create a deque
c = deque()
d = deque([1, 2, 3, 4, 5])

# add element
d.append(6) # Append element to the right
d.appendleft(0) # Append element to the left

# remove element
rightmost = d.pop() # Remove and return the rightmost element
leftmost = d.popleft() # Remove and return the leftmost element

```

## Base Questions

### Implement

- [232. Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks)

  - 使用兩個 array 實現操作，一個負責輸入，一個負責輸出

  ```Python
  class MyQueue:

    def __init__(self):
        self.sIn = []
        self.sOut = []
        
    def push(self, x: int) -> None:
        self.sIn.append(x)
        
    def pop(self) -> int:
        while not self.sOut:
            while self.sIn:
                self.sOut.append(self.sIn.pop())
        return self.sOut.pop()
        
    def peek(self) -> int:
        res = self.pop()
        self.sOut.append(res)
        return res

    def empty(self) -> bool:
        return len(self.sIn) == 0 and len(self.sOut) == 0
  ```

- [225. Implement Stack using Queues](https://leetcode.com/problems/implement-stack-using-queues)

  1. 使用兩個 array 實現操作，一個負責輸入，一個負責輸出
  2. 使用一個 deque 實現操作

  ```Python
  from collections import deque

  class MyStack:

    def __init__(self):
        self.queue = deque()
        
    def push(self, x: int) -> None:
        self.queue.append(x)

    def pop(self) -> int:
        for _ in range(len(self.queue) - 1):
            self.queue.append(self.queue.popleft())
        return self.queue.popleft()

    def top(self) -> int:
        ans = self.pop()
        self.push(ans)
        return ans

    def empty(self) -> bool:
        return not self.queue
  ```

### Stack Operations

- [20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses)

  - 搭配 hash table，配對 stack 最上層 (stack[-1]) 與下一個進入者

  ```Python
  def isValid(s: str) -> bool:
    table = {')':"(", "}":"{", "]":"["}
    stack = []

    for i in s:
      if stack and i in table and table[i] === stack[-1]:
        stack.pop()
      else:
        stack.append(i)
 
    return not stack
  ```

  - Time complaxity : O(n)
  - Space complaxity : O(n)
- Related
  - [1047. Remove All Adjacent Duplicates In String](https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string)
  - [150. Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation)

### Monotonic Queue

- [239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum)

  - 使用到 monotonic queue 去保持 queue 內的值順序
  - 不需要保留所有輸入的值，只保留必要即可
  - 輸出時只需要檢查當前值是否在 queue 最前端，若是則從 queue 移除
  - monotonic queue (單調佇列):
    - queue 的變形結構，強調 queue 內的值需保持嚴格遞增或嚴格遞減，其他操作相同
    - use case:
      - Dynamic programming : 透過在特定視窗內維護相關的最小值或最大值來最佳化計
      - 找 nearest 元素：有效辨識範圍內符合特定標準的元素
      - Scheduling algorithms :  根據某些值管理優先權任務，確保它們遵循所需的順序
      - Sliding window problems : 解決在動態視窗內維持某些最小值或最大值至關重要的問題

  ```Python
  from collections import deque
  class MQueue:
      def __init__(self):
          self.que = deque()
      def pop(self, value):
          if self.que and value == self.que[0]:
              self.que.popleft()
      def append(self, value):# 保持單調遞增
          while self.que and value > self.que[-1]: 
              self.que.pop()
          self.que.append(value)
      def front(self):
          return self.que[0]
  ```

  ```Python
  def maxSlidingWindow(nums: List[int], k: int) -> List[int]:
    queue = MQueue()
    ans = []

    for i in range(k):
      queue.append(nums[i])
    ans.append(queue.front())

    for i in range(1, len(nums)):
      queue.pop(nums[i - k])
      queue.append(nums[i])
      ans.append(queue.front())
    
    return ans
  ```

  - Time complaxity : O(n)
  - Space complaxity : O(k)

### Min Heap

- [347. Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements)

  - 使用到 Mini Heap 樹狀結構，Python 有標準函式庫支援 heapq

  ```python
  import heapq
  heap = []
  # 把 item push 進 heap 中，並保持最小元素在 root
  heapq.heappush(heap,item)
  # 把 最小值從 heap root 中移出，由第二小的替代
  heapq.heappop(heap)

  ```

  ```Python
  import heapq
  def topKFrequent(nums: List[int], k: int) -> List[int]:
    table = {}
    for num in nums:
      table[num] = table.get(num, 0) + 1

    pri_que = [] 
    for key, freq in table.items():
      heapq.heappush(pri_que, (freq, key))
      if len(pri_que) > k:
        heapq.heappop(pri_que)

    res = []
    while len(pri_que):
        res.append( heapq.heappop(pri_que)[1])
    return res
  ```

  - Time complaxity : O(nlogk)
  - Space complaxity : O(n)

## Reference

[代码随想录](https://github.com/youngyangyang04/leetcode-master)
