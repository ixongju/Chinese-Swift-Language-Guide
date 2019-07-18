## 基础
Swift是iOS，macOS，watchOS和tvOS应用程序开发的新的编程语言。即便如此，Swift的大部分与你使用C语言和Objective-C的经验相同。

Swift包含自身版本的C和Objective-C的基本类型，如`Int`是整数，`Double`和`Float`是浮点值，`Bool`是布尔值，`String`是文本数据。Swift同时提供强大的三个基本集合类型，`Array`，`Set`和`Dictionary`,详见[集合类型（Collection Types）](Collection_Types.md)。

如C语言，Swift用变量储存，用表示名来引用值。Swift还广泛使用值无法更改的变量。这些被称为常量，但是比C语言中的常量强大得多。当你需要使用不改变的值是，常量被Swift广泛使用以确保代码更安全和简洁。

除了熟悉的类型，Swift推出了Objective-C中没有的高级类型，如元组（`tuple`）。元组允许你创建和传递成组的值。你可以用元组让函数以组合值的形式返回多个值。

Swift也推出了可选类型来处理缺省值。可选值表示“有值，是x”或“根本没有值”的意思。可选值就像Objective-C中的空指针，但它适应于任何类型，不只是类。可选值不仅安全并比Objective-C中的空指针富有表现性，它也是Swift许多重要功能的核心。

Swift是类型安全语言，这意味着Swift能让你清楚地知道你的代码可以与什么类型的值一起工作。如果你的部分代码需要一个`String`类型，类型安全会阻止你不小心传入一个`Int`类型。同样地，类型安全会阻止你在需要一个非可选`String`的时候输入一个可选`String`。类型安全帮你在开发阶段提前捕获并修复错误。

## 常量和变量

