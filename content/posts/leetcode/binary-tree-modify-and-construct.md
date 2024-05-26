---
title: "[ Leetcode ] Binary Tree - Modify And Construct"
date: 2024-04-15
lastmod: 2024-05-15
draft: false
authors: ["nofear195"]
description: ""
categories: ["Leetcode"]
tags: ["Leetcode","Python"]
lightgallery: true

---


<!--more-->

## Base Questions

### Modify Binary Tree

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

### Construct Binary Tree

- [106. Construct Binary Tree from Inorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal)

  - concept : 用後序探訪的特性得知最後探訪的為根節點，搭配中序定位左右子樹，便可確立唯一二元樹
  - Recursive
    1. 參數與返回值
       - 參數：後序與中序的陣列
       - 返回值：唯一二元樹的根節點
    2. 終止條件
       - 後序陣列長度為０，探訪至葉節點，唯一二元樹建置完成
    3. 單層邏輯
       1. 從後序最後一個值作為當前根節點，建制新的子樹
       2. 找到根節點在中序的位置 index，作為切割點
       3. 切割中序陣列，切成中序的左右子樹陣列
       4. 切割後序陣列，切成後序的左右子樹陣列
       5. 遞回處理左右區間

  ```Python
  def buildTree(inorder: List[int], postorder: List[int]) -> Optional[TreeNode]:
    
    def traversal(inorder, postorder) -> Optional[TreeNode]:
        if len(postorder) == 0: return None

        rootValue = postorder[-1]
        root = TreeNode(rootValue)
        
        if len(postorder) == 1: return root

        delimiterIndex = inorder.index(rootValue)
        
        leftInorder = inorder[:delimiterIndex]
        rightInorder = inorder[delimiterIndex + 1:]

        postorder.pop()
        leftPostorder = postorder[:len(leftInorder)]
        rightPostorder = postorder[len(leftInorder):]

        root.left = traversal(leftInorder, leftPostorder)
        root.right = traversal(rightInorder, rightPostorder)

        return root
    if not inorder or not postorder: return None
    return traversal(inorder, postorder)
  ```

  - Related
    - [105. Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal)

- [654. Maximum Binary Tree](https://leetcode.com/problems/maximum-binary-tree)

  - Recursive
    1. 參數與返回值
       - 參數：當下節點所需元素的陣列
       - 返回值：用輸入陣列構成的二元樹根節點
    2. 終止條件
       - 輸入陣列長度為 1，即已探訪至葉節點
    3. 單層邏輯
       1. 找到輸入陣列最大值作為根節點
       2. 找到根節點在陣列的位置 index，作為切割點
       3. 以切割點建構左右子樹 (前提是要確保有值才建立子樹)

  ```Python
  def constructMaximumBinaryTree(nums: List[int]) -> Optional[TreeNode]:

    if len(nums) == 1:
        return TreeNode(nums[0])
    
    maxValue = max(nums)
    maxValueIndex = nums.index(maxValue)

    node = TreeNode(maxValue)

    if maxValueIndex > 0:  # 確保有值才能建立子樹
        node.left = self.constructMaximumBinaryTree(nums[:maxValueIndex])
    if maxValueIndex < len(nums) - 1: # 確保有值才能建立子樹
        node.right = self.constructMaximumBinaryTree(nums[maxValueIndex + 1:])
    
    return node
  ```

- [617. Merge Two Binary Trees](https://leetcode.com/problems/merge-two-binary-trees)

  - Recursive (以前序為例，最好理解)
    1. 參數與返回值
       - 參數：兩樹根節點
       - 返回值：合併後，樹的根節點
    2. 終止條件
       - 兩樹合併後，只會存在一棵樹，所以若一其一為空，則返回另一棵不為空的樹
    3. 單層邏輯
       - 因爲合併後的樹只有一顆，所以可用任一棵樹進行操作
       - 先更新當前節點值，再遞回更新子樹

  ```Python
  def mergeTrees(root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:

    if root1 is None: return root2
    if root2 is None: return root1

    root1.val += root2.val # 中
    root1.left = mergeTrees(root1.left, root2.left) # 左
    root1.right = mergeTrees(root1.right, root2.right) # 右
    return root1
  ```

  - Iterative
  - 使用 Queue 存放元素
  - levelorder traversal

  ```Python
  from collections import deque
  def mergeTrees(root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:

    if root1 is None: return root2
    if root2 is None: return root1
    que = deque([root1, root2])

    while que:
        node1 = que.popleft()
        node2 = que.popleft()

        node1.val += node2.val

        if node1.left is not None and node2.left is not None:
            que.append(node1.left)
            que.append(node2.left)

        if node1.right is not None and node2.right is not None:
            que.append(node1.right)
            que.append(node2.right)

        if node1.left is None and node2.left is not None:
            node1.left = node2.left
        
        if node1.right is None and node2.right is not None:
            node1.right = node2.right
    return root1
  ```

## Reference

[代码随想录](https://github.com/youngyangyang04/leetcode-master)