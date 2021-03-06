
函数
===

## 1、参数

（1）当两个或多个连续的函数命名参数（包括返回值参数列表）是同一类型时，除了最后一个类型外，其他都可以省略。如：
```go
func add(x, y int) int { return x + y }
```

（2）Go中的参数传递都是`值传递`，即函数如果要修改实参的值，实参必须是个指针变量。


## 2、返回值

（1）函数可以返回**`任意数量`**的返回值。

（2）命名返回值：

函数返回值接受参数。在Go中，函数可以返回多个“`结果参数`”，而不仅仅是一个值。而且，它们可以像变量那样命名和使用。

如果命名了返回值参数，一个没有参数的 `return` 语句会将当前的值作为返回值返回。如：
```go
func split(sum int) (x, y int) {
    x = sum * 4 / 9
    y = sum - x
    return
}
```


## 3、表达式语句

可以出现在`语句上下文`中的函数（包括内建函数）或方法调用以及接收操作叫做`表达式语句`，即该函数或方法调用等操作可以独立成为一个语句；它们可以被`parenthesized`。

但是有部分内建函数的调用不能用于语句上下文中，即它们不能独立成为一个语句，它们必须放到其它语句（如赋值语句）中。这些内建函数有：

    append   cap   complex     imag    len      make   new
    real     unsafe.Alignof    unsafe.Offsetof  unsafe.Sizeof


## 4、函数支持度

### 不支持
`嵌套(nested)`、`重载（overload）` 和 `默认参数(default parameters)`。

### 支持
`无需声明原型`、`不定长度变参`、`多返回值`、`命名返回值参数`、`匿名函数`、`闭包`。

## 5、不定长度变参
Go函数支持`不定长度变参`(也叫`可变参数`)，其方式是：在定义函数时，将`最后一个形参`(假设为 p )的类型设置为 `...T` (其真实类型为 `[]T`)即可。

当调用可变长度参数函数时，对于形参 p，如果没有实现的参数传递，则它的值为 `nil`；否则，它的值是一个新的、类型为`[]T`的`slice`，它的底层表示是一个包含所有实际参数的新数组，并且该slice的长度和容量是绑定到形参p的参数的个数（由此可知，长度和容量应该是一样的），因此，该长度和容量因每次函数调用而不同。我们可以形象地认为，多余的、变化的参数被收集到一个slice中了。

如果最后一个参数是可赋值给类型为`[]T`的分片，那么，只要在该参数后面添加`...`标识，该参数可以不变地作为值传递给`不定长度变参`，即形参p，此时，没有新的 slice 被创建。也即是说，如果最后一个参数也是`[]T`类型的`slice`，它可以直接传递给形参p，只要它的后面添加`...`标识。

```go
func Greet(prefix string, who ...string) {
    // Do something
}

Greet("nobody")                             // who => nil
Greet("Hello:", "Joe", "Anna", "Eileen")    // who => []string{"Joe", "Anna", "Eileen"}

s := []string{"James", "Aaron"}
Greet("Goodbye:", s...)                     // who => s
```

## 6、匿名函数与递归
当匿名函数需要被递归调用时，我们必须首先声明一个变量，再将匿名函数赋值给这个变量。
```go
var FuncN func(args ...interface{})
FuncN = func(args ...interface{}) {
    // ...
}

FuncN2 := func(args ...interface{}) {
    FuncN2(args...)                      // compile error: undefined: FuncN2
}
```


## 7、编译时被执行的函数
如果函数在编译时被执行，那么它的返回值是常量。

函数           | 返回值 | 编译时便计算?
---------------|-------|-------------
`unsafe.Sizeof`、`unsafe.Alignof`、`unsafe.Offsetof` |  uintptr   | Yes, 总是
`len`、`cap`   | int | 有时候是。[Go 规范中讲到](https://golang.org/ref/spec#Length_and_capacity)：如果 s 是字符串常量，则 `len(s)` 是常量；如果 s 是数组或者是数组指针，则 `len(s)` 是常量。
`real`、`imag` | float64 (默认类型) | 有时候是。[Go 规范中讲到](https://golang.org/ref/spec#Constants)：如果 s 是复数常量，则 `real(s)` 和 `imag(s)` 是常量。
`complex`      | complex128 (默认类型) | 有时候是。[Go 规范中讲到](https://golang.org/ref/spec#Constants)：如果 sr 和 si 都是常量，则 `complex(sr, si)` 是常量。


## 8、可以在函数体外声明的源代码元素

下列类型既可以声明在函数体内，也可以声明在函数体外：

    type
    variable
    constant

`pakcage` 的前面只能有注释，不能有其它任何语句。

`import` 必须在其它元素的声明的前面，`package` 语句的后面。

标签（Label）必须声明在函数体内。
