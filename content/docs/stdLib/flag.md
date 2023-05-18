+++
title = "flag"
linkTitle = "flag"
date = 2023-05-17T09:59:21+08:00
description = ""
isCJKLanguage = true
draft = false
+++
# flag

[https://pkg.go.dev/flag@go1.20.1](https://pkg.go.dev/flag@go1.20.1)

​	flag包实现了命令行标志解析。

## Usage 

​	使用 flag.String()、Bool()、Int() 等函数定义标志。

​	下面的代码声明了一个整数标志 -n，存储在指针 nFlag 中，类型为 `*int`：

```go 
import "flag"
var nFlag = flag.Int("n", 1234, "help message for flag n")
```

​	如果你喜欢，可以使用 Var() 函数将标志绑定到变量。

```go 
var flagvar int
func init() {
	flag.IntVar(&flagvar, "flagname", 1234, "help message for flagname")
}
```

或者，你可以创建满足 Value 接口(使用指针接收器)的自定义标志，并将它们与标志解析耦合：

```
flag.Var(&flagVal, "name", "help message for flagname")
```

对于这种标志，默认值就是变量的初始值。

在定义好所有标志之后，调用

```
flag.Parse()
```

将命令行参数解析为定义好的标志。

然后可以直接使用标志。**如果使用标志本身，它们都是指针**；**如果与变量绑定，它们就是值**。

```
fmt.Println("ip has value ", *ip)
fmt.Println("flagvar has value ", flagvar)
```

解析后，跟在标志后面的参数可以作为 `flag.Args()` 切片使用，也可以单独作为 flag.Arg(i) 使用。参数的索引从 0 到 `flag.NArg()-1`。

## Command line flag syntax 

以下形式是允许的：

```
-flag
--flag   // 也允许双短线
-flag=x
-flag x  // 只适用于非布尔标志
```

​	可以使用一个或两个短线，它们是等价的。最后一种形式不适用于布尔标志，因为命令

```
cmd -x *
```

其中 `*` 是 Unix shell 通配符，如果存在名为 `0`、`false` 等的文件，其含义将发生变化。你必须使用 -flag=false 形式来关闭布尔标志。

​	标志解析在第一个非标志参数("-"是非标志参数)之前或终止符"--"之后停止。

​	整数标志接受 1234、0664、0x1234 等值，也可以是负数。布尔标志可以是：

```
1, 0, t, f, T, F, true, false, TRUE, FALSE, True, False
```

​	时间间隔标志接受任何符合 time.ParseDuration 的输入。

​	默认的命令行标志集由顶级函数控制。FlagSet 类型允许定义独立的标志集，例如实现命令行接口中的子命令。FlagSet 的方法类似于命令行标志集的顶级函数。

### Example

``` go 
//这些例子展示了flag包的更复杂的用途。
package main

import (
	"errors"
	"flag"
	"fmt"
	"strings"
	"time"
)

// 示例1：一个名为 "species "的单一字符串标志，默认值为 "gopher"。
var species = flag.String("species", "gopher", "the species we are studying")

// 例二：两个flag共享一个变量，所以我们可以有一个速记的方法。
// 初始化的顺序是未定义的，所以要确保两者使用相同的默认值。
// 它们必须用一个init函数来设置。
var gopherType string

func init() {
	const (
		defaultGopher = "pocket"
		usage         = "the variety of gopher"
	)
	flag.StringVar(&gopherType, "gopher_type", defaultGopher, usage)
	flag.StringVar(&gopherType, "g", defaultGopher, usage+" (shorthand)")
}

// 例3：一个用户定义的标志类型，一个持续时间的切片。
type interval []time.Duration

// String是用来格式化标志值的方法，是flag.Value接口的一部分。
// String方法的输出将在诊断中使用。
func (i *interval) String() string {
	return fmt.Sprint(*i)
}

// Set是设置标志值的方法，是flag.Value接口的一部分。
// Set的参数是一个要解析的字符串，用来设置标志。
// 它是一个逗号分隔的列表，所以我们把它拆开。
func (i *interval) Set(value string) error {
    // 如果我们想让这个标志被设置多次，累积数值，我们可以删除这个if语句。
	// 这将允许诸如-deltaT 10s -deltaT 15s和其他组合的使用。
	if len(*i) > 0 {
		return errors.New("interval flag already set")
	}
	for _, dt := range strings.Split(value, ",") {
		duration, err := time.ParseDuration(dt)
		if err != nil {
			return err
		}
		*i = append(*i, duration)
	}
	return nil
}

// 定义一个标志来累积持续时间。因为它有一个特殊的类型。
// 我们需要使用Var函数，因此在init期间创建该标志。

var intervalFlag interval

func init() {
    // 将命令行标志与intervalFlag变量绑定，并设置一个使用信息。
	flag.Var(&intervalFlag, "deltaT", "comma-separated list of intervals to use between events")
}

func main() {
    // 所有有趣的部分都与上面声明的变量在一起，
    // 但为了使flag包能够看到那里定义的标志，必须执行，通常在main的开始(不是init！):
	// flag.Parse()
	// 我们不在这里调用它，因为这段代码是一个叫做 "Example"的函数，是包测试套件的一部分，
    // 它已经解析了标志。但在pkg.go.dev查看时，该函数被重命名为 "main"，
    // 它可以作为一个独立的例子运行。
}

```


## 常量 

This section is empty.

## 变量

[View Source](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=1150)

``` go 
var CommandLine = NewFlagSet(os.Args[0], ExitOnError)
```

​	CommandLine 是默认的命令行标志集，从 os.Args 解析而来。诸如 BoolVar、Arg 等顶级函数是 CommandLine 方法的包装器。

[View Source](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=100)

``` go 
var ErrHelp = errors.New("flag: help requested")
```

​	ErrHelp 是在调用 -help 或 -h 标志，但没有定义这样的标志时返回的错误。

[View Source](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=674)

``` go 
var Usage = func() {
	fmt.Fprintf(CommandLine.Output(), "Usage of %s:\n", os.Args[0])
	PrintDefaults()
}
```

​	Usage 打印一个使用说明，文档化所有已定义的命令行标志到 CommandLine 的输出，这个输出默认是 os.Stderr。当解析标志时发生错误时会调用它。该函数是一个变量，可以更改指向自定义函数。默认情况下，它会打印一个简单的标题并调用 [PrintDefaults](#func-printdefaults)；关于输出格式及如何控制它的详细信息，请参阅 PrintDefaults 的文档。自定义的使用函数可能选择退出程序；默认情况下，退出仍会发生，因为命令行的错误处理策略设置为 `ExitOnError`。

## 函数

#### func [Arg](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=698) 

``` go 
func Arg(i int) string
```

​	Arg函数返回第 i 个命令行参数。Arg(0) 是在处理标志后剩下的第一个参数。如果请求的元素不存在，则 Arg 返回空字符串。

#### func [Args](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=712) 

``` go 
func Args() []string
```

​	Args函数返回非标志命令行参数。

#### func [Bool](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=736) 

``` go 
func Bool(name string, value bool, usage string) *bool
```

​	Bool函数定义一个具有指定名称、默认值和用法字符串的布尔标志。返回值是存储标志值的布尔变量的地址。

#### func [BoolVar](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=722) 

``` go 
func BoolVar(p *bool, name string, value bool, usage string)
```

​	BoolVar函数定义一个具有指定名称、默认值和用法字符串的布尔标志。参数 p 指向一个布尔变量，用于存储标志的值。

#### func [Duration](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=922) 

``` go 
func Duration(name string, value time.Duration, usage string) *time.Duration
```

​	Duration函数定义一个具有指定名称、默认值和用法字符串的 time.Duration 标志。返回值是存储标志值的 time.Duration 变量的地址。标志接受符合 time.ParseDuration 的值。

#### func [DurationVar](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=906) 

``` go 
func DurationVar(p *time.Duration, name string, value time.Duration, usage string)
```

​	DurationVar函数定义一个具有指定名称、默认值和用法字符串的 time.Duration 标志。参数 p 指向一个 time.Duration 变量，用于存储标志的值。标志接受符合 time.ParseDuration 的值。

#### func [Float64](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=892) 

``` go 
func Float64(name string, value float64, usage string) *float64
```

​	Float64函数定义一个具有指定名称、默认值和用法字符串的 float64 标志。返回值是存储标志值的 float64 变量的地址。

#### func [Float64Var](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=878) 

``` go 
func Float64Var(p *float64, name string, value float64, usage string)
```

​	Float64Var函数定义一个具有指定名称、默认值和用法字符串的 float64 标志。参数 p 指向一个 float64 变量，用于存储标志的值。

#### func [Func](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=954)  <- go1.16

``` go 
func Func(name, usage string, fn func(string) error)
```

​	Func函数定义一个具有指定名称和用法字符串的标志。每次看到标志时，fn 都会使用标志的值调用。如果 fn 返回非 nil 错误，则将其视为标志值解析错误。

##### Func Example
``` go 
package main

import (
	"errors"
	"flag"
	"fmt"
	"net"
	"os"
)

func main() {
	fs := flag.NewFlagSet("ExampleFunc", flag.ContinueOnError)
	fs.SetOutput(os.Stdout)
	var ip net.IP
	fs.Func("ip", "`IP address` to parse", func(s string) error {
		ip = net.ParseIP(s)
		if ip == nil {
			return errors.New("could not parse IP")
		}
		return nil
	})
	fs.Parse([]string{"-ip", "127.0.0.1"})
	fmt.Printf("{ip: %v, loopback: %t}\n\n", ip, ip.IsLoopback())

	// 256不是一个有效的IPv4组成部分
	fs.Parse([]string{"-ip", "256.0.0.1"})
	fmt.Printf("{ip: %v, loopback: %t}\n\n", ip, ip.IsLoopback())

}
Output:

{ip: 127.0.0.1, loopback: true}

invalid value "256.0.0.1" for flag -ip: could not parse IP
Usage of ExampleFunc:
  -ip IP address
    	IP address to parse
{ip: <nil>, loopback: false}
```

#### func [Int](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=762) 

``` go 
func Int(name string, value int, usage string) *int
```

​	Int函数定义一个具有指定名称、默认值和用法字符串的 int 标志。返回值是存储标志值的 int 变量的地址。

#### func [Int64](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=788) 

``` go 
func Int64(name string, value int64, usage string) *int64
```

​	Int64函数定义一个具有指定名称、默认值和用法字符串的 int64 标志。返回值是存储标志值的 int64 变量的地址。

#### func [Int64Var](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=774) 

``` go 
func Int64Var(p *int64, name string, value int64, usage string)
```

​	Int64Var函数定义一个具有指定名称、默认值和用法字符串的 int64 标志。参数 p 指向一个 int64 变量，用于存储标志的值。

#### func [IntVar](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=748) 

``` go 
func IntVar(p *int, name string, value int, usage string)
```

​	IntVar函数定义一个具有指定名称、默认值和用法字符串的 int 标志。参数 p 指向一个 int 变量，用于存储标志的值。

#### func [NArg](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=706) 

``` go 
func NArg() int
```

​	NArg函数返回在处理标志后剩余的参数数目。

#### func [NFlag](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=683) 

``` go 
func NFlag() int
```

​	NFlag函数返回已设置的命令行标志数目。

#### func [Parse](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=1137) 

``` go 
func Parse()
```

​	Parse函数从 `os.Args[1:]` 解析命令行标志。必须在定义所有标志并在程序访问标志之前调用它。

#### func [Parsed](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=1143) 

``` go 
func Parsed() bool
```

​	Parsed函数报告命令行标志是否已被解析。

#### func [PrintDefaults](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=647) 

``` go 
func PrintDefaults()
```

​	PrintDefaults函数打印一个使用说明，显示所有已定义命令行标志的默认设置。对于值为整数的标志 x，默认输出的格式为：

```
-x int
	usage-message-for-x (default 7)
```

​	使用方法会在除了具有一个字节名称的 bool 标志外的其他地方显示在单独的一行上。对于 bool 标志，类型被省略，如果标志名称为一个字节，则使用说明消息出现在同一行上。如果默认值是类型的零值，则括号中的默认值被省略。列出的类型(这里是 int)可以通过在标志的使用说明字符串中放置一个带反引号的名称来更改；消息中的第一个此类项被认为是要在消息中显示的参数名称，并在显示消息时从消息中删除反引号。例如，给定

```
flag.String("I", "", "search `directory` for include files")
```

输出将是

```
-I directory
	search directory for include files.
```

要更改标志消息的目标，请调用 CommandLine.SetOutput。

#### func [Set](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=500) 

``` go 
func Set(name, value string) error
```

​	Set函数设置命名的命令行标志的值。

#### func [String](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=866) 

``` go 
func String(name string, value string, usage string) *string
```

​	String函数定义一个具有指定名称、默认值和用法字符串的字符串标志。返回值是存储标志值的字符串变量的地址。

#### func [StringVar](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=852) 

``` go 
func StringVar(p *string, name string, value string, usage string)
```

​	StringVar函数定义一个具有指定名称、默认值和用法字符串的字符串标志。参数 p 指向一个字符串变量，用于存储标志的值。

#### func [TextVar](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=940)  <- go1.19

``` go 
func TextVar(p encoding.TextUnmarshaler, name string, value encoding.TextMarshaler, usage string)
```

​	TextVar函数定义一个具有指定名称、默认值和用法字符串的标志。参数 p 必须是一个指向将保存标志值的变量的指针，并且 p 必须实现 encoding.TextUnmarshaler 接口。如果使用标志，则将标志值传递给 p 的 UnmarshalText 方法。默认值的类型必须与 p 的类型相同。

##### TextVar Example
``` go 
package main

import (
	"flag"
	"fmt"
	"net"
	"os"
)

func main() {
	fs := flag.NewFlagSet("ExampleTextVar", flag.ContinueOnError)
	fs.SetOutput(os.Stdout)
	var ip net.IP
	fs.TextVar(&ip, "ip", net.IPv4(192, 168, 0, 100), "`IP address` to parse")
	fs.Parse([]string{"-ip", "127.0.0.1"})
	fmt.Printf("{ip: %v}\n\n", ip)

	// 256 is not a valid IPv4 component
	ip = nil
	fs.Parse([]string{"-ip", "256.0.0.1"})
	fmt.Printf("{ip: %v}\n\n", ip)

}
Output:

{ip: 127.0.0.1}

invalid value "256.0.0.1" for flag -ip: invalid IP address: 256.0.0.1
Usage of ExampleTextVar:
  -ip IP address
    	IP address to parse (default 192.168.0.100)
{ip: <nil>}
```

#### func [Uint](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=814) 

``` go 
func Uint(name string, value uint, usage string) *uint
```

​	Uint函数定义一个具有指定名称、默认值和用法字符串的 uint 标志。返回值是存储标志值的 uint 变量的地址。

#### func [Uint64](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=840) 

``` go 
func Uint64(name string, value uint64, usage string) *uint64
```

​	Uint64函数定义一个指定名称、默认值和使用说明的 uint64 标志。返回值是一个 uint64 变量的地址，该变量存储标志的值。

#### func [Uint64Var](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=826) 

``` go 
func Uint64Var(p *uint64, name string, value uint64, usage string)
```

​	Uint64Var函数定义一个指定名称、默认值和使用说明的 uint64 标志。参数 p 指向一个 uint64 变量，用于存储标志的值。

#### func [UintVar](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=800) 

``` go 
func UintVar(p *uint, name string, value uint, usage string)
```

​	UintVar函数定义一个指定名称、默认值和使用说明的 uint 标志。参数 p 指向一个 uint 变量，用于存储标志的值。

#### func [UnquoteUsage](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=536)  <- go1.5

``` go 
func UnquoteUsage(flag *Flag) (name string, usage string)
```

​	UnquoteUsage函数从标志的使用说明中提取带反引号的名称，并返回未引用的名称和使用说明。给定 "a `name` to show"，返回 ("name", "a name to show")。如果没有反引号，则名称是标志值类型的猜测，如果标志是布尔值，则为空字符串。

#### func [Var](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=996) 

``` go 
func Var(value Value, name string, usage string)
```

​	Var函数定义一个具有指定名称和使用说明的标志。标志的类型和值由第一个参数 Value 表示，该参数通常持有 Value 的用户定义实现。例如，调用者可以创建一个标志，将逗号分隔的字符串转换为字符串切片，给定的切片具有 Value 的方法；特别是，Set 会将逗号分隔的字符串分解为切片。

#### func [Visit](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=467) 

``` go 
func Visit(fn func(*Flag))
```

​	Visit函数按字典顺序访问命令行标志，并为每个标志调用 fn。它仅访问已设置的标志。

#### func [VisitAll](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=453) 

``` go 
func VisitAll(fn func(*Flag))
```

​	VisitAll函数按字典顺序访问命令行标志，并为每个标志调用 fn。它访问所有标志，即使未设置。

## 类型

### type [ErrorHandling](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=365) 

``` go 
type ErrorHandling int
```

​	ErrorHandling定义了FlagSet.Parse在解析失败时的行为。

``` go 
const (
	ContinueOnError ErrorHandling = iota // 返回一个描述性错误。
	ExitOnError  // 调用os.Exit(2) 或对于-h/-help 则调用Exit(0)。
	PanicOnError  // 调用一个带描述性错误的panic。
)
```

如果解析失败，这些常量会使FlagSet.Parse按照描述的行为进行操作。

### type [Flag](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=397) 

``` go 
type Flag struct {
	Name     string // 在命令行中出现的名称
	Usage    string // 帮助信息
	Value    Value  // 设定的值
	DefValue string // 默认值(以文本形式)；用于帮助信息
}
```

​	Flag表示标志的状态。

#### func [Lookup](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=478) 

``` go 
func Lookup(name string) *Flag
```

​	Lookup函数返回命名的命令行标志的Flag结构，如果不存在则返回nil。

### type [FlagSet](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=379) 

``` go 
type FlagSet struct {
	// Usage是解析标志时发生错误时调用的函数。
	// 该字段是一个函数(不是方法)，可以更改以指向自定义错误处理程序。
	// 在调用Usage后发生的情况取决于ErrorHandling设置；
    // 对于命令行，默认值为ExitOnError，
	// 它在调用Usage后退出程序。
	Usage func()
	// 包含已过滤或未导出的字段
}
```

​	FlagSet表示一组已定义的标志。FlagSet的零值没有名称并具有ContinueOnError错误处理。

​	标志名称必须在FlagSet内唯一。尝试定义其名称已在使用中的标志将导致恐慌。

#### func [NewFlagSet](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=1167) 

``` go 
func NewFlagSet(name string, errorHandling ErrorHandling) *FlagSet
```

​	NewFlagSet方法返回一个新的、空的FlagSet，其中包含指定的名称和错误处理属性。如果名称不为空，它将在默认用法消息和错误消息中打印。

#### (*FlagSet) [Arg](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=688) 

``` go 
func (f *FlagSet) Arg(i int) string
```

​	Arg方法返回第i个参数。Arg(0)是在处理标志后剩余的第一个参数。如果请求的元素不存在，则Arg返回空字符串。

#### (*FlagSet) [Args](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=709) 

``` go 
func (f *FlagSet) Args() []string
```

​	Args方法返回非标志参数。

#### (*FlagSet) [Bool](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=728) 

``` go 
func (f *FlagSet) Bool(name string, value bool, usage string) *bool
```

​	Bool方法定义具有指定名称、默认值和用法字符串的布尔标志。返回值是一个bool变量的地址，该变量存储标志的值。

#### (*FlagSet) [BoolVar](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=716) 

``` go 
func (f *FlagSet) BoolVar(p *bool, name string, value bool, usage string)
```

​	BoolVar方法定义具有指定名称、默认值和用法字符串的布尔标志。参数p指向一个bool变量，用于存储标志的值。

#### (*FlagSet) [Duration](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=913) 

``` go 
func (f *FlagSet) Duration(name string, value time.Duration, usage string) *time.Duration
```

​	Duration方法定义具有指定名称、默认值和用法字符串的time.Duration标志。返回值是一个time.Duration变量的地址，该变量存储标志的值。该标志接受一个可接受的time.ParseDuration值。

#### (*FlagSet) [DurationVar](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=899) 

``` go 
func (f *FlagSet) DurationVar(p *time.Duration, name string, value time.Duration, usage string)
```

​	DurationVar方法定义具有指定名称、默认值和用法字符串的time.Duration标志。参数p指向一个time.Duration变量，用于存储标志的值。该标志接受一个可接受的time.ParseDuration值。

#### (*FlagSet) [ErrorHandling](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=433)  <- go1.10

```go 
func (f *FlagSet) ErrorHandling() ErrorHandling
```

​	ErrorHandling方法返回标志集的错误处理行为。

#### (*FlagSet) [Float64](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=884) 

```go 
func (f *FlagSet) Float64(name string, value float64, usage string) *float64
```

​	Float64方法定义了一个指定名称、默认值和用法说明的 float64 标志(flag)。返回值是一个 float64 变量的地址，该变量存储标志的值。

#### (*FlagSet) [Float64Var](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=872) 

```go 
func (f *FlagSet) Float64Var(p *float64, name string, value float64, usage string)
```

​	Float64Var方法定义了一个指定名称、默认值和用法说明的 float64 标志。参数 p 是一个指向 float64 变量的指针，用于存储标志的值。

#### (*FlagSet) [Func](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=947)  <- go1.16

```go 
func (f *FlagSet) Func(name, usage string, fn func(string) error)
```

​	Func方法定义了一个指定名称和用法说明的标志。每次遇到该标志时，都会将标志的值传递给 fn 函数。如果 fn 函数返回一个非 nil 错误，则将其视为标志值解析错误。

#### (*FlagSet) [Init](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=1179) 

```go 
func (f *FlagSet) Init(name string, errorHandling ErrorHandling)
```

​	Init方法为一个标志集设置名称和错误处理属性。默认情况下，零值 FlagSet 使用一个空名称和 ContinueOnError 错误处理策略。

#### (*FlagSet) [Int](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=754) 

```go 
func (f *FlagSet) Int(name string, value int, usage string) *int
```

​	Int方法定义了一个指定名称、默认值和用法说明的 int 标志。返回值是一个 int 变量的地址，该变量存储标志的值。

#### (*FlagSet) [Int64](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=780) 

``` go 
func (f *FlagSet) Int64(name string, value int64, usage string) *int64
```

​	Int64 函数定义了一个指定名称、默认值和用法说明的 int64 标志。返回值是一个 int64 变量的地址，该变量存储标志的值。

#### (*FlagSet) [Int64Var](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=768) 

```go 
func (f *FlagSet) Int64Var(p *int64, name string, value int64, usage string)
```

​	Int64Var方法定义了一个指定名称、默认值和用法说明的 int64 标志。参数 p 是一个指向 int64 变量的指针，用于存储标志的值。

#### (*FlagSet) [IntVar](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=742) 

```go 
func (f *FlagSet) IntVar(p *int, name string, value int, usage string)
```

​	IntVar方法定义了一个指定名称、默认值和用法说明的 int 标志。参数 p 是一个指向 int 变量的指针，用于存储标志的值。

#### (*FlagSet) [Lookup](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=472) 

```go 
func (f *FlagSet) Lookup(name string) *Flag
```

​	Lookup方法返回指定名称的标志(flag)结构体，如果不存在则返回nil。

#### (*FlagSet) [NArg](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=703) 

```go 
func (f *FlagSet) NArg() int
```

​	NArg方法返回处理完flag后剩余的参数个数。

#### (*FlagSet) [NFlag](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=680) 

```go 
func (f *FlagSet) NFlag() int
```

​	NFlag方法返回已设置的flag的数量。

#### (*FlagSet) [Name](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=428)  <- go1.10

```go 
func (f *FlagSet) Name() string
```

​	Name返回设置的标志的名称。

​	Name方法返回flag set的名称。

#### (*FlagSet) [Output](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=420)  <- go1.10

```go 
func (f *FlagSet) Output() io.Writer
```

​	Output方法返回用于使用说明和错误信息的目标io.Writer。如果未设置或设置为nil，则返回os.Stderr。

#### (*FlagSet) [Parse](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=1104) 

```go 
func (f *FlagSet) Parse(arguments []string) error
```

​	Parse方法从参数列表中解析flag定义，该列表不应包括命令名称。必须在FlagSet中定义所有flag并在程序访问flag之前调用。如果设置了但未定义-help或-h，则返回ErrHelp。

#### (*FlagSet) [Parsed](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=1131) 

```go 
func (f *FlagSet) Parsed() bool
```

​	Parsed方法返回是否已调用f.Parse。

#### (*FlagSet) [PrintDefaults](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=575) 

```go 
func (f *FlagSet) PrintDefaults()
```

​	PrintDefaults方法将标志集合中所有定义的命令行标志的默认值打印到标准错误(除非另有配置)。有关更多信息，请参见全局函数PrintDefaults的文档。

#### (*FlagSet) [Set](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=483) 

```go 
func (f *FlagSet) Set(name, value string) error
```

​	Set方法设置指定名称的flag的值。

#### (*FlagSet) [SetOutput](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=439) 

```go 
func (f *FlagSet) SetOutput(output io.Writer)
```

​	SetOutput方法设置用于帮助信息和错误信息输出的目标。如果output为nil，则使用os.Stderr。

#### (*FlagSet) [String](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=858) 

```go 
func (f *FlagSet) String(name string, value string, usage string) *string
```

​	String方法定义了一个指定名称、默认值和用法说明的字符串标志。返回值是一个string类型变量的地址，该变量存储标志的值。

#### (*FlagSet) [StringVar](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=846) 

```go 
func (f *FlagSet) StringVar(p *string, name string, value string, usage string)
```

​	StringVar方法定义了一个指定名称、默认值和用法说明的字符串标志。参数p指向一个string类型的变量，用于存储标志的值。

#### (*FlagSet) [TextVar](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=931)  <- go1.19

```go 
func (f *FlagSet) TextVar(p encoding.TextUnmarshaler, name string, value encoding.TextMarshaler, usage string)
```

​	TextVar方法定义了一个指定名称、默认值和用法说明的标志。参数p必须是一个指向将保存标志值的变量的指针，p必须实现encoding.TextUnmarshaler接口。如果使用了该标志，则标志值将传递给p的UnmarshalText方法。默认值的类型必须与p的类型相同。

#### (*FlagSet) [Uint](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=806) 

```go 
func (f *FlagSet) Uint(name string, value uint, usage string) *uint
```

​	Uint方法定义了一个指定名称、默认值和用法说明的uint标志。返回值是一个uint变量的地址，该变量存储标志的值。

#### (*FlagSet) [Uint64](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=832) 

```go 
func (f *FlagSet) Uint64(name string, value uint64, usage string) *uint64
```

​	Uint64方法定义了一个指定名称、默认值和用法说明的uint64标志。返回值是一个uint64变量的地址，该变量存储标志的值。

#### (*FlagSet) [Uint64Var](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=820) 

```go 
func (f *FlagSet) Uint64Var(p *uint64, name string, value uint64, usage string)
```

​	Uint64Var方法定义了一个指定名称、默认值和用法说明的uint64标志。参数p指向一个uint64类型的变量，用于存储标志的值。

#### (*FlagSet) [UintVar](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=794) 

```go 
func (f *FlagSet) UintVar(p *uint, name string, value uint, usage string)
```

​	UintVar方法定义了一个指定名称、默认值和用法说明的uint标志。参数p指向一个uint类型的变量，用于存储标志的值。

#### (*FlagSet) [Var](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=964) 

```go 
func (f *FlagSet) Var(value Value, name string, usage string)
```

​	Var方法定义了一个指定名称和用法说明的标志。标志的类型和值由第一个参数Value表示，通常Value持有一个用户定义的实现。例如，调用方可以创建一个标志，通过给该slice的方法提供Value，将逗号分隔的字符串转换为字符串的slice；特别地，Set会将逗号分隔的字符串分解为slice。

#### (*FlagSet) [Visit](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=459) 

```go 
func (f *FlagSet) Visit(fn func(*Flag))
```

​	Visit方法按词典顺序访问设置了值的标志，对于每个标志调用fn函数。它只访问已设置的标志。

#### (*FlagSet) [VisitAll](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=445) 

```go 
func (f *FlagSet) VisitAll(fn func(*Flag))
```

​	VisitAll方法按词典顺序访问所有标志，对于每个标志调用fn函数，即使标志未设置也会访问。

### type [Getter](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=359)  <- go1.2

```go 
type Getter interface {
	Value
	Get() any
}
```

​	Getter是一个接口，允许检索存储在标志中的值。它包装了Value接口，而不是作为其一部分，因为它出现在Go 1之后，并具有其兼容性规则。由此包提供的所有Value类型都满足Getter接口，除了Func使用的类型。

### type [Value](https://cs.opensource.google/go/go/+/go1.20.1:src/flag/flag.go;l=350) 

```go 
type Value interface {
	String() string
	Set(string) error
}
```

​	Value是标志中存储的动态值的接口(默认值表示为字符串)。

​	如果Value具有IsBoolFlag() bool方法返回true，则命令行解析器将-name等同于-name=true，而不是使用下一个命令行参数。

​	对于每个存在的标志，Set在命令行顺序中调用一次。标志包可以使用零值接收器(如nil指针)调用String方法。

##### Value Example

```go 
package main

import (
	"flag"
	"fmt"
	"net/url"
)

type URLValue struct {
	URL *url.URL
}

func (v URLValue) String() string {
	if v.URL != nil {
		return v.URL.String()
	}
	return ""
}

func (v URLValue) Set(s string) error {
	if u, err := url.Parse(s); err != nil {
		return err
	} else {
		*v.URL = *u
	}
	return nil
}

var u = &url.URL{}

func main() {
	fs := flag.NewFlagSet("ExampleValue", flag.ExitOnError)
	fs.Var(&URLValue{u}, "url", "URL to parse")

	fs.Parse([]string{"-url", "https://golang.org/pkg/flag/"})
	fmt.Printf(`{scheme: %q, host: %q, path: %q}`, u.Scheme, u.Host, u.Path)

}
Output:

{scheme: "https", host: "golang.org", path: "/pkg/flag/"}

```




