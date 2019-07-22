## 字符串和字符

字符串是一系列字符，例如`hello, world`或`albatross`。Swift中的字符串以`String`类型呈现。有多种方法可以访问字符串的内容，包括以`Character`集合的形式。

Swift的`String`和`Character`类型提供快速，兼容Unicode的方式在代码中使用文本。创建和操作字符串的语法轻量可读，使用与C语言相似的字符串字面量。字符串链接简单到用加号（`+`）运算符就可，字符串的可变性通过选择常量还是变量来管理，就像Swift中其他类型的值样。你也可以使用字符串将常量，变量，字面量和表达式插入到更长的字符串中，这个过程称为字符串插值。这让为显示，储存和打印自定义创建字符串变得容易。

除了语法简洁，Swift的`String`类型是一个快速，现代化的字符串实现。每个字符串都由单独用Unicode编码的字符组成，并提供多种Unicode呈现方式访问这些字符。

> 注意：
> Swfit中`String`类型桥接于Foundation中的`NSString`类。Foundation扩展了`String`以暴露`NSString`中定义的方法。这意味着，如果导入Foundation框架，你可以直接在`String`上使用`NSString`中定义的方法，而不需要类型转换。
> 关于更多与Foundation和Cocoa框架一起使用`String`的信息，参见[桥接String与NSString](https://developer.apple.com/documentation/swift/string#2919514)

## 字符串字面量

你可以把预定义的字符串值当作字面量包含在代码中。字符串字面量就是一串用引号（`"`）括起来的字符。

用字面量初始化变量或常量。
```swift
let someString = "Some string literal value"
```
注意，Swift推导出`someString`为字符串类型，因为它被字符串常量初始化。

### 多行字符串字面量

如果你需要使用占多行的字符串，用多行字符串字面量---一串用三重引号（`"""`）括起的字符：
```swift
let quotation = """
The White Rabbit put on his spectacles.  "Where shall I begin,
please your Majesty?" he asked.

"Begin at the beginning," the King said gravely, "and go on
till you come to the end; then stop."
"""
```
多行字符串字面量包含引号之间的所有行。字符串以起始三重引号开始，以末尾三重引号结束，这意味着下面的字符串并不是以换行符开始和结束。
```swift
let singleLineString = "These are the same."
let multilineString = """
These are the same.
"""
```
当你代码中多行字符串字面量包含一个换行符，该换行符会显示在字符串值中。如果你想用换行来让代码易读，但是不想让换行出现在字符串值中，在行尾添加转义符（`\`）：
```swift
let softWrappedQuotation = """
The White Rabbit put on his spectacles.  "Where shall I begin, \
please your Majesty?" he asked.

"Begin at the beginning," the King said gravely, "and go on \
till you come to the end; then stop."
"""
```

用空行当作第一行和最后一行，使多行字符串字面量以换行开始和结束。如：
```swift
let lineBreaks = """

This string starts with a line break.
It also ends with a line break.

"""
```

多行字符串字面量可以缩进以匹配周围的代码。右三重引号（`"""`）之前的空格告诉Swift在所有其他行之前要忽略哪些空格。但是，如果你在第一行前面写上空格，而不是在右三重引号之前，则该空格会被包含：
<p align="center">
<img src="https://docs.swift.org/swift-book/_images/multilineStringWhitespace_2x.png" alt="取余运算符" width="450"/>
</p>

上面的例子中，索然整体多行字符串字面量都有缩进，但第一行与最后一行并不以空格开始。中间行的缩进比右三重引号多，所以该行缩进多处的四个空格。

### 字面量中的特殊字符

字符串字面量可以包含以下特殊字符：
* 转义特殊字符`\0` (空字符), `\\` (反斜杠backslash), `\t` (水平标签horizontal tab), `\n` (缩紧line feed), `\r` (回车carriage return), `\"` (双引号double quotation mark) and `\'` (单引号single quotation mark)
* 任意Unicode标量值，写作`\u{n}`，`n`是1-8位十六进制数字（Unicode将在下面讨论）

下面的代码展示了特殊字符的四个例子。`wiseWords`包含两个被转义的引号。`dollarSign`，`blackHeart`和`sparklingHeart`常量示范了纯Unicode格式：
```swift
let wiseWords = "\"Imagination is more important than knowledge\" - Einstein"
// "Imagination is more important than knowledge" - Einstein
let dollarSign = "\u{24}"        // $,  Unicode scalar U+0024
let blackHeart = "\u{2665}"      // ♥,  Unicode scalar U+2665
let sparklingHeart = "\u{1F496}" // 💖, Unicode scalar U+1F496
```
因为多行字符串字面量用三重引号而不是单引号，你可以在多行字符串字面量里用双引号(`"`)而不需要转义。要在多行字符串字面量中包含`"""`，至少将其中一个双引号转义。例如：
```swift
let threeDoubleQuotationMarks = """
Escaping the first quotation mark \"""
Escaping all three quotation marks \"\"\"
"""
```

### 扩展字符串分隔符

可以将字符串字面量放到*扩展分隔符*中，使字面量中的特殊字符不发挥效果。将字符串用置于引号中，然后用数字符号（`#`）包围。例如，打印字符串字面量`#"Line 1\nLine 2"#`打印出行缩进转义结果（`\n`）而不是打印两行字符串。

如果你需要字符串字面量中特殊字符的效果，请匹配转义字符(`\`)后面字符串的数字符号。例如，如果你的字符串是#"Line 1\nLine 2"#，你打算使用换行，你可以用#"Line 1\#nLine 2"#代替。类似地，`###"Line1\###nLine2"###`也会换行。

使用扩展分隔符的字符串字面量也可以是多行字符串字面量。你可以用扩展字符串分隔符在多行字符串中包含`"""`，重写字面量结束（三重引号）的默认行为。例如：
```swift
let threeMoreDoubleQuotationMarks = #"""
Here are three more double quotes: \"""
"""#
```

## 初始化空字符串

创建空字符串作为创建长字符串的起点，要么用空字符串字面量给变量赋值，要么用初始化语法初始化一个字符串实例：
```swift
var emptyString = ""       // empty string literal
var anotherEmptyString = String()  // initializer syntax
// these two strings are both empty, and are equivalent to each other.
```

通过检查字符串值的布尔属性`isEmpty`可以知道字符串值是否为空：
```swift
if emptyString.isEmpty {
  print("Nothing to see here")
}
// Prints "Nothing to see here"
```

## 字符串可变性

通过将一个字符串赋值给变量（可修改）或常量（不可修改）来表明它是否可变：
```swift
var variableString = "Horse"
variableString += " and carriage"
// variableString is now "Horse and carriage"

let constantString = "Highlander"
constantString += " and another Highlander"
// this reports a compile-time error - a constant string cannot be modified
```

> 注意：
这种操作跟Objective-C和Cocoa中的可变字符串不同，Cocoa中你可以选择两个类（`NSString`和`NSMutableString`）来决定字符串是否可变。

## 字符串是值类型

Swift中字符串类型是值类型。如果你创建一个新的字符串值，将该字符串传递个函数或方法，或赋值给变量或常量时，会被复制。这几种情况中，会创建现有字符串的备份，新的备份会被传递或用于赋值，而不是原始的字符串。值类型在[结构体和枚举是值类型](Structures_and_Classes.md#结构体和枚举是值类型)中有讨论。

## 使用字符







[< 基本操作符](Basic_Operators.md) || [集合类型 >](Collection_Types.md)
