## 基础
Swift是iOS，macOS，watchOS和tvOS应用程序开发的新的编程语言。即便如此，Swift的大部分与你使用C语言和Objective-C的经验相同。

Swift包含自身版本的C和Objective-C的基本类型，如`Int`是整数，`Double`和`Float`是浮点值，`Bool`是布尔值，`String`是文本数据。Swift同时提供强大的三个基本集合类型，`Array`，`Set`和`Dictionary`,详见[集合类型（Collection Types）](Collection_Types.md)。

如C语言，Swift用变量储存，用表示名来引用值。Swift还广泛使用值无法更改的变量。这些被称为常量，但是比C语言中的常量强大得多。当你需要使用不改变的值是，常量被Swift广泛使用以确保代码更安全和简洁。

除了熟悉的类型，Swift推出了Objective-C中没有的高级类型，如元组（`tuple`）。元组允许你创建和传递成组的值。你可以用元组让函数以组合值的形式返回多个值。

Swift也推出了可选类型来处理缺省值。可选值表示“有值，是x”或“根本没有值”的意思。可选值就像Objective-C中的空指针，但它适应于任何类型，不只是类。可选值不仅安全并比Objective-C中的空指针富有表现性，它也是Swift许多重要功能的核心。

Swift是类型安全语言，这意味着Swift能让你清楚地知道你的代码可以与什么类型的值一起工作。如果你的部分代码需要一个`String`类型，类型安全会阻止你不小心传入一个`Int`类型。同样地，类型安全会阻止你在需要一个非可选`String`的时候输入一个可选`String`。类型安全帮你在开发阶段提前捕获并修复错误。

## 常量和变量

常量和变量将一个名称（如`maximumNunberOfLoginAttempts`或`welcomeMessage`）和特定类型的值（如数字`10`或字符串`"Hello"`）关联起来。常量的值一旦设定就不能再修改，变量则可以。

#### 声明常量和变量

常量和变量必须在使用前声明。用`let`关键字声明常量，用`var`关键字声明变量。下面的例子说明如何使用常量和变量来跟踪用户尝试登录的次数：
```swift
let maximumNunberOfLoginAttempts = 10
var currentLoginAttemp = 0
```
这段代码可以这样读：
“声明一个名为`maximumNunberOfLoginAttempts`的常量，设置为10，声明一个名为`currentLoginAttemp`的变量，初始值设置为0。”

在这个例子中，尝试登录的最大允许次数声明为常量，因为这个最大值不会变。当前登录次数声明为变量，因为每次登录失败这个值会增加。

你可以在一行定义多个变量或常量，用逗号隔开：
```swift
var x = 0.0, y = 0.0, z = 0.0
```
> 注意：如果一个储存值不会改变，请用`let`关键字声明为常量。用`var`关键字声明能够被改变的存储值。

#### 类型说明

你可以在声明常量或变量的时候提供类型说明，以清楚地表明常量或变量能储存什么类型的值。在常量或变量名后面接上冒号，冒号后面加上空格，空格后面加上类型名。

下面的例子为变量`welcomeMessage`提供了类型说明，以表明变量可以储存字符串类型：
```swift
var welcomeMessage: String
```
冒号意味“...是...类型”，所以以上代码可以读作：
“声明一个字符串类型的变量`welcomeMessage`”。

短语“是`String`类型”意思是“可以储存字符串类型的值”。把它想成能储存“东西的类型”（或“某种东西”）。

`welcomeMessage`因此可以被赋任何字符串值而不报错。
```swift
welcomeMessage = "Hello"
```

你可以在一行之内定义多个类型相同的相关变量，多个变量用逗号隔开，后面只需一个类型说明：
```swift
var red, green, blue: Double
```
> 注意：在实践中，你很少需要写类型说明。如果你在定义变量或常量的时候提供一个初始值，Swift可以推导出该变量或常量的类型，就像[类型安全和类型推导]()中描述的样。上述例子中，`welcomeMessage`没有初始值，所以为其添加了类型说明，而不是从初始值中读取类型。

#### 常量和变量命名

常量和变量的名称可以包括几乎所有的字符，包括Unicode字符：
```swift
let π = 3.14159
let 你好 = "你好世界"
let 🐶🐮 = "dogcow"
```

常量和变量名不能包含空格，数学符号，箭头，私有Unicode标量值或划线画框的字符。也不能以数字开头，即使数字可以被用在名称中其他任何位置。

一旦你声明了某一类型的常量或变量，你不能在声明相同名称的常量或变量，或用它储存不同类型的值。也不能把常量改成变量或把变量改成常量。

> 注意：如果你需要用Swift关键字中词命名变量或常量，用单引号把名称括起来。然而，你应该避免使用这样的名称，除非你没有其他选择。

