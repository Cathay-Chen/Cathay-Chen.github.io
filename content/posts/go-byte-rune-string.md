---
title: "Go 语言中的单引号与双引号之字符与字符串"
date: 2022-08-03T15:52:22+08:00
lastmod: 2022-08-03T15:52:22+08:00
draft: false
tags: ["go"]
categories: ["golang"]
author: "cathay"
---

## 前言

**在 Go 中 单引号与双引号并不是等价的。** **单引号**用来表示 **字符**，**双引号**用来表示**字符串**。

Go语言的字符有以下两种：

- 一种是 uint8 类型，或者叫 byte 型，代表了 ASCII 码的一个字符。
- 另一种是 rune 类型，代表一个 UTF-8 字符，当需要处理中文、日文或者其他复合字符时，则需要用到 rune 类型。rune 类型等价于 int32 类型。

下面详细介绍一下字符和字符串类型。

## byte

占用 1 个节字，也就是 8 个比特位，所以它和 uint8 类型本质上没有区别，它表示的是 ACSII 表中的一个字符。

如下这段代码，分别定义了 byte 类型和 uint8 类型的变量 a 和 b。

```go
import "fmt"

func main() {
    var a byte = 65
    // 8进制写法: var c byte = '\101'     其中 \ 是固定前缀
    // 16进制写法: var c byte = '\x41'    其中 \x 是固定前缀

    var b uint8 = 66
    fmt.Printf("a 的值: %c \nb 的值: %c", a, b)
    // 或者使用 string 函数
    // fmt.Println("a 的值: ", string(a)," \nb 的值: ", string(b))
}

// 输出：
// a 的值: A
// b 的值: B
```

在 ASCII 表中，由于字母 A 的ASCII 的编号为 65 ，字母 B 的 ASCII 编号为 66，所以上面的代码也可以写成这样：

```go
import "fmt"

func main() {
    var a byte = 'A'
    var b uint8 = 'B'
    fmt.Printf("a 的值: %c \nb 的值: %c", a, b)
}

// 输出：
// a 的值: A
// b 的值: B
```

上面两段代码输出的结果都一样，所以 byte 和 uint8 类型本质上没有却别。

## rune

占用 4 个字节，共 32 位比特位，所以它和 int32 本质上也没有区别。它表示的是一个 Unicode 字符（ Unicode 是一个可以表示世界范围内的绝大部分字符的编码规范）由于 byte 类型能表示的值是有限，只有 2^8=256 个。当需要处理**中文**、**日文**或者其他**复合字符**时，则需要用到 rune 类型。

```go
import (
    "fmt"
    "unsafe"
)

func main() {
    var a byte = 'A'
    var b rune = 'B'
    fmt.Printf("a 占用 %d 个字节数\nb 占用 %d 个字节数", unsafe.Sizeof(a), unsafe.Sizeof(b))
}

// 输出：
// a 占用 1 个字节数
// b 占用 4 个字节数
```

不管是 byte 还是 rune ，都是使用**单引号**，而没使用**双引号**。

单引号用来表示**字符**，在上面的例子里，如果你使用双引号，就意味着你要定义一个字符串，赋值时与前面声明的前面会不一致，这样在编译的时候就报错：` cannot use "B" (type untyped string) as type rune in assignment`

---

> **byte 和 uint8 没有区别，rune 和 int32 没有区别，那为什么还要多出 byte 和 rune 类型呢？**
>
> 因为 uint8 和 int32 ，直观上让人以为这是一个数值，但是实际上，它也可以表示一个字符，所以为了消除这种直观错觉，就诞生了 byte 和 rune 这两个别名类型。

## string

### string 类型介绍

