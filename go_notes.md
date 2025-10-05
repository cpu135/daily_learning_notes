## Go的特性
- Go 语言是一门类型安全和内存安全的编程语言。虽然 Go 语言中仍有指针的存在，但并不允许进行指针运算。
- Go擅长 网络通信、并发和并行编程
- Go 作为静态语言的 编译和链接也非常快
- Go 的可执行文件都比相对应的源代码文件要大很多，这恰恰说明了 Go 的 runtime ( runtime 类似 Java 和 .NET 语言所用到的虚拟机，它负责管理包括内存分配、垃圾回收)嵌入到了每一个可执行文件当中
- Go也可以调用C语言的库，只不过对于C的函数调用的变量要及时释放内存

    
每个 Go 文件都属于且仅属于一个包，意思就是每个go文件开头的第一行都应该是package ###，###也就是该文件属于的包
而对于 Go 应用程序来说，应该都包含一个名为 main 的包。 如果你打算编译包名不是为 main 的源文件，如 pack1，编译后产生的对象文件将会是 pack1.a 而不是可执行程序。也就是说，main包就相当于go程序的入口包，只有他才能作为可执行文件；其他非main包都叫库包，是静态库，不是可执行的，只能用来导入或链接。

属于同一个包的源文件必须全部被一起编译，一个包即是编译时的一个单元

如果对一个包进行更改或重新编译，所有引用了这个包的客户端程序都必须全部重新编译。

如果你导入了一个包却没有使用它，则会在构建程序时引发错误，如 imported and not used: os，这正是遵循了 Go 的格言：“没有不必要的代码！”。
main() 函数是每一个可执行程序所必须包含的，一般来说都是在启动后第一个执行的函数（如果有 init() 函数则会先执行该函数）。如果你的 main 包的源代码没有包含 main() 函数，则会引发构建错误 undefined: main.main。
只有当某个函数需要被外部包调用的时候才使用大写字母开头 ：```fmt.Println（"hello, world"）```

```
func FunctionName (a typea, b typeb) (t1 type1, t2 type2)：
    (a typea, b typeb)：参数列表
    (t1 type1, t2 type2)：返回类型列表
```


常量使用关键字 const 定义 ，常量的定义格式：const identifier [type] = value，例如：const Pi = 3.14159 ，当然其中的type你可以省略，编译器自己会判断

第一个 iota 等于 0，每当 iota 在新的一行被使用时，它的值都会自动加 1，并且没有赋值的常量默认会应用上一行的赋值表达式：
```
// 赋值一个常量时，之后没赋值的常量都会应用上一行的赋值表达式
const (
a = iota  // a = 0
b         // b = 1
c         // c = 2
d = 5     // d = 5   
e         // e = 5
)

// 赋值两个常量，iota 只会增长一次，而不会因为使用了两次就增长两次
const (
Apple, Banana = iota + 1, iota + 2 // Apple=1 Banana=2
Cherimoya, Durian                  // Cherimoya=2 Durian=3
Elderberry, Fig                    // Elderberry=3, Fig=4

)

// 使用 iota 结合 位运算 表示资源状态的使用案例
const (
Open = 1 << iota  // 0001
Close             // 0010
Pending           // 0100
)

const (
_           = iota             // 使用 _ 忽略不需要的 iota
KB = 1 << (10 * iota)          // 1 << (10*1)
MB                             // 1 << (10*2)
GB                             // 1 << (10*3)
TB                             // 1 << (10*4)
PB                             // 1 << (10*5)
EB                             // 1 << (10*6)
ZB                             // 1 << (10*7)
YB                             // 1 << (10*8)
)

```

### go和c 指针的区别

-   var a, b *int 表示a和b都是指针，但是在c语言里面 int* a, b; 表示a是指针，b不是

当一个变量被声明之后，系统自动赋予它该类型的零值：int 为 0，float32(64) 为 0.0，bool 为 false，string 为空字符串，指针为 nil。记住，所有的内存在 Go 中都是经过初始化的

变量的命名规则遵循骆驼命名法，即首个单词小写，每个新单词的首字母大写，例如：numShips 和 startDate。 但如果你的全局变量希望能够被外部包所使用，则需要将首个单词的首字母也大写