你可以把现有变量的值改成另一个兼容的值。在下面的例子中，变量`friendlyWelcome`的值从`Hello!`变成`Bonjour!`：
```swift
var friendlyWelcome = "Hello!"
friendlyWelcome = "Bonjour!"
// friendlyWelcome is now "Bonjour!"
```

与变量不同，常量在被赋值之后就不能改变。尝试如此做，代码将在编译的时候报错：
```swift
let languageName = "Swift"
languageName = "Swift++"
// This is a compile-time error: languageName cannot be changed.
```

#### 打印常量和变量

你可以用`print(_:separator:terminator:)`函数打印常量和变量。
```swift
print(friendlyWelcome)
// Prints "Bonjour!"
```
`print(_:separator:terminator:)`函数是一个全局的在适当的地方打印一个或多个值得函数。例如，`print(_:separator:terminator:)`函数或在Xcode的控制台打印输出。`separator`和`terminator`有默认值，所以你可以忽略。默认情况下，函数在行尾添加换行符来结束一个行。传给terminator参数一个空字符串以输入无换行的值。例如：`print(someValue, terminator: "")`。更多详情，请参见[默认参数值]()。

Swift用字符插入的方式在长字符串中包含变量或常量名作为占位符，并提示将常量名或变量名替换为常量或变量当前的值。将变量名或常量名放在括号中，括号前面添加斜杠：
```swift
print("The current value of friendlyWelcome is \(friendlyWelcome)")
// Prints "The current value of friendlyWelcome is Bonjour!"
```
> 注意：[字符串插值]()中描述了所有可以插入到字符串中的选项。

#### 注释
用注释在代码中包含非执行代码，作为笔记或提醒。编译的时候Swift编译器会忽略注释。

Swift中的注释跟C中的注释类似。单行注释以双斜杠开始。
```swift
// This is a comment.
```

多行注释以`/*`开始，以`*/`结尾。
```swift
/* This is also a comment
but is written over multiple lines. */
```

与C中注释不同的是，Swift中，多个注释可以嵌套。你可以在一段注释中书写第二段注释。第二段注释在第一段注释前关闭。
```swift
/* This is the start of the first multiline comment.
 /* This is the second, nested multiline comment. */
This is the end of the first multiline comment. */
```

嵌套注释可以让你快速简便地注释较大的代码块，即使代码已经包含多行注释。

#### 分号
与大多数语言不同的是，Swift不要求你在代码中每个声明后面书写分号（`;`），当然你可以这么做。但是，当你要把多个单独的声明写在同一行时，就需要加上分号。
```swift
let cat = "🐱"; print(cat)
// Prints "🐱"
```

#### 整数

整数所有的没有小数部分的数，比如42和-23。整数包括有符号整数（正数，0或负数）和无符号整数（正数或0）。

Swift提供8位，16位，32位和64位的有符号和无符号整数。这些整数遵循类似于C语言的命名约定，如8位无符号整数的类型为`UInt8`，32位有符号整数的类型为`Int32`。跟所有其他类型一样，这些整数类型有大写的名称。

##### 整数边界
你可以通过`min`和`max`属性访问整数的最大和最小边界：
```swift
let minValue = UInt8.min  // minValue is equal to 0, and is of type UInt8
let maxValue = UInt8.max  // maxValue is equal to 255, and is of type UInt8
```
这些属性属于相应的类型（如上面的类型属于`UInt8`）所以可以与相同类型的表达式一起使用。

##### 有符号整数（`Int`）

大多数情况下，你不需要为你的代码选定特定类型。Swift提供了另一个整数型，`Int`，它与当前设备上的字节数相等。
* 在32位的设备上，`Int`与`Int32`一致。
* 在64位的设备上，`Int`与`Int64`一致。

除非你要使用特定的字节的整数，否则请直接使用`Int`。这有助于代码的一致性和互操作性。在32位设备上，`Int`可以储存介于`-2,147,483,648`和`2,147,483,648`的所有数，这是一个够大的范围。

##### 无符号整数（`UInt`）
Swift也提供了一个无符号整数类型，`UInt`，它与当前设备字节数相同：

* 在32位的设备上，`UInt`与`UInt32`一致。
* 在64位的设备上，`UInt`与`UInt64`一致。

> 试一试：仅当你需要与设备字节数大小相同的无符号整数类型是才使用`UInt`，若非如此，请使用`Int`，即便已知被储存的值是非负数。坚持使用`Int`能保证代码的连贯性，避免了不同类型相互转换的麻烦，也匹配了类型推断。详见[类型安全和类型推断]()。

#### 浮点数

