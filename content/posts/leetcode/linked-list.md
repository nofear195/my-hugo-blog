---
title: "[ Leetcode ] Linked List"
date: 2024-04-05
lastmod: 2024-04-05
draft: false
authors: ["nofear195"]
description: ""
categories: ["Leetcode"]
tags: ["Leetcode","Python"]
lightgallery: true

---


<!--more-->

## Operations

### Definition

  ```Python
 class ListNode:
    def __init__(self, val = 0, next = None):
        self.val = val
        self.next = next

  ```

### 性能分析表

  | |insert/delete|search| scenario|
  |---| --- | ---|---|
  |Array|O(n)|O(1)|fixed data size, frequent search, less add/delete|
  |Linked List|O(1)|O(n)|float data size, less search, frequent add/delete|

## Base Questions

### Dummy Head ListNode

- 在原始 Linked list 前端增加一個虛擬節點(dummy node)，可以省去為第一個節點所增加的判斷邏輯

- [203. Remove Linked List Elements](https://leetcode.com/problems/remove-linked-list-elements)

  ```Python
  def removeElements(head: Optional[ListNode], val: int) -> Optional[ListNode]:
    
    dummyHead = ListNode(next = head)

    curr = dummyHead
    while curr.next:
      if curr.next.val == val:
        curr.next = curr.next.next
      else:
        cur = curr.next
    return dummyHead.next # 回傳新 node 的頭
  ```

  - Time complaxity : O(n)
  - Space complaxity : O(1)

- [24. Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs)

  - 先畫圖演練順序

  ```Python
  def swapPairs(head: Optional[ListNode]) -> Optional[ListNode]:
    dummyHead = ListNode(0, head)

    curr = dummyHead
    while curr.next and curr.next.next:
      tmp1 = curr.next # 1
      tmp2 = curr.next.next # 2

      curr.next = curr.next.next # curr -> 2
      curr.next.next  = tmp1 # 2 -> 1 
      curr.next.next.next = tmp2 # 1 -> 3

      curr = curr.next.next # curr 移至 3
    return dummyHead.next
  ```

  - Time complaxity : O(n)
  - Space complaxity : O(1)

### Two pointers

- [19. Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list)

  - 運用快慢指針，讓快指針先走 n + 1 step，再讓快慢指針同時一步一步走，值到快指針為空
  - `n + 1` step : 讓慢指針最後剛好停留在要刪除的節點前一個位置，方便執行操作

  ```Python
  def removeNthFromEnd(head: Optional[ListNode], n: int) -> Optional[ListNode]:
    dummyHead = ListNode(0, head)
    slow = fast = dummyHead

    for _ in range(n + 1):
      fast = fast.next
    
    while fast:
      fast = fast.next
      slow = slow.next

    slow.next = slow.next.next # 讓慢指針很輕易的連到下下個節點
    return dummyHead.next
  ```

  - Time complaxity : O(n)
  - Space complaxity : O(1)
- Related
  - [160. Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists)

### Recursive and Iterative

- [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list)

  - Iterative

  ```Python
  def reverseList(head: Optional[ListNode]) -> Optional[ListNode]:
    prev = None
    curr = head
    
    while curr:
      temp = curr.next
      curr.next = prev
      prev = curr
      curr = temp
    return prev
  ```

  - Time complaxity : O(n)
  - Space complaxity : O(1)

  - Recursive

  ```Python
  def reverseList(head: Optional[ListNode]) -> Optional[ListNode]:
    
    def reverse(prev, curr):
      if not curr:
        return prev
      
      temp = curr.next
      curr.next = prev
      return reverse(curr, temp)
    
    return reverse(None, head)
  ```

  - Time complaxity : O(n) (node * n)
  - Space complaxity : O(n) (space * n)

### Linked List Cycle

- [142. Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii)

  - 判斷是否有 cycle:
    - 用快慢指針：設定指針步伐：(慢＊1，快＊2)，若有環產生，則快慢指針一定會在環中相遇
  - 如果有環，如何找到環入口: (數學推導 x: 環入口， y: 環入口到相遇點，ｚ:相遇點到環入口)
    - 快指針走的步伐為慢指針的兩倍 : 2 * (x + y)
    - 2 * (x + y) = x + y + n(y + z)
    - x + y = n(y + z)
    - x = n(y + z) - y
    - x = n(n - 1)(y + z) + z
    - if n = 1 => x = z
    - 從起點出發一個指針，從相遇點也出發一個指針，兩個指針每次都只走一步，當兩個指針相遇時，就是環的入口點

  ```Python
  def detectCycle(head: Optional[ListNode]) -> Optional[ListNode]:
    slow = fast = dummyHead

    while fast and fast.next:
      slow = slow.next # 每次走一步
      fast = fast.next.next # 每次走二步

      if slow == fast:  # 快慢指針相遇，表示一定有環
        tmp1 = fast
        tmp2 = head
        while tmp1 != tmp2: # 找 x = z，相遇點
          tmp1 = tmp1.next
          tmp2 = tmp2.next
        return tmp2
    return None
  ```

  - Time complaxity : O(n)
  - Space complaxity : O(1)

## Reference

[代码随想录](https://github.com/youngyangyang04/leetcode-master)