常量和变量将一个名称（如`maximumNunberOfLoginAttempts`或`welcomeMessage`）和特定类型的值（如数字`10`或字符串`"Hello"`）关联起来。常量的值一旦设定就不能再修改，变量则可以。

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
// Int8 cannot store a number larger than its maximum value,
// and so this will also report an error.
```
因为每种数字类型可以储存的值的范围不同，因此必须根据具体情况进行值类型转换。这样可以防止隐藏的转换错误，并有助于明确显式转换的意图。

要转换特定类型的数字，你可以用已知的值初始化一个新数字。下面的例子中，常量`twoThousand`是`UInt16`类型，而`one`是`UInt8`类型。他们不能被直接相加，因为类型不同。但是，在例子中，用`UInt16(one)`创建一个新的`UInt16`类型的数，然后用这个替换原来的值。
```swift
let twoThousand: UInt16 = 2_000
let one: UInt8 = 1
let twoThousandAndOne = twoThousand + UInt16(one)
```
因为加号的两边都是`UInt16`了，所以允许加法运算。输出类型被推导为`UInt16`类型，因为它是两个`UInt16`的值的和。

为了转换指定类型的数字，用这个值初始化想要的数字类型。下面的例子中，常量`twoThousand`是`UInt16`类型，`one`是`UInt8`类型。由于它们类型不同，所以不能直接相加。所以，例子中用`one`的值创建一个`UInt16`类型的数字，用这个值代替原来的值：
```swift
let twoThousand: UInt16 = 2_000
let one: UInt = 1
let twoThousandAndOne = twoThousand + UInt16(one)
```
因为现在加号的两边都是`UInt16`类型，所以允许执行加法运算。输出常量`twoThousandAndOne`可以被推导出是`UInt16`类型，因为它是两个`UInt16`类型数字的和。

`SomeType(ofInitialValue)`是初始化Swift类型，传入初始值的默认方法。在背后，`UInt16`有一个接受`UInt8`类型值的初始化方法，所以这个方法被用做从已有的`UInt8`值创建`UInt16`值。你不能传入任意类型，但是，它必须是`UInt16`已经提供了初始化方法的类型。扩展已有类型，为其提供接受新类型的初始化方法在[扩展](Extensions.md)中有描述

#### 整数与浮点数转换
整型数与浮点型数之间的转换必须是显式的：
```swift
let three = 3
let pointOneFourOneFiveNine = 0.14159
let pi = Double(three) + pointOneFourOneFiveNine
// pi equals 3.14159, and is inferred to be of type Double
```
在这里，常量`three`的值被用来创建新的`Double`类型的值，所以加号的两边是相同类型。如果没有这种转换，加法运算是不被允许的。

浮点型数转换为整型数也必须显式地进行。整型数可以用`Double`或`Float`型的值初始化：
```swift
let integerPi = Int(pi)
// integerPi  equals 3, and is inferred to be of type Int
```

用浮点数初始化整型数时，往往被截掉小数部分。这意味着，`4.75`会变成`4`，`-3.9`会变成`-3`。

> 注意：
数字常量或变量之间的结合不同于数字字面值之间的结合。字面值3可以被直接相加给字面值0.14159，因为数字字面值没有明确的类型。它们的类型只有在被编译器计算的时候，才会被决定。

## 类型别名
类型别名为一个已知的类型定义一个别名。你可以用`typealias`关键字定义别名。

但你需要以一个更符合上下文的名称来使用一个已知类型的时候，别名就很有用来，例如，当使用特定大小的外部资源数据时：
```swift
typealias AudioSample = UInt16
```
一旦你定义来别名，你可以在任何能够使用原始名的地方使用别名。
```swift
var maxAmplitudeFound = AudioSample.min
// maxAmplitudeFound is now 0
```

这里，`AudioSample`被定义为`UInt16`的别名。因为是别名，调用`AudioSample.min`实际上就是调用`UInt16.min`，这为`maxAmplitudeFound`提供了一个初始值`0`。

## 布尔值

Swift有基本的布尔值，为`Bool`。布尔值与逻辑相关，因为它们只能表示真或假。Swift提供两个布尔常量值，`true`和`false`。
```swift
let orangesAreOrange = true
let turnipsAreDelicious = false
```

从`orangesAreOrange`和`turnipsAreDelicious`被布尔值字面量赋值可以推导出它们是布尔类型。就像上面提到的`Int`和`Double`类型，如果你在创建变量或常量的时候就为他们赋值`true`或`false`,就不需要显式指定为`Bool`类型。用已知类型的字面量初始化常量或变量的时候，Swift的类型推导会让代码更准确和可读。

当你使用诸如`if`等条件语句的时候，布尔类型就显得异常有用：
```swift
if turnipsAreDelicious {
    print("Mmm, tasty turnips!")
} else {
    print("Eww, turnips are horrible.")
}
// Prints "Eww, turnips are horrible."
```

关于`if`等条件语句，详见[控制流](Control_Flow.md)

Swift的类型安全机制，防止把非布尔值当作`Bool`类型。下面的例子将报编译错误：
```swift
let i = 1
if i {
  // this example will not compile, and will report an error
}
```

但，下面的做法是可行的：
```swift
let i = 1
if i == 1 {
  // this example will compile successfully
}
```

`i == 1`的比较接过是`Bool`类型，所以第二个例子通过了类型检查。关于`i == 1`这样比较的讨论在[基本操作符](Basic_Operators.md)中。

像其他类型安全的例子样，这个做法能避免意外的错误，并且确保代码意图明显。

## 元组

元组将多个指组合为一个复合值。元组中的值可以是任何类型的，并不需要都是同样的类型。

这个例子，`(404, "Not Found")` 是描述HTTP状态码的元组。HTTP状态码是你访问web页面的时候web服务器返回的特定值。如果你访问一个不存在的网页，就会返回一个`404 Not Found`的状态码。
```swift
let http404Error = (404, "Not Found")
// http404Error is of type (Int, String), and equals (404, "Not Found")
```
`(404, "Not Found")`元组通过组合`Int`和`String`类型为HTTP状态码提供两个分开的值：一个数字和一个人类可读的描述。这个元组可以被描述为：“一个类型为`(Int, String)`的元组”。

你可以从任何排列在一起的类型创建元组，只要你需要，它们可以包含很多不同类型。没有什么会阻止你创建诸如`(Int, Int, Int)`或`(String, Bool)`等类型或任何满足你需要的元组。

你可以将元组内容拆解成分离的常量或变量，然后像使用常量或变量样用它们：
```swift
let (statusCode, statusMessage) = http404Error
print("The status code is \(statusCode)")
// Prints "The status code is 404"
print("The status message is \(statusMessage)")
// Prints "The status message is Not Found"
```

如果你只需要元组中一部分值，拆解的时候可以用下划线(`_`)忽略其余的：
```swift
let (justTheStatusCode, _) = http404Error
print("The status code is \(justTheStatusCode)")
// Prints "The status code is 404"
```

或者，用从`0`开始的索引访问单独的元素：
```swift
print("The status code is \(http404Error.0)")
// Prints "The status code is 404"
print("The status message is \(http404Error.1)")
// Prints "The status message is Not Found"
```

定义元组的时候，你可以给元素取名：
```swift
let http200Status = (statusCode: 200, description: "OK")
```

如果你命名了元组中的元素，你可以用名称来访问这些元素：
```swift
print("The status code is \(http200Status.statusCode)")
// Prints "The status code is 200"
print("The status message is \(http200Status.description)")
// Prints "The status message is OK"
```

元组在作为函数的返回值的时候会很有用。一个尝试检索网页的函数可能返回`(Int, String)`类型的元组，用来描述检索是成功还是失败。通过返回一个仅有两个值的元组，函数能为结果提供比返回单个值时更详细的信息。更多信息，请看[函数（Functions）](Functions.md)。

> 注意：
> 对于相关值的简单组合，元组是很有用的。但不适合创建复杂的数据结构。如果你的数据结构可能会很复杂，把它建模成一个类或结构体，而不是元组。更多信息，请看[结构体和类（Structures and Classes）](Structures_and_Classes.md)

## 可选值
在一个值可能缺失的情况下，使用可选值。一个可选值代表两种可能性：或者有一个值，你可以解包这个值，然后使用，或者根本没有值。

> 注意：
C或者Objective-C中并没有可选值的概念。Objective-C中相似概念是，返回一个对象的方法可以返回`nil`。`nil`意味着可用对象缺失。但是，这只对对象有效——对结构体，C类型，枚举无效。对于这些类型，Objective-C方法会返回一个特别的值（例如`NSNotFound`）来表明值的缺失。这个行为确保方法的使用者知道有一个特别的值需要测试，并且记得检查。Swift的可选值适用于所有的类型，而不需要特殊的常量。

这里有一个如何使用可选值来应对值的缺失的情况的例子。Swift的`Int`型有一个尝试把`String`类型的值转换为`Int`型值的初始化方法，不是每一个字符串都可以被转换为整数。字符串`"123"`可以转换为数字`123`，但是字符串`"hello, world"`没有明显的数字可以转换。

下面的例子展示了用这个初始化方法将字符串转换为整数：
```swift
let possibleNumber = "123"
let convertedNumber = Int(possibleNumber)
// convertedNumber is inferred to be of type "Int?", or "optional Int"
```

因为初始化方法可能失败，所以返回来*可选*`Int`，而不是`Int`。可选的`Int`写作`Int?`，而不是`Int`。问好表明这个值包含可选项，意味着它可能包含其他的`Int`值，或它根本没有值。（它不能包含其他的，例如`Bool`型或`String`型。它是`Int`或什么也没有。）

### nil

通过指定特殊值`nil`给一个可选变量，把变量设置为无值状态。
```swift
var serverResponseCode: Int? = 404
//  serverResponseCode contains an actual Int value of 404
serverResponseCode = nil
// serverResponseCode now contains no value
```

> 注意：
你不能用`nil`给非可选的常量或变量赋值。如果你的代码中一个常量或变量需要在某些条件下使用缺失值，请将它声明为相关类型的可选值。

如果定义可选变量时不提供默认值，变量将自动被赋值`nil`：
```swift
var surveyAnswer: String?
// surveyAnswer is automatically set to nil
```
> 注意：
Swift中的nil与Objective-C中的nil不同。Objective-C中，nil是指向一个不存在对象的指针。Swift中，nil不是指针——它是某种类型值的缺失。任何类型的可选值都可以被设置成nil，不只是对象类型。

### if语句与强制解包
你可以用if语句比较可选值与`nil`来判断一个可选值是不是包含有值。用等号（`==`）或不等号（`!=`）进行比较。

如果可选值有值，它就不会等于`nil`：
```swift
if convertedNumber != nil {
  print("convertedNumber contains some integer value.")
}
// Prints "convertedNumber" contains some integer value.
```

一旦你确定可选值确实含有值，你可以通过在可选值名后面加上感叹号（`!`）的方式访问内部值。感叹号表示，“我知道可选值里面一定有值，请使用它。”这种方法成为可选值的*强制解包*：
```swift
if convertedNumber != nil {
    print("convertedNumber has an integer value of \(convertedNumber!).")
}
// Prints "convertedNumber has an integer value of 123."
```

更多关于`if`语句，参见[控制流](Control_Flow.md)

> 注意：
试着用感叹号访问不存在值的可选值，从而出发运行时错误。在用感叹号强制解包之前，请确保可选值包含非nil的值。

### 可选绑定
用*可选绑定*确定可选值有没有包含值，如果有，用一个临时常量或变量使该值可用。可选绑定可以与`if`语句或`while`语句一起用来检查可选值内部的值，并将该值抽出给变量或常量，作为语句的一部分。更过关于`if`和`while`语句的讨论，在[控制流](Control_Flow.md)中。

可选绑定的`if`语句如下：
```
if let constantName = someOptional {
    statements
}
```
你可以用可选绑定重写上面的例子，而不是使用强制解包：
```swift
if let actualNumber = Int(possibleNumber) {
    print("The string \"\(possibleNumber)\" has an integer value of \(actualNumber)")
} else {
    print("The string \"\(possibleNumber)\" could not be converted to an integer")
}
// Prints "The string "123" has an integer value of 123"
```

代码可以这么读：
“如果`Int(possibleNumber)`返回的可选值`Int`包含值，就将它赋值给常量`actualNumber`”。

如果转换成功了，常量`actualNumber`在第一个if分支中可用。它已经被可选值中的值初始化了，所以没有必要在用感叹号前缀去访问该值。这个例子中，`actualNumber`简单地被用做打印转换结果。

你可以在可选绑定中使用常量和变量。如果你想在第一个分支中操作`actualNumber`的值，你可以写成`if var actualNumber`。可选值的值会以变量的形式而不是常量变得可用。

你可以根据需要，在一个`if`语句中包含多个布尔条件和可选绑定语句，各个语句之间用逗号隔开。如果任何一个可选绑定的值是`nil`，或任何一个布尔条件是`false`，则整个`if`语句会被视作`false`。下面的两个`if`语句是等效的：
```swift
if let firstNumber = Int("4"), let secondNumber = Int("42"), firstNumber < secondNumber && secondNumber < 100 {
  print("\(firstNumber) < \(secondNumber) < 100")
}
// Prints "4 < 42 < 100"

