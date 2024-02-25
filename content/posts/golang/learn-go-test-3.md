---
title: "從測試中學習 Go 語言 (迴圈迭代篇)"
date: 2024-02-18
lastmod: 2024-02-19
draft: false
authors: ["nofear195"]
description: ""
categories: ["Go"]
tags: ["Go","for-loop"]
lightgallery: true

---


<!--more-->

## Iteration

> go 語法沒有 do , while 等關鍵字，所有的迴圈迭代只能用 **for** 去實現

### For loop

```go
for initialization; condition; increment {
    // Code to execute repeatedly
}

for i := 0; i < 5; i++ {
  fmt.Println(i)
}
```

### While loop

```go
for condition {
    // Code to execute repeatedly
}

i := 0
for i < 5 {
    fmt.Println(i)
    i++
}

```

## Example

- repeat_test.go
  - 撰寫 Test function 與 Benchmark function

  ```go {title="repeat_test.go"}
  package repeat

  import "testing"

  func TestRepeat(t *testing.T) {
    repeated := Repeat("a")
    expected := "aaaaa"

    if repeated != expected {
      t.Errorf("expected %q but got %q", expected, repeated)
    }
  }

  func BenchmarkRepeat(b *testing.B) {
    for i := 0; i < b.N; i++ {
      Repeat("a")
    }
  }
  ```

- repeat.go

  ```go {title="repeat.go"}
  package repeat

  const repeatCount = 5

  func Repeat(character string) string {
    var repeated string
    for i := 0; i < repeatCount; i++ {
      repeated += character
    }
    return repeated
  }

  ```

- test result

  ```bash
  vscode ➜ /workspaces/learn-go/repeat $ go test -v
  === RUN   TestRepeat
  --- PASS: TestRepeat (0.00s)
  PASS
  ok      test-with-go/repeat  0.002s


  vscode ➜ /workspaces/learn-go/repeat $ go test -bench .
  goos: linux
  goarch: arm64
  pkg: test-with-go/repeat
  BenchmarkRepeat-8       13891360                86.05 ns/op
  PASS
  ok      test-with-go/repeat  1.288s
  ```

## Reference

[https://quii.gitbook.io/learn-go-with-tests/](https://quii.gitbook.io/learn-go-with-tests/)
