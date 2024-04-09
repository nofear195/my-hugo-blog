---
title: "[ Leetcode ] String"
date: 2024-04-03
lastmod: 2024-04-03
draft: false
authors: ["nofear195"]
description: ""
categories: ["Leetcode"]
tags: ["Leetcode","Python"]
lightgallery: true

---


<!--more-->

## Operations

- string 是由一序列的字母組成，多數的在 Array 的操作同樣可以用在 string

```python
# ASCII 數字與字母間轉換
# ord : 字母轉 ASCII code
char = 'A'
ord(char) # 65

# chr : ASCII code 轉字母
num = 97
chr(num) # 'a'

# python swap 內容方法
a[i], a[j] = a[j], a[i]

# 字串內反轉方法(不改變原始內容 reversed)
# 先轉成 list 轉換後再用 join 轉回
before = list("string")
before[1:] = reversed(before[1:])
after = ''.join(before) # sgnirt

# 用 reverse() 會改變原始內容
a = 'hello world'.split() # ['hello', 'world']
a.reverse() # ['world', 'hello']
b = ''.join(a) # worldhello

```

## Classic Questions

### Reverse String

- [151. Reverse Words in a String](https://leetcode.com/problems/reverse-words-in-a-string)

  ```Python
  def reverseWords(s: str) -> str:
    words = s.split()
    left, right = 0, len(words) - 1 # two pointers
    
    while left < right:
        words[left], words[right] = words[right], words[left]
        left += 1
        right -= 1

    return ' '.join(words)
  ```

  - Time complaxity : O(n)
  - Space complaxity : O(1)
- Related
  - [344. Reverse String](https://leetcode.com/problems/reverse-string)
  - [541. Reverse String II](https://leetcode.com/problems/reverse-string-ii)

### KMP

- 主要用於兩個字串的匹配上，當出現的字串不匹配时，可以由一部分之前已经匹配的文本内容往回推，避免從頭做匹配
- 預先為子字串建立一個 next 的陣列，方便在主字串尋找匹配時可快速跳回去
- next 陣列 : 前綴表 => 紀錄最長的公共前后缀 (類似迴文 找最長對稱的字串)
- next 陣列內容放置可被跳過的個數，剛好可以跳到剛好的 index 上 (index = 可跳過的個數)
- 使用 next 陣列，若當前字母沒配對成功，則找 next 陣列 (index - 1) 的內容將匹配內容往前跳

- [28. Find the Index of the First Occurrence in a String](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string)

  ```Python
  def strStr(haystack: str, needle: str) -> int:
    def buildNext(s):
        next = [0]
        j = 0
        for i in range(1, len(s)):
            while j > 0 and s[i] != s[j]:
                j = next[j - 1] # index - 1 紀錄可跳過比較的個數
            if s[i] == s[j]:
                j += 1
            next.append(j)
        return next

    next = buildNext(needle)
    j = 0
    for i in range(len(haystack)):
        while j > 0 and haystack[i] != needle[j]:
            j = next[j - 1] # index - 1 紀錄可跳過比較的個數
        if haystack[i] == needle[j]:
            j += 1
        if j == len(needle):
            return i - j + 1
    return -1
  ```

  - Time complaxity : O(n + m)
  - Space complaxity : O(m) [ m 為字符串 needle 的前缀表]
- Related
  - [459. Repeated Substring Pattern](https://leetcode.com/problems/repeated-substring-pattern)

## Reference

[代码随想录](https://github.com/youngyangyang04/leetcode-master)
