# 從測試中學習 Go 語言 (變數、條件判斷篇)


藉由 Go 內建完整的測試系統，同時學習 Go 程式語言與測試開發流程

&lt;!--more--&gt;

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
  french  = &#34;French&#34;
  spanish = &#34;Spanish&#34;
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
if num &gt; 0 {
    fmt.Println(&#34;Number is positive&#34;)
} else if num == 0 {
    fmt.Println(&#34;Number is zero&#34;)
} else {
    fmt.Println(&#34;Number is negative&#34;)
}

// Example of if statement within initialization
if num := 15; num &gt; 10 {
    fmt.Println(&#34;Number is greater than 10&#34;)
}
```

### switch-case

```go
fruit := &#34;apple&#34;
switch fruit {
case &#34;apple&#34;:
    fmt.Println(&#34;It&#39;s an apple&#34;)
case &#34;banana&#34;:
    fmt.Println(&#34;It&#39;s a banana&#34;)
default:
    fmt.Println(&#34;It&#39;s something else&#34;)
}

```

## Reference

[https://quii.gitbook.io/learn-go-with-tests/](https://quii.gitbook.io/learn-go-with-tests/)


---

> Author: nofear195  
> URL: https://nofear195.github.io/my-hugo-blog/posts/learn-go-test-2/  