对于局部变量来说，:=是声明，=是赋值，声明就是第一次出现时用的，第一次出现必须用声明；全局变量可以直接=
其实跟c语法一样，如果局部函数中出现了a:=3，全局变量也有个a，那么这2个a就是不同的，但如果局部变量里是a=3，那么就是同一个变量只是赋值了而已

不同类型的值不可以做运算或者使用，比如b=a ；a是int ，b是int32  ，虽然int在64位操作系统上也是64位，但是int和int64也不一样

在 type TZ int 中，TZ 就是 int 类型的新名称

## 字符串

### 判断字符串 s 是否以 prefix 开头：
```
strings.HasPrefix(s, prefix string) bool
```

### 判断字符串 s 是否以 suffix 结尾：
```
strings.HasSuffix(s, suffix string) bool
```
这两个函数返回true或false

### 判断字符串 `s` 是否包含 `substr`：
```
strings.Contains(s, substr string) bool
```

### 判断子字符串或字符在父字符串中出现的位置（索引）

`Index()` 返回字符串 `str` 在字符串 `s` 中的索引（`str` 的第一个字符的索引），`-1` 表示字符串 `s` 不包含字符串 `str`：

```
strings.Index(s, str string) int
```

### 字符串替换

`Replace()` 用于将字符串 `str` 中的前 `n` 个字符串 `old` 替换为字符串 `new`，并返回一个新的字符串，如果 `n = -1` 则替换所有字符串 `old` 为字符串 `new`：

```
strings.Replace(str, old, new string, n int) string
```

### 统计字符串出现次数

Count() 用于计算字符串 str 在字符串 s 中出现的非重叠次数（比如gggg，就是2个gg不重叠）：
```
strings.Count(s, str string) int
```

### 重复字符串

Repeat() 用于重复 n 次字符串 s 并返回一个新的字符串：

```
strings.Repeat(s, count int) string
```
就重复说一个字符串n次

### 修改字符串大小写

`ToLower()` 将字符串中的 Unicode 字符全部转换为相应的小写字符：

```
strings.ToLower(s) string
```

`ToUpper()` 将字符串中的 Unicode 字符全部转换为相应的大写字符：

```
strings.ToUpper(s) string
```

### 修剪字符串

`strings.TrimSpace(s)` 来剔除字符串开头和结尾的空白符号；如果你想要剔除指定字符，则可以使用 `strings.Trim(s, "cut")` 来将开头和结尾的 `cut` 去除掉。该函数的第二个参数可以包含任何字符，如果你只想剔除开头或者结尾的字符串，则可以使用 `TrimLeft()` 或者 `TrimRight()` 来实现。

### 分割字符串

`strings.Fields(s)` 将会利用 1 个或多个空白符号来作为动态长度的分隔符将字符串分割成若干小块，并返回一个 slice，如果字符串只包含空白符号，则返回一个长度为 0 的 slice。

`strings.Split(s, sep)` 用于自定义分割符号来对指定字符串进行分割，同样返回 slice。

因为这 2 个函数都会返回 slice，所以习惯使用 for-range 循环来对其进行处理（第 7.3 节）。

### 拼接 slice 到字符串

`Join()` 用于将元素类型为 string 的 slice 使用分割符号来拼接组成一个字符串：

```
strings.Join(sl []string, sep string) string
```
```
package main

import (
	"fmt"
	"strings"
)

func main() {
	str := "The quick brown fox jumps over the lazy dog"
	sl := strings.Fields(str)
	fmt.Printf("Splitted in slice: %v\n", sl)
	for _, val := range sl {
		fmt.Printf("%s - ", val)
	}
	fmt.Println()
	str2 := "GO1|The ABC of Go|25"
	sl2 := strings.Split(str2, "|")
	fmt.Printf("Splitted in slice: %v\n", sl2)
	for _, val := range sl2 {
		fmt.Printf("%s - ", val)
	}
	fmt.Println()
	str3 := strings.Join(sl2,";")
	fmt.Printf("sl2 joined by ;: %s\n", str3)
}
```

输出：

	Splitted in slice: [The quick brown fox jumps over the lazy dog]
	The - quick - brown - fox - jumps - over - the - lazy - dog -
	Splitted in slice: [GO1 The ABC of Go 25]
	GO1 - The ABC of Go - 25 -
	sl2 joined by ;: GO1;The ABC of Go;25

### 从字符串中读取内容

strings.NewReader(str) 用于生成一个 Reader 并读取字符串中的内容，然后返回指向该 Reader 的指针


