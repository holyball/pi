# Go 快速入门（面向 Java / Kotlin 开发者）

Go 是静态类型、编译型语言，语法精简，没有类与继承，用 struct 加方法和 interface 组织代码。并发是语言内建能力。

## 1. 程序结构

```go
package main            // 每个文件属于一个 package，main 包是可执行入口

import "fmt"            // 导入标准库

func main() {           // main 函数是程序入口
    fmt.Println("hello")
}
```

- 一个目录就是一个 package，同目录下的文件共享作用域。
- 首字母大写的标识符对外导出（相当于 public），小写的仅包内可见。

## 2. 变量与常量

```go
var count int = 10      // 完整声明
var name = "Ann"        // 省略类型，自动推断
age := 20               // 短声明，只能在函数内使用，最常用

const pi = 3.14         // 常量
const (
    Red = iota          // iota 从 0 递增，Red=0 Green=1 Blue=2
    Green
    Blue
)
```

- `:=` 声明并推断类型，等价于先 `var` 再赋值。
- 未使用的局部变量与未使用的 import 会导致编译报错，这是强制约束。

## 3. 基本类型

```go
var i int = 42          // 整数，另有 int8/int16/int32/int64
var f float64 = 3.14    // 浮点
var b bool = true       // 布尔
var s string = "hi"     // 字符串，UTF-8，不可变
var r rune = '中'       // rune 即 int32，表示一个 Unicode 码点
```

数值类型之间不做隐式转换，必须显式：`float64(i)`。

## 4. 函数

```go
func add(a int, b int) int {          // 参数类型在名字之后，返回值类型在末尾
    return a + b
}

func swap(a, b int) (int, int) {      // 多返回值，Go 的常见特征
    return b, a
}

// 命名返回值
func divide(a, b int) (result int, err error) {
    if b == 0 {
        return 0, fmt.Errorf("除数为零")
    }
    result = a / b
    return
}

// 可变参数
func sum(nums ...int) int {
    total := 0
    for _, n := range nums {
        total += n
    }
    return total
}
```

函数可赋值给变量、作为参数与返回值，支持闭包。

## 5. 控制流

```go
if x > 0 {
    fmt.Println("正数")
} else {
    fmt.Println("非正数")
}

// if 可带初始化语句
if v, err := divide(10, 2); err == nil {
    fmt.Println(v)
}

// 只有 for 一种循环关键字
for i := 0; i < 3; i++ { }            // 经典写法
for i < 10 { i++ }                    // 相当于 while
for { break }                         // 无限循环
for idx, val := range []int{1, 2, 3} {// range 遍历
    fmt.Println(idx, val)
}

// switch 不需要 break，默认不穿透
switch count {
case 1:
    fmt.Println("一")
case 2, 3:
    fmt.Println("二或三")
default:
    fmt.Println("其他")
}
```

## 6. 复合类型

```go
// 数组：定长
var arr [3]int = [3]int{1, 2, 3}

// slice：变长，日常使用最多
nums := []int{1, 2, 3}
nums = append(nums, 4)             // 追加
sub := nums[1:3]                   // 取子片段

// map：键值容器，类似 Kotlin 的 Map
m := map[string]int{"a": 1}
m["b"] = 2
v, ok := m["a"]                    // ok 表示键是否存在
delete(m, "a")

// struct：结构体，用来聚合字段
type User struct {
    ID   int
    Name string
}
u := User{ID: 1, Name: "Ann"}
fmt.Println(u.Name)
```

## 7. 方法与接口

```go
type User struct {
    Name string
}

// 方法：在函数名前加接收者，绑定到 User
func (u User) Greet() string {
    return "hi " + u.Name
}

// 指针接收者，可修改原对象
func (u *User) Rename(name string) {
    u.Name = name
}

// 接口：定义方法集合
type Greeter interface {
    Greet() string
}

// User 自动满足 Greeter，无需 implements 声明（隐式实现）
var g Greeter = User{Name: "Ann"}
```

接口是隐式实现的：只要类型拥有接口要求的全部方法，就自动满足该接口，无需像 Java 那样显式 `implements`。

## 8. 指针

```go
x := 10
p := &x            // 取地址
fmt.Println(*p)    // 解引用，得到 10
*p = 20            // 通过指针修改，x 变为 20
```

Go 有指针但没有指针运算，用来在函数间共享与修改数据。

## 9. 错误处理

```go
func readFile(name string) ([]byte, error) {
    if name == "" {
        return nil, fmt.Errorf("文件名为空")
    }
    // ...
    return []byte("data"), nil
}

// 调用方检查 error，这是 Go 的核心约定
data, err := readFile("a.txt")
if err != nil {
    fmt.Println("出错:", err)
    return
}
fmt.Println(string(data))
```

Go 用返回值传递错误，调用方逐层检查 `err != nil`。`panic` / `recover` 只用于不可恢复的严重故障，日常错误不用它。

## 10. 并发

```go
import (
    "fmt"
    "sync"
)

func main() {
    var wg sync.WaitGroup

    // goroutine：轻量并发单元，用 go 关键字启动，类似 Kotlin 协程
    for i := 0; i < 3; i++ {
        wg.Add(1)
        go func(n int) {
            defer wg.Done()
            fmt.Println("任务", n)
        }(i)
    }
    wg.Wait()          // 等待全部完成

    // channel：goroutine 之间传递数据的管道
    ch := make(chan int)
    go func() { ch <- 42 }()   // 发送
    v := <-ch                  // 接收
    fmt.Println(v)
}
```

- `go f()` 启动一个 goroutine 并发执行。
- channel 用于安全传递数据，`ch <- v` 发送，`<-ch` 接收。
- `defer` 注册延迟调用，函数返回前执行，常用于释放资源。

## 编译与运行

```bash
go run main.go          # 直接编译并运行
go build                # 编译出可执行文件
go mod init example     # 初始化模块，生成 go.mod
gofmt -w main.go        # 格式化代码，Go 有统一强制的格式
```

