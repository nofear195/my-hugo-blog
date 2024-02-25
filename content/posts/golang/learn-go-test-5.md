---
title: "從測試中學習 Go 語言 (結構與介面篇)"
date: 2024-02-23
lastmod: 2024-02-25
draft: false
authors: ["nofear195"]
description: ""
categories: ["Go"]
tags: ["Go","struct","method","interface","table-driven-test"]
lightgallery: true

---



<!--more-->

## Exported and Unexported identifiers

- Go 語言無 public 與 private 關鍵字，而是使用變數開頭是否為英文大小寫作為區分
- 主要用途：是否要讓外部檔案 (package) 可以存取內部變數, 欄位, 函式等

| Feature                 | Exported Identifiers                  | Unexported Identifiers                 |
|-------------------------|---------------------------------------|----------------------------------------|
| Naming Convention       | Start with an uppercase letter        | Start with a lowercase letter          |
| Accessible from        | Outside the package where defined     | Only within the package where defined  |
| Accessibility          | Public                                | Private                                |
| Use Cases              | Types, variables, constants, functions intended for use by other packages | Encapsulation of implementation details within a package |

## Struct

- 一個自定義類型的方式，將多個不同類型的資料組合在一起，形成一個新的複合資料類型
- struct 內部的欄位可以為基本類型(int, float64 ...)抑或是其他 struct (embedding)
- `type` 關鍵字可用於為基本類型取別名，但非建立新的資料類型

```go
type ID  int// alias for int
type Info struct {
    ID ID // exported field
    color string  // unexported field
}
type Circle struct {
    Info Info // embed Info
    Radius float64
}

circle := Cricle{
    Info :Info{"001","red"},
    Radius : 2
}
```

## Method

- 附加到 struct 的函式，為自定義的 struct 建立可操作內部欄位的方法

```go
func (c Circle) Area() float64 {
    return 3.14 * c.Radius * c.Radius
}

fmt.Println(circle.Area()) // 12.56

```

## Interface

- 定義一組的函式(function, methods) 供多個類型實現
- Go 的 interface 解析為隱式 (implicit)，若傳入的類型有完全符合 interface 定義的函式，即可通過編譯
- 用來定義可接收不同類型作為參數的函式 (parametric polymorphism)
- 可以協助測試型態功能時，在進行 assert 可以直接呼叫功能，而不需要知道其確切的型態

```go
type Shape interface {
    Area() float64
}

type Rectangle struct{
    Info Info // embed Info
    Height float64
    Width float64
}

func (r Rectangle) Area() float64 {
    return r.Height * r.Width
}

rectangle := Rectangle{
    Info : Info{ID:2, color:"blue"},
    Height : 2,
    Width : 3,
}

fmt.Println(rectangle.Area()) // 6

shape := Shape(circle)
fmt.Println(shape.Area()) // 6

```

## Table driven test

- 用來測試同一個函式在不同 test cases 的結果

```go
import (
"math"
 "testing"
)

func TestArea(t *testing.T) {
 testCases := []struct { // anonymous struct
    shape Shape
    expected float
 }{
  {Rectangle{Info: Info{ID: 1, color: "blue"}, Height: 2, Width: 3},  6},
  {Circle{Info: Info{ID: 2, color: "red"}, Radius: 2}, math.Pi * 4},
 }

 for _, tc := range testCases {
  got := tc.shape.Area()
  if got != tc.expected {
   t.Errorf("Area calculation failed for shape: %#v. Expected: %f, Got: %f", tc.shape, tc.expected, got)
  }
 }
}

```

## Reference

[https://quii.gitbook.io/learn-go-with-tests/](https://quii.gitbook.io/learn-go-with-tests/)