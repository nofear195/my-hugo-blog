---
title: "從測試中學習 Go 語言 (變數、條件判斷篇)"
date: 2024-02-17
lastmod: 2024-02-18
draft: true
authors: ["nofear195"]
description: ""
categories: ["Go"]
tags: ["Go","Types","if-else","switch-case"]
lightgallery: true

---

<!--more-->

## Types

- Base Types
  - int
  - float64 : 十進制的雙精度浮點數
  - string
  - bool : true or false

- Composite Types (複合類型)
  - slice
  - map
  - struct

- Pointer Types
  - pointer : *T

- Interface Types
  - interface{}

- Function Types
  - func

- Channel Types
  - chan T

## Variable Declaration

### var

- 宣告有明確類型的變數
- 變數值可改變
- 可以使用簡短宣告的方式，一次宣告變數並為其賦值：```:=```

```go

var age string
age = 5
// 等同於使用 := 
age := 5

```

### const

- 宣告不可改變值的變數
- 可用 **()** ㄧ次性包裹所有需定義為 const 的變數

```go
const pi = 3.14

const (
  french  = "French"
  spanish = "Spanish"
)
```

### type

- 用於宣告自定義的類型或別名

```go
type Bitcoin int

type Wallet struct {
 balance Bitcoin
}

type Stringer interface {
 String() string
}

```

## Conditionals

### if-else

```go
num := 10
if num > 0 {
    fmt.Println("Number is positive")
} else if num == 0 {
    fmt.Println("Number is zero")
} else {
    fmt.Println("Number is negative")
}

// Example of if statement within initialization
if num := 15; num > 10 {
    fmt.Println("Number is greater than 10")
}
```

### switch-case

```go
fruit := "apple"
switch fruit {
case "apple":
    fmt.Println("It's an apple")
case "banana":
    fmt.Println("It's a banana")
default:
    fmt.Println("It's something else")
}

```

## Reference

[https://quii.gitbook.io/learn-go-with-tests/](https://quii.gitbook.io/learn-go-with-tests/)
