# 從測試中學習 Go 語言 (迴圈迭代篇)



&lt;!--more--&gt;

## Iteration

&gt; go 語法沒有 do , while 等關鍵字，所有的迴圈迭代只能用 **for** 去實現

### For loop

```go
for initialization; condition; increment {
    // Code to execute repeatedly
}

for i := 0; i &lt; 5; i&#43;&#43; {
  fmt.Println(i)
}
```

### While loop

```go
for condition {
    // Code to execute repeatedly
}

i := 0
for i &lt; 5 {
    fmt.Println(i)
    i&#43;&#43;
}

```

## Example

- repeat_test.go
  - 撰寫 Test function 與 Benchmark function

  ```go {title=&#34;repeat_test.go&#34;}
  package repeat

  import &#34;testing&#34;

  func TestRepeat(t *testing.T) {
    repeated := Repeat(&#34;a&#34;)
    expected := &#34;aaaaa&#34;

    if repeated != expected {
      t.Errorf(&#34;expected %q but got %q&#34;, expected, repeated)
    }
  }

  func BenchmarkRepeat(b *testing.B) {
    for i := 0; i &lt; b.N; i&#43;&#43; {
      Repeat(&#34;a&#34;)
    }
  }
  ```

- repeat.go

  ```go {title=&#34;repeat.go&#34;}
  package repeat

  const repeatCount = 5

  func Repeat(character string) string {
    var repeated string
    for i := 0; i &lt; repeatCount; i&#43;&#43; {
      repeated &#43;= character
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


---

> Author: nofear195  
> URL: https://nofear195.github.io/my-hugo-blog/posts/learn-go-test-3/  

