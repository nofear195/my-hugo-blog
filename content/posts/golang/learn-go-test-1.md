---
title: "從測試中學習 Go 語言 (測試流程篇)"
date: 2024-02-18
lastmod: 2024-02-18
draft: false
authors: ["nofear195"]
description: ""
categories: ["Go"]
tags: ["Go","Test","TDD"]
lightgallery: true

---

藉由 Go 內建完整的測試系統，同時學習 Go 程式語言與測試開發流程

<!--more-->

## Test-Driven Development (TDD)

Test-Driven Development (TDD) 測試驅動開發，一種軟體開發的方法。 </br>

- 為一個編寫失敗的測試、撰寫可以剛好通過的程式碼、最後再重構的循環過程
- 確保程式在整個開發過程中經過良好測試、模組化且易於維護

### Red-Green-Refactor Workflow

![https://developer.ibm.com/articles/5-steps-of-test-driven-development/](https://developer.ibm.com/developer/default/articles/5-steps-of-test-driven-development/images/tdd-red-green-refactoring-v3.png)
> image resource : [https://developer.ibm.com/articles/5-steps-of-test-driven-development/](https://developer.ibm.com/articles/5-steps-of-test-driven-development/)

#### Red

- Start Small : 從基本而小功能開始，之後再逐漸增加複雜性
- Write a failing test : 為目標撰寫的測試一定會失敗，因爲該目標功能根本沒撰寫
- Focus on the "what," not the "how" ：清楚描述目標程式碼該做什麼，而非如何實現

#### Green

- Write the minimum code : 撰寫剛好可以通過測試的程式。
- Keep it simple and clean ：專注核心功能，保持程式間單乾淨、模組化

#### Refactor

- Clean up and optimize ：測試通過後，重構程式碼以提高其可讀性、可維護性和效能
- Repeat ：尊循相同的 Red-Green-Refactor，為功能的不同方面撰寫更多測試

## GO Testing Rules

- 測試檔案命名： **XXX_test.go**
- 引入 **testing** 模組： ```import "testing"```
- 測試函數 : ```func TestXxx(t *testing.T){...}```
  - 名稱前綴須為： **Test**
  - 只能接收一個參數 **t *testing.T**
  - 測試指令 : ```go test```
- 子測試函數 subtest (optional): ```t.Run('subtest name',func(t *testing.T){...})```
  - 使用時機 ：同一函數面對各種輸入條件下的結果
  - 位置 ： 主要測試函數內
- 測試幫助函數 Helper Func :  ```func helper(t testing.TB,...){ t.Helper() ...}```
  - 使用時機 ：提取測試函式會用到的共用邏輯，獨立成一個函式
  - 第一個參數接收測試函式的測試變數，並使用 ```testing.TB```型態
  - 函式第一行需加入 ```t.Helper()``` : 作為標記，在輸出測試錯誤時跳過
- 測試錯誤輸出方式
  - t.Errorf :```t.Errorf```
    - ex : ```t.Errorf("got %q want %q", got, want)```
    - 使用時機：大多數情形，用在非致命性錯誤發生時，可繼續跑測試
  - t.Fatal :```t.Fatal```
    - ex: ```t.Fatal("didn't get an error but wanted one")```
    - 使用時機: 少數會發生致命性錯誤時，立即終止測試
- Benchmark 基準測試：```func BenchmarkXxx(b *testing.B){...}```
  - 名稱前綴須為： **Benchmark**
  - 只能接收一個參數 **b *testing.B**
  - 測試指令： ```go test -bench .```
  - 使用時機： 循環測試某個函式，查看其效能時

    ```go
    func BenchmarkRandInt(b *testing.B) {
      for i := 0; i < b.N; i++ {
          rand.Int()
      }
     }   
    ```

## Example

Task : 撰寫一個 Hello function，輸入 人名，輸出 Hello, 人名

1. 撰寫 test function : TestHello

    - file

        ```go {title="hello_test.go"}
          package hello

          import "testing"

          func TestHello(t *testing.T) {

            got := Hello("Frank")
            want := "Hello, Frank"

            if got != want {
              t.Errorf("got %q want %q", got, want)
            }
          }
        ```

    - result

      ```bash
        vscode ➜ /workspaces/learn-go/hello $ go test
        # test-with-go/hello [test-with-go/hello.test]
        ./hello_test.go:7:9: undefined: Hello
        FAIL    test-with-go/hello [build failed]
      ```

2. 撰寫 function : Hello

    - file

        ```go {title="hello.go"}
          package hello

          func Hello(name string) string {
            return "Hello, " + name
          }
        ```

    - result

      ```bash
        vscode ➜ /workspaces/learn-go/hello $ go test
        PASS
        ok      test-with-go/hello      0.001s
      ```

3. 新增測試子項目，假設輸入為空字串值時

    - file

        ```go {title="hello_test.go"}
          func TestHello(t *testing.T) {

            t.Run("saying hello to people", func(t *testing.T) {
              got := Hello("Frank")
              want := "Hello, Frank"

              if got != want {
                t.Errorf("got %q want %q", got, want)
              }
            })

            t.Run("say 'Hello, World' when an empty string is supplied", func(t *testing.T) {
              got := Hello("")
              want := "Hello, World"

              if got != want {
                t.Errorf("got %q want %q", got, want)
              }
            })

          }
        ```

    - result

      ```bash
        vscode ➜ /workspaces/learn-go/hello $ go test
        --- FAIL: TestHello (0.00s)
            --- FAIL: TestHello/say_'Hello,_World'_when_an_empty_string_is_supplied (0.00s)
                hello_test.go:21: got "Hello, " want "Hello, World"
        FAIL
        exit status 1
        FAIL    test-with-go/hello      0.001s
      ```

4. 更新 Hello function

    - file

        ```go {title="hello.go"}
          func Hello(name string) string {
            if name == "" {
              name = "World"
            }
            return "Hello, " + name
          }
        ```

    - result

      ```bash
        vscode ➜ /workspaces/learn-go/hello $ go test
        PASS
        ok      test-with-go/hello      0.001s
      ```

5. 提取相同邏輯的程式碼片段，獨立成一個可重複使用的函式

    - file

        ```go {title="hello_test.go"}
          // package 私有函數開頭為小寫字母
          func assertCorrectMessage(t testing.TB, got, want string) {
            t.Helper()
            if got != want {
              t.Errorf("got %q want %q", got, want)
            }
          }
          func TestHello(t *testing.T) {

            t.Run("saying hello to people", func(t *testing.T) {
              got := Hello("Frank")
              want := "Hello, Frank"
              assertCorrectMessage(t, got, want)
            })

            t.Run("say 'Hello, World' when an empty string is supplied", func(t *testing.T) {
              got := Hello("")
              want := "Hello, World"
              assertCorrectMessage(t, got, want)
            })

          }
        ```

    - result

      ```bash
        vscode ➜ /workspaces/learn-go/hello $ go test
        PASS
        ok      test-with-go/hello      0.001s
      ```

## Go Tools

### Dependency management file

- go.mod
  - Go 專案中管理依賴套件的文件
  - 內容大致包括：模組名稱、使用的 Go 版本、依賴套件等

  ```bash
  // initialize in terminal with module name 
   go mod init example.com/v2
  // module name 主要是敘述之後要發布時，可以去下載的位置
  // 若沒有要發布可以自己取變數
   go mod init test-with-go
  ```

  ``` {title="go.mod"}
  module test-with-go

  go 1.21.6

  ```

### Formatter

- go fmt
  - Go built in tools
  - format file : ```go fmt```

- vscode settings.json

  ```json
  {
    "editor.defaultFormatter": "golang.go",
  }
  ```

### Documentation

- go doc
  - Go built in documentation in terminal
  - ex : ```do doc fmt```

- godoc
  - Go Documentation Server (with web page)
  - ```go install golang.org/x/tools/cmd/godoc@latest```
  - start server : ```godoc```

## Reference

[https://quii.gitbook.io/learn-go-with-tests/](https://quii.gitbook.io/learn-go-with-tests/)
