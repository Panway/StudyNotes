## 基础(这是个添加目录的不错的范例，来源：https://github.com/malinkang/SwiftNote)


### 目录

* [1.常量和变量](#1.常量和变量)
* [2.输出](#2.输出)
* [3.注释](#3.注释)
* [4.分号](#4.分号)
* [5.数据类型](#5.数据类型)
	+ [5.1整数](#5.1整数)
	+ [5.2浮点数](#5.2浮点数)
	+ [5.3数值类型转换](#5.3数值类型转换)
	+ [5.4类型别名](#5.4类型别名)
	+ [5.5布尔值](#5.5布尔值)
	+ [5.6元组](#5.6元组)
	+ [5.7可选类型](#5.7可选类型)
		* [5.7.1if 语句以及强制解析](#5.7.1if 语句以及强制解析)
		* [5.7.2可选绑定](#5.7.2可选绑定)
		* [5.7.3隐式解析可选类型](#5.7.3隐式解析可选类型)
* [6.基本运算符](#6.基本运算符)
	+ [6.1赋值运算符](#6.1赋值运算符)
	+ [6.2算术运算符](#6.2算术运算符)
	+ [6.3复合运算符](#6.3复合运算符)
	+ [6.4比较运算符](#6.4比较运算符)
	+ [6.5三目运算符](#6.5三目运算符)
	+ [6.6空合运算符](#6.6空合运算符)
	+ [6.7区间运算符](#6.7区间运算符)
	+ [6.8逻辑运算符](#6.8逻辑运算符)
	+ [6.9位运算符](#6.9位运算符)
	+ [6.10溢出运算符](#6.10溢出运算符)

---------------------------------------------------------------------


<h3 id="1.常量和变量">1.常量和变量</h3>

常量使用`let`来声明，变量使用`var`来声明。

```swift
let maximumNumberOfLoginAttempts = 10
var currentLoginAttempt = 0
```
你可以在一行中声明多个常量或者多个变量，用逗号隔开：

```swift
var x = 0.0, y = 0.0, z = 0.0
```

当你声明常量或者变量的时候可以加上类型标注（type annotation），说明常量或者变量中要存储的值的类型。如果要添加类型标注，**需要在常量或者变量名后面加上一个冒号和空格，然后加上类型名称**。
```swift
var welcomeMessage: String
```

如果你在声明常量或者变量的时候赋了一个初始值，Swift可以推断出这个常量或者变量的类型，就不需要写类型标注。

你可以用任何你喜欢的字符作为常量和变量名，包括 Unicode 字符：

```swift
let π = 3.14159
let 你好 = "你好世界"
let 🐶🐮 = "dogcow"

```
常量与变量名**不能包含数学符号，箭头，保留的（或者非法的）Unicode 码位，连线与制表符。也不能以数字开头，但是可以在常量与变量名的其他地方包含数字**。

<h3 id="2.输出">2.输出</h3>

你可以使用全局函数`println`来输出当前常量或者变量的值

Swift用**字符串插值（string interpolation）**的方式把常量名或者变量名当做占位符加入到长字符串中，Swift 会用当前常量或变量的值替换这些占位符。将常量或变量名放入圆括号中，并在开括号前使用反斜杠将其转义：

```swift

var friendlyWelcome = "Hello!"

println("The current value of friendlyWelcome is \(friendlyWelcome)")
```

<h3 id="3.注释">3.注释</h3>

略

<h3 id="4.分号">4.分号</h3>

Swift 并不强制要求你在每条语句的结尾处使用分号(;)，如果打算在同一行内写多条独立语句，必须使用分号。

```swift
let cat = "🐱"; println(cat)
// 输出 "🐱"
```

<h3 id="5.数据类型">5.数据类型</h3>



<h4 id="5.1整数">5.1整数</h4>

Swift提供了8，16，32，64位的有符号和无符号整数类型。

可以访问不同整数类型的`min`和`max`属性来获取对应类型的最大值和最小值。

```swift

let minValue = UInt8.min

let maxValue = UInt8.max

println("UInt8 min value is \(minValue) and max value is \(maxValue)")
//输出 UInt8 min value is 0 and max value is 255

```

Swift提供了两种特殊的整数类型`Int`和`UInt`，长度与当前平台的原生字节长度相同。

* 在32位平台上，`Int`和`Int32`长度相同，`UInt`和`UInt32`长度相同。
* 在64位平台上，`Int`和`Int64`长度相同，`UInt`和`UInt64`长度相同。

整数可以用二进制，八进制、十进制和十六进制来表示。

* 一个十进制数，没有前缀
* 一个二进制数，前缀是`0b`
* 一个八进制数，前缀是`0o`
* 一个十六进制数，前缀是`0x`

```swift
let decimalInteger = 17
let binaryInteger = 0b10001       // 二进制的17
let octalInteger = 0o21           // 八进制的17
let hexadecimalInteger = 0x11     // 十六进制的17
```

<h4 id="5.2浮点数">5.2浮点数</h4>

Swift提供了两种有符号浮点数类型

* `Double`表示64位浮点数
* `Float`表示32位浮点数


<h4 id="5.3数值类型转换">5.3数值类型转换</h4>

略

<h4 id="5.4类型别名">5.4类型别名</h4>


你可以使用typealias关键字来定义类型别名。

```swift
typealias AudioSample = UInt16
var maxAmplitudeFound = AudioSample.min
// maxAmplitudeFound 现在是 0
```


<h4 id="5.5布尔值">5.5布尔值</h4>

Swift用`Bool`表示布尔类型，`Bool`有两个值`true`和`false`
```swift
let orangesAreOrange = true
let turnipsAreDelicious = false

```

<h4 id="5.6元组">5.6元组</h4>

元组（tuples）把多个值组合成一个复合值。元组内的值可以是任意类型，并不要求是相同类型。

```swift

let http404Error = (404, "Not Found")
// http404Error 的类型是 (Int, String)，值是 (404, "Not Found")

```
可以将一个元组的内容分解（decompose）成单独的常量和变量

```swift
let (statusCode, statusMessage) = http404Error
println("The status code is \(statusCode)")
// 输出 "The status code is 404"
println("The status message is \(statusMessage)")
// 输出 "The status message is Not Found"
```
如果你只需要一部分元组值，分解的时候可以把要忽略的部分用下划线（_）标记：

```swfit
let (justTheStatusCode, _) = http404Error
println("The status code is \(justTheStatusCode)")
// 输出 "The status code is 404"
```
此外，你还可以通过下标来访问元组中的单个元素，下标从零开始：

```swift
println("The status code is \(http404Error.0)")
// 输出 "The status code is 404"
println("The status message is \(http404Error.1)")
// 输出 "The status message is Not Found"
```

你可以在定义元组的时候给单个元素命名：

```swift
let http200Status = (statusCode: 200, description: "OK")
```

给元组中的元素命名后，你可以通过名字来获取这些元素的值：

```swift
println("The status code is \(http200Status.statusCode)")
// 输出 "The status code is 200"
println("The status message is \(http200Status.description)")
// 输出 "The status message is OK"
```

<h4 id="5.7可选类型">5.7可选类型</h4>

使用可选类型来处理值可能缺失的情况。可选类型表示`有值，等于x`或者`没有值`，可选类型使用数据类型加问号的形式表示。比如`Int?`.

<h4 id="5.7.1if 语句以及强制解析">5.7.1if 语句以及强制解析</h4>

可以通过`if`语句判断可选类型的值是否等于`nil`来判断是否包含值得。当你确定可选类型确实包含值之后，你可以在可选的名字后面加一个感叹号（!）来获取值，这被成为可选值的解析。

```swift

let possibleNumber = "123"
let convertedNumber = possibleNumber.toInt()

if convertedNumber != nil {
    println("\(possibleNumber) has an integer value of \(convertedNumber!)")
} else {
    println("\(possibleNumber) could not be converted to an integer")
}

// 打印 123 has an integer value of 123
//不加! 打印123 has an integer value of Optional(123)
```

<h4 id="5.7.2可选绑定">5.7.2可选绑定</h4>


使用可选绑定（optional binding）来判断可选类型是否包含值，如果包含就把值赋给一个临时常量或者变量。可选绑定可以用在if和while语句中来对可选类型的值进行判断并把值赋给一个常量或者变量。

```swift

if let actualNumber = possibleNumber.toInt() {
    println("\(possibleNumber) has an integer value of \(actualNumber)")
} else {
    println("\(possibleNumber) could not be converted to an integer")
}
// 输出 "123 has an integer value of 123"

```

<h4 id="5.7.3隐式解析可选类型">5.7.3隐式解析可选类型</h4>

当确定一个可选类型总会有值，每次都要判断和解析可选值是非常低效的。Swfit定义了一种可选类型叫做隐式解析可选类型。声明一个隐式解析可选类型只需要将类型后面的问号改为感叹号（String!）。隐式可选类型并不需要每次都使用解析来获取可选值。

```java

let assumedString: String! = "An implicitly unwrapped optional string."
println(assumedString)  // 不需要感叹号
// 输出 "An implicitly unwrapped optional string."

```

<h3 id="6.基本运算符">6.基本运算符</h3>

<h4 id="6.1赋值运算符">6.1赋值运算符</h4>

`＝`

<h4 id="6.2算术运算符">6.2算术运算符</h4>

`+`、`-`、`*`，`/`


`%`

Swift中可以对浮点数进行取余。
```swift
8 % 2.5 // 等于 0.5
```

`++` `--`

一元负号运算符

一元正号运算符

<h4 id="6.3复合运算符">6.3复合运算符</h4>

略

<h4 id="6.4比较运算符">6.4比较运算符</h4>

`==` `!=` `>` `<` `>=` `<=`

<h4 id="6.5三目运算符">6.5三目运算符</h4>

`？：`

<h4 id="6.6空合运算符">6.6空合运算符</h4>

空合运算符`(a ?? b)`将对可选类型a进行空判断，如果a包含一个值就进行解封，否则就返回一个默认值b.这个运算符有两个条件:

* 表达式a必须是Optional类型
* 默认值b的类型必须要和a存储值的类型保持一致

<h4 id="6.7区间运算符">6.7区间运算符</h4>

闭区间运算符`（a...b）`定义一个包含从a到b`(包括a和b)`的所有值的区间，b必须大于a。

半开区间（a..<b）定义一个从a到b但不包括b的区间。 该区间包含第一个值而不包括最后的值。

<h4 id="6.8逻辑运算符">6.8逻辑运算符</h4>

`!` `&&` `||`

<h4 id="6.9位运算符">6.9位运算符</h4>

* 按位取反`~`
* 按位与`&`
* 按位或`|`
* 按位异或`^`
* 左移`<<`
* 右移`>>`

<h4 id="6.10溢出运算符">6.10溢出运算符</h4>
略