浮点数是有小数部分的数字，如`3.1415`，`0.1`和`-273.15`。

浮点数表示的范围比整数表示的范围宽得多，能储存的数比`Int`类型要大得多或小得多。Swift提供两种有符号浮点数类型：

* `Double`代表64位浮点数类型
* `Float`代表32位浮点数类型

> 注意：`Double`能至少精确到15位小数，而`Float`可以精确到6位小数。用哪一个类型取决与你在代码中要储存的数字。在两者都可用的情况下，推荐使用`Double`。

## 类型安全和类型推导

Swift是类型安全语言。类型安全语言鼓励你明确代码中值得类型。如果你需要`String`类型，你不能错误传入`Int`。

因为Swift是类型安全的，编译时Swift会检查代码类型，并将类型不匹配的地方标记成错误。这确保你能在开发阶段就捕获并处理代码中的错误。

在你使用不同类型的值时类型检查帮你避免错误。但是，这并不意味着你需要为你声明的每一个变量或常量制定类型。如果你没有为值指定类型，Swift会通过类型推导得出合适的类型。类型推导使编译器在编译代码时能够通过检查你所提供的值而自动推断出表达式的类型。

因为有了类型推导，Swift所需要的声明比其他语言，如C或Objective-C，要少。常量和变量仍然是显式类型，但制定类型的大部分工作都有编译器为你完成。

类型推导在你为常量或变量赋初始值的时候非常有用。类型推导通常在你为常量或变量赋字面值的时候，就已经完成。（字面量是你直接用在代码中的值，如`42`和`3.14159`等。）

比如，当你把字面量42赋值给一个没有说明类型的常量时，Swift会推导出你想要这个常量是`Int`类型，因为你已经用一个整数将它初始化：
```swift
let meaningOfLife = 42
// meaningOfLife is inferred to be of type Int
```

依此，如果你没有为浮点字面量制定类型，Swift会推导出你想创建一个`Double`类型值：
```swift
let pi = 3.14159
// pi is inferred to be of type Double
```

Swift在推导浮点数时，总是选择使用`Double`·类型。

如果你结合使用整数和浮点数值的表达式，从上下文会推导出`Double`类型：
```swift
let anotherPi = 3 + 0.14159
// anotherPi is also inferred to be of type Double
```

3本身并没有明确的类型，因此从浮点数作为加法的一部分推断出`Double`作为输出类型。

## 数字字面量
整数字面量可以写作：
* 一个十进制数，无前缀
* 一个二进制数，有`0b`前缀
* 一个八进制数，有`0o`前缀
* 一个十六进制数，有`0x`前缀

浮点数字面量可以是一个没有前缀的十进制数，或一个有`0x`前缀的十六进制数。在小数点的两边都必须有数字。十进制浮点数有一个可选的指数大写或小写的`e`；十六进制浮点数必须有一个指数，大写或小写的`p`。

有`exp`指数的十进制数，基础数被乘以10的幂：
* `1.25e2`指`125.0`。
* `1.25e-2`指`0.0125`。

十六进制类似：
* `0xFp2`指15*2的2次幂，即60.0。
* `0xFp-2`指15*2的-2次幂，即3.375。

下面所有的数的十进制数值都是12.1875：
```swift
let decimalDouble = 12.1875
let exponentDouble = 1.21875e1
let hexadecimalDouble = 0xC.3p0
```

## 数字类型转换

对于代码中所有通用整型的整数用`Int`类型，即便它们是非负。在日常开发中使用默认整型意味着，整型常量和变量直接和代码保持互换性，匹配字面量的类型推导。

当需要用特定类型处理任务时，用其他整型类型，因为需要符合，外部资源数据的确切的大小，或性能表现，内存使用或其他必要的优化。这种情况下，用确切大小的类型有助于捕获任何意外地值的泄露，并隐式记录所有数据的性质。

#### 整型转换
整型常量或变量能够储存的数字的范围因数字类型不同。一个`Int8`类型的常量或变量可以储存`-128`到`127`之间的数，而一个`UInt8`的常量和变量可以储存`0`到`255`之间的数。一个不适合整型常量或变量尺寸的数字，在编译时将被报错：
```swift
let cannotBeNegative: UInt8 = -1
// Uint8 cannot store negative numbers, and so this will report an error
let tooBig: Int8 = Int8.max + 1
// Int8 cannot store a number larger than its maximun value,
// and so this will also report an error.
```
因为每种数字类型可以储存的值的范围不同，因此必须根据具体情况进行值类型转换。这样可以防止隐藏的转换错误，并有助于明确显式转换的意图。





















[< 快速开始](../Welcome_to_Swift/A_Swift_Tour.md) || [基本操作符 >](Basic_Operators.md)