Go 语言中的字符串以原生数据类型出现，使用字符串就像使用其他原生数据类型（int、bool、float32、float64 等）一样。 Go 语言里的字符串的内部实现使用UTF-8 编码。 字符串的值为双引号(")中的内容，可以在Go语言的源码中直接添加非ASCII码字符，例如：

```go
s1 := "hello"
s2 := "你好"
```

golang中的字符串底层实际上是一个 **byte 数组**。因此可能会出现下面这种情况：

```go
str := "hello 世界"
fmt.Println(len(str)) // 输出 12
```

我们期望得到的结果应该是 8，原因是 golang 中的 string 底层是由一个 byte 数组实现的，而 golang 默认的编码是 utf-8，一个中文字符占 3 个字节，所以得到的长度是 12，想要得到我们想要的结果也很简单，golang 中的 `unicode/utf8` 包提供了用 utf-8 获取长度的方法：

```go
str := "hello 世界"
fmt.Println(utf8.RuneCountInString(str)) // 输出 8
```

上面说了 byte 类型实际上是一个 int8 类型，int8 适合表达 ascii 编码的字符，而 int32 可以表达更多的数，可以更容易的处理 unicode 字符。因此，我们可以通过rune类型来处理unicode字符：

```go
str := "hello 世界"
str2 := []rune(str)
fmt.Println(len(str2)) // 输出 8
```

注意：**这里 str2 拿到的是一个 rune 切片类型的引用**。

这里实际上是申请一块内存将 str 的内容复制到这块内存，这块内存是一个 rune 类型的切片，而 str2 拿到的则是一个 rune 类型的切片的**引用**，我们可以通过如下代码证明这里的 str2 一个引用：

```go
str := "hello 世界"
str2 := []rune(str) // 切片的引用
t := str2
t[0] = 'w'
fmt.Println(string(str2)) // 输出 wello 世界
```

通过把 str2 赋值给 t，t 上改变的数据，实际上是改变的是 t 指向的 rune 切片，因此，str 也会跟着改变。

### string 类型的遍历

```go
// 遍历字符串
func traversalString() {
    s := "hello 世界"
    for i := 0; i < len(s); i++ { //byte
        fmt.Printf("%v(%c) ", s[i], s[i])
    }
    fmt.Println()
    for _, r := range s { //rune
        fmt.Printf("%v(%c) ", r, r)
    }
    fmt.Println()
}

// 输出：
// 104(h) 101(e) 108(l) 108(l) 111(o) 32( ) 228(ä) 184(¸) 150() 231(ç) 149() 140() 
// 104(h) 101(e) 108(l) 108(l) 111(o) 32( ) 19990(世) 30028(界) 
```

### 修改字符串

要修改字符串，需要先将其转换成 []rune 或 []byte，完成后再转换为 string。无论哪种转换，都会重新分配内存，并复制字节数组。

```go
func changeString() {
    s1 := "big"
    // 强制类型转换
    byteS1 := []byte(s1) // 切片的引用
    byteS1[0] = 'p'
    fmt.Println(string(byteS1))

    s2 := "白萝卜"
    runeS2 := []rune(s2) // 切片的引用
    runeS2[0] = '红'
    fmt.Println(string(runeS2))
}

// 输出：
// pig
// 红萝卜
```

### 字符串转义符

Go 语言的字符串常见转义符包含回车、换行、单双引号、制表符等。如下所示：

| 字符 | 注释                                      |
| ---- | ----------------------------------------- |
| `\a` | 响铃                                      |
| `\b` | 退格                                      |
| `\f` | 换页                                      |
| `\n` | 换行                                      |
| `\r` | 回车                                      |
| `\t` | 制表符                                    |
| `\v` | 垂直制表符                                |
| `\'` | 单引号 (只用在 '\'' 形式的rune符号面值中) |
| `\"` | 双引号 (只用在 "..." 形式的字符串面值中)  |
| `\\` | 反斜杠                                    |

例如：

- 单行字符

```go
package main
import (
    "fmt"
)
func main() {
    fmt.Println("str := \"d:\\Code\\work\\go.exe\"")
}
```

- 多行字符串

Go语言中要定义一个多行字符串时，就必须使用反引号字符：

```go
s1 := `第一行
        第二行
        第三行
        `
fmt.Println(s1)
```

`反引号`间换行将被作为字符串中的换行，但是 `所有` 的转义字符均无效，文本将会原样输出。



## 参考链接

1. [「Go」byte、rune与string](https://studygolang.com/articles/28115)

2. [Go语言字符类型（byte和rune）](http://c.biancheng.net/view/18.html)
