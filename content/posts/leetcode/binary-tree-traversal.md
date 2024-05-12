---
title: "[ Leetcode ] Binary Tree - Traversal"
date: 2024-04-08
lastmod: 2024-04-24
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
 class TreeNode:
    def __init__(self, val = 0, left = None, right = None):
        self.val = val
        self.left = left
        self.right = right
  ```

### Way of Traversal

1. Depth-first traversal:
    - 先往深度走，遇到 leaf node 再往回走
    - Inorder traversal (左`中`右)
    - Preorder traversal (`中`左右)
      - 隱含由當前節點往下探索概念，適合找 binary tree `深度`
    - Postorder traversal (左`右`中)
      - 隱含找當前節點的 parent 向上延伸概念，適合找 binary tree `高度`
2. Breadth-first traversl: (level-order-traversal)
    - 逐層遍歷

### Recursive Steps

1. 確定遞迴涵式的`參數`與`返回值`
    - 確立在過程中哪些參數是需要做運算或是儲存等動作
    - 確立每個遞迴要返回的值，從而確定返回的型態
2. 確定終止條件
    - 確立終止遞迴的方法，保存單層內容並返回上一層，慎防 stack overflow
3. 確定單層遞迴的邏輯
    - 確立在每一層需要處理的邏輯，會重複在每一層中使用到

## Base Questions

### Depth-first traversal

- [144. Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal)

  - Recursive
  - 在單層遞迴邏輯中，依照順序處理遍歷方式 (preorder : 中左右)

  ```Python
  def preorderTraversal(root: Optional[TreeNode]) -> List[int]:

    # step 1 :確定參數與返回值
    def traversal(current: Optional[TreeNode], result: List[int]) -> None: 
      # step 2 :確立終止條件
      if current == None: return 
      # step 3 :每層要處理的事項
      result.append(current.val) # 中
      taversal(current.left, result) # 左
      taversal(current.right, result) # 右
    
    result = []
    traversal(root, result)
    return result
  ```

  - Iterative
  - 使用 `Stack` 儲存遍歷的次序
  - 儲存順序為先右再左節點，取出時才會是正確的遍歷順序 (左->右)
  - 使用空節點標記下一個要遍歷的節點 : 標記法 (適用所有排序)

  ```Python
  def preorderTraversal(root: Optional[TreeNode]) -> List[int]:
    if root is None: return []

    stack, result = [], []
    stack.append(root)
    while stack:
      node = stack[-1]
      if node:
        stack.pop()
        # 可以由下往上確認遍歷順序，中序：中左右 
        if node.right: stack.append(node.right) # 右 
        if node.left: stack.append(node.left) # 左
        stack.append(node) # 中
        # 加入空節點，用來辨識下一個要放進去遍歷結果的節點
        stack.append(None) 
      else:
        stack.pop() # 先行彈出空節點，下一個即是目標節點
        node = stack.pop() # 彈出目標節點
        result.append(node.val) # 執行單層所需操作
    return result
  ```

- Related
  - [145. Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal)
  - [94. Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal)

### Breadth-first traversl

- [102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal)

  - Recursive
  - 隱含 preorder的遍歷順序，用來構建出整個樹的結構

  ```Python
  def levelOrder(root: Optional[TreeNode]) -> List[List[int]]:

    # step 1 :確定參數與返回值
    def order(current: Optional[TreeNode], result: List[int], depth: int) -> None: 
      # step 2 :確立終止條件
      if current == None: return 
      # step 3 :每層要處理的事項
      if len(result) == depth:
        result.append([])
      result[depth].append(current.val) # 中
      order(current.left, result, depth + 1) # 左 (backtracking)
      order(current.right, result, depth + 1) # 右 (backtracking)
    
    result = []
    order(root, result, 0)
    return result
  ```

  - Iterative
  - 隱含 preorder的遍歷順序，用來構建出整個樹的結構
  - 使用 `Queue` 儲存遍歷的次序

  ```Python
  from collections import deque
  def levelOrder(root: Optional[TreeNode]) -> List[List[int]]:
    if root is None: return []
    result = []

    que = deque()
    que.append(root)
    while que:
      size = len(que) # que 長度會動態增減，要先固定初始大小
      temp = []
      for _ in range(size):
        node = que.popleft()
        temp.append(node.val) # 中
        if node.left: que.append(node.left) # 左
        if node.right: que.append(node.right) # 右
      result.append(temp)

    return result
  ```

- Related
  - [107. Binary Tree Level Order Traversal II](https://leetcode.com/problems/binary-tree-level-order-traversal-ii)
  - [199. Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view)
  - [637. Average of Levels in Binary Tree](https://leetcode.com/problems/average-of-levels-in-binary-tree)
  - [429. N-ary Tree Level Order Traversal](https://leetcode.com/problems/n-ary-tree-level-order-traversal)
  - [515. Find Largest Value in Each Tree Row](https://leetcode.com/problems/find-largest-value-in-each-tree-row)

### Depth of Binary Tree

- [111. Minimum Depth of Binary Tree](https://leetcode.com/problems/minimum-depth-of-binary-tree)

  - postorder traversal : root -> leaf 最小距離 = 最小深度
  - Recursive
    1. 參數與返回值
       - 參數：root，計算底下子樹最小深度
       - 返回值：深度
    2. 終止條件
       - 若節點為空，回傳高度為 0
    3. 單層邏輯
       - 若左子樹為空，右子樹不為空：最小深度為 1 + 右子樹深度
       - 若左子樹不為空，右子樹為空：最小深度為 1 + 左子樹深度
       - 若左右子樹皆不為空：最小深度為 1 + 左右子樹取最小值

  ```Python
  def minDepth(root: Optional[TreeNode]) -> int:
    
    def getDepth(node: Optional[TreeNoed]) -> int:
      if node is None: return 0

      leftDepth = getDepth(node.left) # 左
      rightDepth = getDepth(node.right) # 右

      if node.left is None and node.right: return 1 + rightDepth
      if node.left and node.right is None: return 1 + leftDepth
      return 1 + min(leftDepth + rightDepth) # 中
    
    return getDepth(root)
  ```

  - levelorder traversal
  - Iterative
    - 只有當左右節點皆為空時，遍歷才會是最低點 (其中一個節點不為空不算)

  ```Python
  from collections import deque
  def minDepth(root: Optional[TreeNode]) -> int:
    if root in None: return  0

    depth = 0
    que = deque([root])
    while que:
      size = len(que)
      depth += 1
      for _ in range(size):
        node = que.popleft()
        if node.left: que.append(node.left) # 左
        if node.right: que.append(node.right) # 右
        if node.left is None and node.right is None: # 中
          return depth
  ```

- Related
  - [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree)

### Invert Binary Tree

- Depth-first 與 Breadth-first 都可解決的狀況

- [226. Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree)

  - Recursive
  - preorder traversal
  - traversal 輸入參數與返回值恰好與題目相同，直接使用題目函式進行遞迴

  ```Python
  def invertTree(root: Optional[TreeNode]) -> Optional[TreeNode]:
    if root is None: return root

    root.left, root.right = root.right, root.left # 中
    invertTree(root.left) # 左
    invertTree(root.right) # 右
    return root
  ```

  - Iterative
  - preorder traversal

  ```Python
  def invertTree(root: Optional[TreeNode]) -> Optional[TreeNode]:
    if root is None: return root

    stack = []
    stack.append(root)
    while stack:
      node = stack[-1]
      if node:
        stack.pop()
        if node.right: stack.append(node.right) # 右
        if node.left: stack.append(node.left) # 左
        stack.append(node) # 中
        stack.append(None) #加入空節點
      else:
        stack.pop() #彈出空節點
        node = stack.pop() # 彈出目標節點
        node.left, node.right = node.right, node.left # 執行單層所需操作
    
    return root
  ```

  - level traversal

  ```Python
  from collections import deque
  def invertTree(root: Optional[TreeNode]) -> Optional[TreeNode]:
    if root is None: return root

    que = deque()
    que.append(root)
    while que:
      size = len(que)
      for _ in range(size):
        node = que.popleft()
        node.left, node.right = node.right, node.left # 中
        if node.left: que.append(node.left) # 左
        if node.right: que.append(node.right)  # 右

    return root
  ```

## Epilogue

- tree 的操作牽涉到許多重複的行為，通常需要熟悉遞迴與迭代的技巧

## Reference

[代码随想录](https://github.com/youngyangyang04/leetcode-master)
