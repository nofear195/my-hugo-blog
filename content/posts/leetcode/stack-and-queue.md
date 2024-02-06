---
title: "[ Leetcode ] Stack and Queue"
date: 2024-02-04
lastmod: 2024-02-06
draft: false
authors: ["nofear195"]
description: ""
categories: ["Leetcode"]
tags: ["Leetcode","Python","JavaScript"]
lightgallery: true

---


<!--more-->

## Concept

Stack & Queue 兩種資料結構，普遍用於儲存資料，已達成特定目的與行為操作

### Similarities

- 為線性結構，資料像一條線沿著順序下去做儲存

- 為抽象的資料結構，普遍的程式語言不會去實作，因此被稱作 **container adapters**，
  - 特性
    1. 可以用基本的 container class 做實現，常用的有 list 、 deque (double-ended queue)
    2. 只用於提供特定的資料訪問方式，Stack (LIFO)、Queue (FIFO)
  - 優點
    1. 因為是由其他容器做實現，可以省去記憶體配置的問題，相對的相鄰的兩個數，在實際記憶體的位置不一定是相鄰的
    2. 抽象化，使用者可以專注於對資料的操作 LIFO、FIFO，省去實作細節
    3. 因應開發需求 (效能優化、記憶體配置) 選擇實現方式

#### 補充: deque (double-ended queue)

- **可用於實作 Stack 與 Queue 的超猛利器**
- 為雙邊的 queue 尾巴結構，同時結合 Stack 與 Queue 特性，可以在兩端進行操作
- 是基本的 container class ，普遍的程式語言都會去實作

### Differences

- Stack
  - 資料存取方式 ： FIFO (Last-In-First-Out)
  - 常見應用
    1. 撤銷/重做：在文字編輯器中，撤銷操作會將最後輸入的文字刪除，而重做操作會將之前刪除的文字重新插入。這兩個操作可以用 Stack 來實現。
    2. 與 Recursion 為共生關係，當一個函數呼叫另一個函數時，呼叫者的狀態會儲存在呼叫堆疊中。當呼叫完成時，狀態將從堆疊中恢復，從而允許程式恢復執行。

- Queue
  - 資料存取方式 ： FIFO (First-In-First-Out)
  - 常見應用
    1. 排隊系統，需要顧及先來後到的順序處理問題或服務

## Python implement

### list 實現 Stack

```python

stack = []
stack.append(10)
stack.append(20)
top_item = stack.pop() #20
len(stack) #1

```

### deque 實現 Stack

- deque 由標準函式庫 collections 提供實作
  - 通常是大型 Stack 的首選，因為它提供更快的兩端追加和彈出操作

```python

from collections import deque

stack = deque()
stack.append(10)
stack.append(20)
top_item = stack.pop() #20
len(stack) #1

```

### deque 實現 Queue

```python

from collections import deque

queue = deque()
queue.append(10)
queue.append(20)
first_item = queue.popleft() #10
len(stack) #1

```

## Base Questions

- [232. Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/description/)

- [225. Implement Stack using Queues](https://leetcode.com/problems/implement-stack-using-queues/description/)

- [20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/description/)

- [1047. Remove All Adjacent Duplicates In String](https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string/description/)

- [150. Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/description/)

- [239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/description/)

- [347. Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/description/)

## Review

- 要時刻考慮當前使用的容器是否為空的情形
- [20, 150] 會用到額外儲存空間 (set, dictionary) 去辨識特殊符號
- Python 中 set, dictionary 用相同的 **{ }** 符號放變數

  ```python
    _set = {'(', '[', '{'} # 只有值
    _dict = {')': '(', ']': '[', '}': '{'} # key-value 配對

    ### 判斷變數是否在 set or dict 中，語法一樣
    if '(' in _set:
        print('yes')
    else:
        print('no')
  ```

- [239] **魔王題**
  - 不需要保留所有輸入的值，只保留必要即可
  - 同理，輸出值只需要檢查當前值是否在 queue 最前端，若是則從 queue 移除
  - 使用到 monotonic queue 去保持 queue 內的值順序
    - monotonic queue (單調佇列):
      - queue 的變形結構，強調 queue 內的值需保持嚴格遞增或嚴格遞減，其他操作相同
      - use case:
        - Dynamic programming : 透過在特定視窗內維護相關的最小值或最大值來最佳化計
        - 找 nearest 元素：有效辨識範圍內符合特定標準的元素
        - Scheduling algorithms :  根據某些值管理優先權任務，確保它們遵循所需的順序
        - Sliding window problems : 解決在動態視窗內維持某些最小值或最大值至關重要的問題

- [347] 使用到 Mini Heap 樹狀結構
  - Python 有標準函式庫支援 heapq

    ```python

        import heapq

        heap = []

        # 把 item push 進 heap 中，並保持最小元素在 root
        heapq.heappush(heap,item)

        # 把 最小值從 heap root 中移出，由第二小的替代
        heapq.heappop(heap)

    ```