if let firstNumber = Int("4") {
  if let secondNumber = Int("42") {
    if firstNumber < secondNumber && secondNumber < 100 {
      print("\(firstNumber) < \(secondNumber) < 100")
    }
  }
}
// Prints "4 < 42 < 100"
```

> 注意：
在if语句中用可选绑定创建的常量或变量只在if语句体中可用。想法，用guard语句创建的常量和变量，在guard语句之后的代码中可用。详见[提前推出](Control_Flow.md#提前推出)

### 隐式解包可选值
就像上面描述的，可选值表明允许一个常量或变量没有值。可以用`if`语句检查可选值是否含有值，如果有的话，可以用可选绑定的方式有条件解包来访问可选值的值。

有时候，一个值被设置之后，明确地知道一个可选值总是有值。这种情况下，每次访问可选值的值并不都需要检查和解包，因为总是能够安全地访问该值。

这种可选值被定义为*隐式解包可选值*。在你想变成可选值的类型后面写上感叹号，而不是问号，来书写隐式解包可选值。

隐式解包可选值在可选值首次本定义之后立即能确定值的存在，并在之后任何时刻都存在的情况下，显得很有用。Swift中可选值最主要的应用是类的初始化，[不拥有引用和隐式解包可选值属性](Automatic_Reference_Counting.md#不拥有引用和隐式解包可选值属性)中有描述。

隐式解包可选值本质上是一个普通可选值，但也可用作非可选值，每次访问是不需要解包的可选值。下面的例子展示了以显式字符串方式访问一个可选字符串和一个隐式解包可选值字符串的不同行为：
```swift
let possibleString: String? = "An optional string."
let forcedString: String = possibleString! // requires an exclamation mark

