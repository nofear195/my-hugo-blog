---
title: "從測試中學習 Go 語言 (資料集合篇)"
date: 2024-02-20
lastmod: 2024-02-21
draft: false
authors: ["nofear195"]
description: ""
categories: ["Go"]
tags: ["Go","Test","TDD"]
lightgallery: true

---

藉由 Go 內建完整的測試系統，同時學習 Go 程式語言與測試開發流程

<!--more-->

## Array

- 大小固定，宣告後即無法變更
- 存放的元素在記憶體中是連續的
- 使用時機：
  - 當元素數量在編譯時固定且已知時
  - 需要直接存取記憶體時
  - 需要更好的效能或直接記憶體存取時
- examples
  - 作為演算法中的向量、矩陣或固定大小的緩衝區
  - 定義硬體介面或低階程式設計中所使用的資料結構的形狀
- 長度是其類型的一部分

  ```go
    var arr [5]int
    arr = [2]int{1,2} // compile error : [2]int 與 [5]int 視為不同的類型
  ```

### Declaration

| Declaration | Values |
| ------|------|
| `arr := [5]int{1, 2, 3, 4, 5}`| [1 2 3 4 5]|
| `arr := [...]int{1, 2, 3, 4, 5}`| [1 2 3 4 5]|
| `var arr [5]int` | [0 0 0 0 0]|
| `arr := [5]int{1: 10, 3: 30}`| [0 10 0 30 0] |

### Operation

| Operation | Code Snippet| Output  |
| :------:|------|------|
| **Declaration**        | `array := [5]int{1, 2, 3, 4, 5}`| [1 2 3 4 5]  |
| **Length**             | `len(array)` | 5|
| **Create**             | Not applicable to arrays | |
| **Read**      | `array[2]`   | 3             |
| **Update**             | `array[2] = 10`  | [1 2 10 4 5] |
| **Delete**             | Not applicable to arrays | |
| **Iteration**           | `for index, value := range array`  </br>`{...}`|  can ignore the index value by using _ blank identifier |

## Slice

- 可動態調整長度
- 存放的元素在記憶體中是連續的
- 使用 `append` 回傳新的 Slice 用來刪除元素
- 使用時機：
  - 當元素的數量是動態的或在編譯時未知時
  - 處理可變長度資料時，例如來自外部來源的輸入 (parameters from function input)
  - 需要靈活管理可成長或縮減的集合時
- examples
  - 處理可變長度序列時
  - 實作 stack、 queue 或 dynamic array 等資料結構時

### Declaration

| Declaration | Values |
| ------|------|
| `slice := []int{1, 2, 3, 4, 5}` | [1 2 3 4 5] |
| `slice := make([]int, 5)`  |  [0 0 0 0 0] |
| `slice := make([]int, 0, 5)` | []   |
| `arr := [...]int{1, 2, 3, 4, 5}; slice := arr[1:4]`|  [2 3 4]   |
| `slice := []int{}` |  []   |

### Operation

| Operation | Code Snippet| Output  |
| :------:|------|------|
| **Declaration**        | `slice := []int{1, 2, 3, 4, 5}`| [1 2 3 4 5]  |
| **Length**             | `len(slice)` | 5|
| **Create**             | `slice = append(slice, 6)`  | [1 2 3 4 5 6]|
| **Read**      | `slice[2]` | 3 |
| **Update**  | `slice[2] = 10` | [1 2 10 4 5 6]            |
| **Delete**             | `index := 2`</br>`slice = append(slice[:index], slice[index+1:]...)` |  [1 2 4 5 6] |
| **Iteration**           | `for index, value := range slice`  </br>`{...}`| can ignore the index value by using _ blank identifier |

## Map

- 可動態調整長度
- 存放的元素在記憶體中是不連續的
- 使用 `delete` 刪除元素
- 本質上是一個指標，當其被放入 function/method 時，是複製指標，而非指向的真實的資料
- 使用時機：
  - 作為 disctionary、hash table 時
  - 當處理需要透過唯一鍵索引的資料集合時
  - 用於儲存鍵和值之間的關係很重要的資料
- examples
  - 基於唯一識別碼建立查找表以實現高效的資料檢索
  - 實現快取以快速存取經常存取的資料
- 用 key 去查找 map 映射的值時，有兩個回傳值 (value, ok)
  - ```value, ok := myMap[key]```

  ```go
  m := map[string]int{"one": 1, "two": 2, "three": 3}

  value, ok := m["zero"] 
  // if 只要查 key 是否有在裡面可用 _ 不檢索其值
  // _, ok := m["zero"]

  if ok {
    fmt.Println("Value:", value)
  } else {
    fmt.Println("Key not found", value) // value == 0
  }
  ```

### Declaration

| Declaration | Values |
| ------|------|
| `m := map[string]int{"one": 1, "two": 2, "three": 3}`| {"one": 1, "two": 2, "three": 3} |
| `m := make(map[string]int)` |  {} |
| `m := map[string]int{}`|  {} |

### Operation

| Operation | Code Snippet| Output  |
| :------:|------|------|
| **Declaration**        | `m := map[string]int{"one": 1, "two": 2, "three": 3}` |  map[four:4 one:1 three:3 two:2] |
| **Length**             | `len(m)` | 3|
| **Create**             | `m["four"] = 4`  |  map[four:4 one:1 three:3 two:2]    |
| **Read**      | `m[two]` </br> `value, ok := m[two]` </br>  `value, ok := m[zero]`  | 2 </br> value: 2, ok: true </br>  value: 0, ok: false              |
| **Update**             | `m["two"] = 20`  | map[four:4 one:1 three:3 two:20] |
| **Delete**             | `delete(m, "three")` | map[four:4 one:1 two:20] |
| **Iteration**           | `for key, value := range m`  </br>`{...}`| can ignore the key value by using _ blank identifier |

## 比較資料集合是否相同

- reflect 套件 DeepEqual 函式：```reflect.DeepEqual(A, B)```
  - 可用來比較 array, slice , map 型態
  - 原理是用遞回的方式比較 A, B 兩個值的元素，如果它們深度相等則傳回 true
  - 使用時不會預先檢查比較的 A, B 型態是否相同
  - 用於大型資料結構時，需注意效能影響
  
  ```go
  import "reflect"

  got := "test"
  want := []int{0, 9}

  if !reflect.DeepEqual(got, want) {
        t.Errorf("got %v want %v", got, want)
  } // raise an error
  ```

- ```slices.Equal(A, B)```
  - GO 1.21 後的標準 package
  - 用來比較 A, B 兩個值的元素，其長度與內容是否相同
  - 使用時會預先檢查比較的 A, B 型態是否相同

  ```go
  import "slices"

  got := []int{0, 1}
  want := []int{0, 9}

  if !slices.Equal(got, want) {
        t.Errorf("got %v want %v", got, want)
  } // raise an error
  ```

## Reference

- [https://quii.gitbook.io/learn-go-with-tests/](https://quii.gitbook.io/learn-go-with-tests/)
- [If a map isn’t a reference variable, what is it?](https://dave.cheney.net/2017/04/30/if-a-map-isnt-a-reference-variable-what-is-it)
