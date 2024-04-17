---
title: "[ Leetcode ] String"
date: 2024-04-03
lastmod: 2024-04-17
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

# 字串內反轉方法(建立新的字串，不改變原始內容 reversed)
# 先轉成 list 轉換後再用 join 轉回
before = list("string")
before[1:] = reversed(before[1:])
after = ''.join(before) # sgnirt

# 用 reverse() 會改變原始內容
a = 'hello world'.split() # ['hello', 'world']
a.reverse() # ['world', 'hello']
b = ''.join(a) # worldhello

# 用 slice 語法反轉 字串與陣列 : 
# 參數 [start:stop:step] 
# [::-1] 
#  // 省略輸入 start, stop 用預設值 0 , len(list) ,  -1 表示由最後一個直往前遍歷
a = "hello world"
b = ['hello', 'world']

a[::-1] # dlrow olleh
b[::-1] # ['world', 'hello']

# 字串比大小 (用字串的字典順序 strings lexicographically) 
str1 = "apple"
str2 = "banana"
print(min(str1, str2))  # Output: "apple"

str3 = "cat"
str4 = "caterpillar"
print(min(str3, str4))  # Output: "cat"
```

### Python Naming Conventions

- 由 PEP8 定義的命名風格

1. 大寫+底線
   - 常數 : `CONSTANT_VALUE = 0`
2. camel case
   - class 名稱

   ```Python
   class MyClass:
    def my_method(self):
        pass
   ```

3. 小寫+底線
   - 變數 : variable, function, method, module (除了 1, 2 皆視為變數)
4. prefix 底線
    - 單底線 : (表示用不到的變數，單純放置佔位)

        ```python=
         for _ in range(10):
                print("hello")
        ```

    - 單底線 + 變數: (表示 class private variable) : 弱私有變數
         - 弱私有變數 : 用來表示 class 私有變數，但仍可被存取

        ```python=
        class MyClass:
            def _get_raw(self):
            print("Oh no")
        o = MyClass()
        o._get_raw()
        ```

    - 雙底線 + 變數:(表示 class private variable) : 強私有變數
        - 強私有變數: 用來表示 class 私有變數，但不可被存取
        - 若非要存取則可用 _ + class name + 變數命名

        ```python=
        class MyClass:
            def __get_raw(self):
            print("Oh no")
        o = MyClass()
        o.__get_raw() # runtime error
        o._MyClass__getraw() ## Oh no (python 用 name mangling 讓強私有可被存取)
        ```

    - 雙底線 + 變數 + 雙底線: (python magic method) 除用於 class 初始化 其餘不建議使用

        ```python
        class MyClass:
            def __init__(self):
                self.name = "bob"
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