let assumedString: String! = "An implicitly unwrapped optional string."
let implicitString: String = assumedString // no need for an exclamation mark
```

你可以把隐式解包可选值想象为允许可选值在被使用的时候自动解包。可不是每次用的时候都要加上感叹号，在声明可选值的时候就放一个感叹号在后面。

> 注意：
如果一个隐式解包可选值是nil，你试图访问解包的值时，会出发运行时错误。结果跟你用感叹号强制解包一个不存在值的普通可选值。

你可以把隐式解包可选值视为普通可选值，然后检查时候含有值：
```swift
if assumedString != nil {
  print(assumedString!)
}
// Prints "An implicitly unwrapped optional string."
```

你也可以将隐式解包可选值进行可选绑定，在一个语句中检查并解包它的值：
```swift
 if let definiteString = assumedString {
   print(definiteString)
 }
 // Prints "An implicitly unwrapped optional string"
```

> 注意：
当在稍后某一点变量的值可能为nil时，不要使用隐式解包可选值。在变量生命周期中，如果你经常需要检查其值是否为nil，请用普通可选值。

## 错误处理
用错误处理来响应程序在执行过程中遇到的错误情况。

与可以通过值得存在和缺失表明函数的成功或失败的可选值对反，错误处理允许你决定引起错误的最底层原因，并且，如果需要，可以将错误传递到程序的另一部分。

当一个函数遇到了错误情况，它会抛出错误。然后函数的调用者可以捕获并适当地响应这个错误：
```swift
func canThrowAnError() throws {
  // this function may or may not throw an error
}
```

一个函数通过在声明中包含`throws`关键字来表明它可能抛出错误。当你调用可能抛出错误的函数时，在表达式的前面写上`try`关键字。

Swift会自动将错误传播出当前作用域，知道它被`catch`语句捕获。
```swift
do {
  try canThrowAnError()
  // no error was thrown
} catch {
  // an error was thrown
}
```
`do`语句创建了一个新作用域，它允许错误在一个或多个`catch`分句中传递。

这个例子表明错误处理如何响应不同的错误情况：
```swift
func makeASandwich() throws {
  // ...
}

do {
    try makeASandwich()
    eatASandwich()
} catch SandwichError.outOfCleanDished {
    washDished()
} catch SandwichError.missingIngredients(let ingredients) {
    buGroceries(ingredients)
}
```
在这个例子中，如果没有干净的碟子或缺失原料，`makeASandwich()`函数会抛出错误。因为`makeASandwich()`可以抛出错误，函数被`try`语句调用。通过用`do`语句包住函数，抛出的任何错误将被传递到`catch`分句中。

如果没有抛出错误，`eatASandwich()`将被调用。如果抛出了错误，并且匹配`SandwichError.outOfCleanDished`分支，`washDished()`函数将被调用。如果抛出的错误匹配`SandwichError.missingIngredients`分支，则`buGroceries(_:)`函数配合catch捕捉到的关联值`[String]`值被调用。

抛出，捕获和传递错误的详细资料，参见[错误处理](Error_Handling.md)

## 断言和先决条件

#### 用断言调试

#### 强制先决条件















[< 快速开始](../Welcome_to_Swift/A_Swift_Tour.md) || [基本操作符 >](Basic_Operators.md)
