# 字符串和字符

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

你可以用`for-in`循环遍历字符串值的方式访问单个的字符值：
```swift
for character in "Dog!🐶" {
    print(character)
}
// D
// o
// g
// !
// 🐶
```
`for-in`循环在[For-in循环](Control_Flow.md#For-in循环)中有描述。

另外，你也可以通过提供`Character`类型说明符，从单个字符串字面量创建一个单独的字符变量或常量：
```swift
let exclamationMark: Character = "!"
```

可以通过传递一个字符数组给字符串的初始化方法当作实参来构建字符串值：
```swift
let catCharacters: [Character] = ["C", "a", "t", "!", "🐱"]
let catString = String(catCharacters)
print(catString)
// Prints "Cat!🐱"
```

## 链接字符串和字符

可以用加号(`+`)把字符串链接起来创建新字符串：
```swift
let string1 = "hello"
let string2 = " there"
var welcome = string1 + string2
// welcome now equals "hello there"
```

也可以用加号赋值运算符把字符串与已有字符串连接起来：
```swift
var instruction = "look over"
instruction += string2
// instruction now equals "look over there"
```

可以用字符串类型的`append()`方法把字符与字符串链接起来：
```swift
let exclamationMark: Character = "!"
welcome.append(exclamationMark)
// welcome now equals "hello there!"
```
> 注意：
不能把字符串与字符相连接，因为字符必须只包含一个字符。

如果你用多行字符串字面量建立长字符串的行，你需要字符串中的每一行都以换行结束，包括最后一行。例如：
```swift
let badStart = """
one
two
"""
let end = """
three
"""
print(badStart + end)
// Prints two lines:
// one
// twothree

let goodStart = """
one
two

"""
print(goodStart + end)
// Prints three lines:
// one
// two
// three
```

在上面的代码中，链接`badStart`与`end`得到一个两行的字符串，这并不是想要的结果。因为`badStart`的最后一行并没有以换行结束，这一行与`end`的第一行链接起来了。相反，`goodStart`的所有行都以换行结束，所以当与`end`链接后变成三行，如愿。

## 字符串插值

*字符串插值* 是从常量，变量，字面量和包含表达式值的字面量的混合物构建新字符串的方法。你可以在单行和多行字符串字面量中使用字符串插值。插入到字符串字面量中的值用带有反斜杠(`\`)前缀的括号包围：
```swift
let multiplier = 3
let message = "\(multiplier) times 2.5 is \(Double(multiplier) * 2.5)"
// message is "3 times 2.5 is 7.5"
```

上面的例子中，变量`multiplier`的值以`\(multiplier)`的形式插入到字符串字面量中。字符串插值被用来创建字符串的时候，占位符由`multiplier`的值代替。

`multiplier`也是后面字符串中一个更大表达式的一部分。这个表达式计算计算`Double(multiplier)`的值，然后将结果（`7.5`）插入到字符串中。这种情况下，包含在字符串字面量中的表达式写成`\(Double(multiplier) * 2.5)`。

可以用*扩展字符串分隔符*创建包含*字符串插值字符*的字符串，如果没有*扩展字符串分隔符*，这些*字符串插值字符*将被视为*字符串插值*。例如：
```swift
print(#"Write an interpolated string in Swift using \(multiplier)."#)
// Prints "Write an interpolated string in Swift using \(multiplier)."
```

为了在使用了*扩展字符串分隔符*的字符串中使用*字符串插值*，用*扩展分隔符*匹配字符串中从头到尾所有反斜杠前的数字。
```swift
print(#"6 times 7 is \#(6 * 7)."#)
// Prints "6 times 7 is 42."
```
> 注意：
> 字符串插值中括号中的表达式不能包含有反斜杠(`\`)，回车或缩进。但是可以包含字符串字面量。

## 统一码（Unicode）

`Unicode`是在不同书写系统中编码，呈现，处理文本的国际标准。它能让你以一个标准格式呈现任何语言中的任何字符，和从一个外部文本文件或网页读取或写入这些字符。如本节所述，Swift中的`String`和`Character`完全兼容Unicode。

### Unicode标量值

在幕后，Swift原生`String`类型是从Unocode标量值创建的。Unicode标量值是字符或修饰符的唯一21位数字，例如，`U+0061`表示`LATIN SMALL LETTER A`(`"a"`)（小写拉丁字母A），`U+1F425`表示`FRONT-FACING BABY CHICK`("🐥")（正面小鸡）。

注意并不是所有的21位Unicode标量值都有对应的字符---有一些标量值保留给未来实现或用作UTF-16编码。一般来说，被指定给字符的标量值都有一个名称，如上面提到的`LATIN SMALL LETTER A`和`FRONT-FACING BABY CHICK`。

### 扩展字形集

Swift的`Character`类型的每一个实例代表着一个*扩展字形集*。一个*扩展字形集*是一个由单个或多个Unicode标量值生成的单个人类可读字符的系列。

举个例子。字母`é`可以用带个Unicode标量值`é`表示（`LATIN SMALL LETTER E WITH ACUTE`,或`U+00E9`）。但是，同样的字母也被表示为一对标量值---一个标准字母`e`（`LATIN SMALL LETTER E`，或`U+0065`），后面跟上`COMBINE ACUTE ACCENT`标量值（`U+0301`）。`COMBINE ACUTE ACCENT`被用于向它前面的标量值添加视觉效果，如在被Unicode文本渲染系统渲染时把`e`变成`é`。

上面两个情况中，Swift字符值将字母`é`作为一个呈现了扩展字形集的字符来呈现。第一种情况中，集群包含一个标量值；第二种情况中，集群包含两个标量值：
```swift
let eAcute: Character = "\u{E9}"
// é
let combinedEAcute: Character = "\u{65}\u{301}"
// e followed by ́
// eAcute is é, combinedEAcute is é
```

扩展字形集是一种将复杂的脚本字符以`Character`值来呈现的灵活方式。例如，韩语字母中的音节，可以以预组合或分解的系列来呈现。这两种表现方式都是Swift中单个`Character`值：
```swift
let precomposed: Character = "\u{D55C}"
// 한
let decomposed: Character = "\u{1112}\u{1161}\u{11AB}"
// ᄒ, ᅡ, ᆫ
// precomposed is 한, decomposed is 한
```

扩展字符集群允许封闭符号的标量值（例如`COMBINING ENCLOSING CIRCLE`，或`U+20DD`）封闭其他的Unicode标量值，（两者组合）作为一个单独的`Character`值：
```swift
let encolsedEAcute: Character = "\u{E9}\u{20DD}"
// enclosedEAcute is é⃝ (Use safari to see é⃝ in correct representation)
```

区域标识符号的Unicode标量值可以结合起来表示单个`Character`值，例如`REGIONAL INDICATOR SYMBOL LETTER U (U+1F1FA)`与`REGIONAL INDICATOR SYMBOL LETTER S (U+1F1F8)`：
 ```swift
let regionalIndicatorForUS: Character = "\u{1F1FA}\u{1F1F8}"
// regionalIndicatorForUS is 🇺🇸
 ```

 ### 字符的数量

 用`count`属性获取字符串中字符的数量：
 ```swift
let unusualMenagerie = "Koala 🐨, Snail 🐌, Penguin 🐧, Dromedary 🐪"
print("unusualMenagerie has \(unusualMenagerie.count) characters")
// Prints "unusualMenagerie has 40 characters"
 ```

 注意，Swift中使用字符的扩展字符集，并不意味着对字符串的拼接和修饰会改变字符串中字符的数量。

 例如，如果用四个字符的单词`cafe`初始化字符串，然后在后面拼接`COMBINING ACUTE ACCENT(U+0301)`，结果仍然只有四个字符，第四个字符变成了`é`，而不是 `e`：
 ```swift
 var word = "cafe"
 print("the number of characters in \(word) is \(word.count)")
 // Prints "the number of characters in cafe is 4"

 word += "\u{301}"    // COMBINING ACUTE ACCENT, U+0301

 print("the number of characters in \(word) is \(word.count)")
 // Prints "the number of characters in café is 4"
 ```
 > 注意：
 > 扩展字符集可以组成多个Unicode标量值。这意味着不同的字符--包括字符相同，表现不同的字符--需要不同的内存来储存。因此，Swift呈现的字符串中，并不是每个字符占用相同数量的内存。所以，如果不通过遍历字符串决定扩展字符的边界，将无法计算字符串中字符的数量。如果使用特别长的字符串，记住，为了决定字符串的字符，`count`属性需要遍历整个字符串中的Unicode标量值。
 > `count`属性返回的数量并不总是与含有相同字符的`NSString`类型值的`length`属性相同。`NSString`的长度基于UTF-16字符串中16位码的数量，而不是字符串中Unicode扩展字符集的数量。

## 访问和修改字符串

可以通过字符串的方法和属性，或下标语法来访问和修改字符串。

### 字符串索引
每个字符串都有一个关联的索引类型，`String.Index`，索引对应着字符串中字符的位置。

如上面提到的，不同的字符需要的内存不同，所以为了决定特定位置是什么字符，必须从头到尾遍历字符串的Unicode标量值。因为这个原因，Swift中的字符串不能用整数值做索引。

用`startIndex`属性访问字符串第一个字符的位置。用`endIndex`属性访问字符串最后一个字符的位置。因此，对于字符串下标方法，`endIndex`属性不是合法的参数。如果字符串是空，`startIndex`等于`endIndex`。

你可以用字符串的`index(before:)`和`index(after:)`方法访问给定索引的前一个会后一个。用`index(_:ofsetBy:)`方法访问与指定索引相隔较远的索引，而不是调用前面的方法若干次。

可以用下标语法访问特定索引的字符：
```swift
let greeting = "Guten Tag!"
greeting[greeting.startIndex]
// G
greeting[greeting.index(before: greeting.endIndex)]
// !
greeting[greeting.index(after: greeting.startIndex)]
// u
let index = greeting.index(greeting.startIndex, offsetBy: 7)
greeting[index]
// a
```

尝试访问字符串范围外的索引，或字符串范围意外的字符，会触发运行时错误。
```swift
greeting[greeting.endIndex] // Error
greeting.index(after: greeting.endIndex) // Error
```

用`indices`属性访问字符串中所有独立字符的索引：
```swift
for index in greeting.indices {
  print("\(greeting[index])", terminator: "")
}
// Prints "G u t e n   T a g ! "
```
> 注意：
> 你可以在任何实现了Collection协议的类型上使用`startIndex`，`endIndex`属性和`index(before:)`，`index(after:)`，`index(_:offsetBy:)`方法。这些包括`String`，如例所示，也包括其他集合类型，如 `Array`，`Dictionary`，和`Set`。

### 插入和移除

用`insert(_:at:)`方法将单个字符插入到字符串的指定位置，用`insert(contentsOf:at:)`方法将另一个字符串插入到字符串的指定位置。
```swift
var welcome = "hello"
welcome.insert("!", at: welcome.endIndex)
// welcome now equals "hello!"

welcome.insert(contentsOf: " there", at: welcome.index(before: welcome.endIndex))
// welcome now equals "hello there!"
```

用`remove(at:)`方法从字符串指定位置移除单个字符，用`removeSubrange(_:)`方法移除指定范围的子字符串：
```swift
welcome.remove(at: welcome.index(before: welcome.endIndex))
// welcome now equals "hello there"

let range = welcome.index(welcome.endIndex, offsetBy: -6)..<welcome.endIndex
welcome.removeSubrange(range)
// welcome now equals "hello"
```

> 注意：
> 你可以对实现了`RangeReplaceableCollection`协议的任何类型使用`insert(_:at:)`，`insert(contentsOf:at:)`，`remove(at:)`，和`removeSubrange(_:)`等方法。这些类型包括`Array`，`Dictionary`和`Set`。

## 子字符串

当你从一个字符串中获取子字符串时---例如，使用下标或诸如`prefix(_:)`的方法---得到的结果是`Substring`类型，而不是另外一个字符串。Swift的`Substring`有大部分跟`String`相同的方法，这意味着你可以像使用字符串一样使用子字符串。但是，与字符串不同的是，当在字符串上执行操作时，较少用到子字符串。当你打算把结果存储较长时间时，把子字符串转换为字符串。例如：
```swift
let greeting = "Hello, world!"
let index = greeting.firstIndex(of: ",") ?? greeting.endIndex
let beginning = greeting[..<index]
// beginning is "Hello"

// Convert the result to a String for long-term storage.
let newString = String(beginning)
```

如字符串一样，每段子字符串都有一段内存，组成子字符串的字符被储存在该内存中。字符串与子字符串的不同在于，为了优化性能，一个子字符串可以共用原字符串的内存，或这使用被用来储存过其他子字符串的内存片段。（字符串也有类似的优化，但是如果两个字符串共用内存，则他们相等）。这种性能优化，使你在修改字符串或子字符串之前，不同担心复制内存时的性能消耗。如上面提到的，子字符串不适合长期保存---因为他们与原字符串共享内存，整个原字符串在任何一个其子字符串被使用前，必须保存在内存中。

上面的例子中，`greeting`是一个字符串，这意味着它有一段储存组成该字符串的所有字符的内存。因为`beginning`是`greeting`的子字符串，它共享了`greeting`的内存。相反，`newString`是一个字符串---当从子字符串被创建时，它就拥有自己的内存片段。下面的图展示了这种关系：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/stringSubstring_2x.png" alt="子字符串" width="450"/>
</p>

> 注意：
> `String`和`Substring`都实现了 `StringProtocol`协议，这意味着操作字符串的函数能很方便地接受`StringProtocol`值。你可以用`String`值或`Substring`值调用该函数。

## 字符串比较

Swift提供三种比较文本值的方法：字符串和字符相等，前缀相等和后缀相等。

### 字符串和字符相等比较

字符串和字符使用等于运算符（`==`）和不等运算符（`!=`）进行检查，如[比较运算符]()中所述。
```swift
let quotation = "We're a lot alike, you and I."
let sameQuotation = "We're a lot alike, you and I."
if quotation == sameQuotation {
    print("These two strings are considered equal")
}
// Prints "These two strings are considered equal"
```

如果字符串与字符串，字符与字符的扩展字符集在规范上相同（*canonically equivalent*），那么，两个字符串（或两个字符）被认为是相等的。如果扩展字符集有相同的表现和语言上的意义，则在规范上相同，即便它们在幕后由不同的Unicode标量值组成。

例如，`LATIN SMALL LETTER E WITH ACUTE(U+00E9)`与链接了`COMBINING ACUTE ACCENT (U+0303)`的`LATIN SMALL LETTER E(U+0065)`在规范上相同。这两种扩展字符集都是表示字符`é`的有效方法，因此被认为在规范上相同：
```swift
// "Voulez-vous un café?" using LATIN SMALL LETTER E WITH ACUTE
let eAcuteQuestion = "Voulez-vous un caf\u{E9}?"

// "Voulez-vous un café?" using LATIN SMALL LETTER E and COMBINING ACUTE ACCENT
let combinedEAcuteQuestion = "Voulez-vous un caf\u{65}\u{301}?"

if eAcuteQuestion == combinedEAcuteQuestion {
    print("These two strings are considered equal")
}
// Prints "These two strings are considered equal"
```

相反地，`LATIN CAPITAL LETTER A`(`U+0041`，或`"A"`)，用在因英文中，与`CYRILLIC CAPITAL LETTER A`(`U+0410`，或`"A"`)，用在俄文中，是不相等的。字符看起来相似，但是拥有不同的语言意义：
```swift
let latinCapitalLetterA: Character = "\u{41}"

let cyrillicCapitalLetterA: Character = "\u{0410}"

if latinCapitalLetterA != cyrillicCapitalLetterA {
    print("These two characters are not equivalent.")
}
// Prints "These two characters are not equivalent."
```
> 注意：
Swift中字符串和字符的比较对语言环境不敏感。

### 前缀和后缀相等比较
使用`hasPrefix(_:)`和`hasSuffix(_:)`方法检查字符串是否含有特定的前缀或后缀，这两个方法需要一个`String`类型的参数，返回一个布尔值。

下面的例子中，是一个表示莎士比亚的《罗密欧与朱丽叶》前两节场景位置的字符串数组：
```swift
let romeoAndJuliet = [
    "Act 1 Scene 1: Verona, A public place",
    "Act 1 Scene 2: Capulet's mansion",
    "Act 1 Scene 3: A room in Capulet's mansion",
    "Act 1 Scene 4: A street outside Capulet's mansion",
    "Act 1 Scene 5: The Great Hall in Capulet's mansion",
    "Act 2 Scene 1: Outside Capulet's mansion",
    "Act 2 Scene 2: Capulet's orchard",
    "Act 2 Scene 3: Outside Friar Lawrence's cell",
    "Act 2 Scene 4: A street in Verona",
    "Act 2 Scene 5: Capulet's mansion",
    "Act 2 Scene 6: Friar Lawrence's cell"
]
```

你可以对`romeoAndJuliet`使用`hasPrefix(_:)`方法来统计这场戏中第一节(`Act 1`)的场景数：
```swift
var act1SceneCount = 0
for scene in romeoAndJuliet {
  if scene.hasPrefix("Act 1") {
    act1SceneCount += 1
  }
}
print("There are \(act1SceneCount) scenes in Act 1")
// Prints "There are 5 scenes in Act 1"
```

类似地，用`hasSuffix(_:)`方法统计发生在Capulet's和Friar Lawrence的场景数：
```swift
var mansionCount = 0
var cellCount = 0
for scene in romeoAndJuliet {
    if scene.hasSuffix("Capulet's mansion") {
        mansionCount += 1
    } else if scene.hasSuffix("Friar Lawrence's cell") {
        cellCount += 1
    }
}
print("\(mansionCount) mansion scenes; \(cellCount) cell scenes")
// Prints "6 mansion scenes; 2 cell scenes"
```
> 注意：
`hasPrefix(_:)`方法和`hasSuffix(_:)`方法执行逐个字符规范相等来比较每个字符串中的扩展字符集，如[字符串和字符相等比较](Strings_and_Characters.md#字符串和字符相等比较)中所述。

## 字符串的Unicode表示

当一个Unicode字符串被写入到文本文件或其他储存时，字符串中的Unicode标量值被以数个Unicode定义的编码表单被编码。每个表单以最小的`代码单元`对字符串进行编码。这些（表单）包括UTF-8编码表单（将字符串编码成8位代码单元），UTF-16编码表单（将字符串编码成16位代码单元）和UTF-32编码表单（将字符串编码成32位代码单元）。

Swift提供了不同的方法来访问字符串的Unicode表示。你可以用`for-in`语句遍历字符串，从而以Unicode扩展字符集访问单个的字符值。这个过程在[使用字符](Strings_and_Characters.md#使用字符)中有述。

另外，访问其他三种兼容Unicode的方式表示的字符串：
* 一个UTF-8代码单元集合（通过`utf8`属性访问）
* 一个UTF-16代码单元集合（通过`utf16`属性访问）
* 一个21位Unicode标量值，与字符串的UTF-32编码表单相同（通过`unicodeScalars`属性访问）

下面的例子表示了字符串的不同呈现，字符串由`D`，`o`，`g`，`!!`(`DOUBLE EXCLAMATION MARK`,或Unicode标量值`U+203C`)，和字符🐶 (`DOG FACE`或Unicode标量值`U+1F436`)组成：
```swift
let dogString = "Dog‼🐶"
```

### UTF-8表示

可以通过遍历`utf8`属性来访问一个字符串的UTF-8表示。这个属性是`String.UTF8View`类型，是一个无符号8位值(`UInt8`)的集合，对应字符串UTF-8表示中的每个比特：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/UTF8_2x.png" alt="UTF-8表示" width="450"/>
</p>

```swift
for codeUnit in dogString.utf8 {
  print("\(codeUnit)", terminator: "")
}
print("")
// Prints "68 111 103 226 128 188 240 159 144 182 "
```

上面的例子中，前面的三个`codeUnit`值(`68`, `111`, `103`)代表字符`D`，`o`和`g`，它们的UTF-8表示与ASCII表示相同。接下来的三个十进制数`codeUnit`值(`226`,`128`,`188`)是`DOUBLE EXCLAMATION MARK`字符的三比特UTF-8呈现。最后四个`codeUnit`值(`240`,`159`,`144`,`182`)是`DOG FACE`字符的四比特UTF-8呈现。

### UTF-16表示

可以通过遍历`utf16`属性来访问一个字符串的UTF-16表示。这个属性是`String.UTF16View`类型，是一个无符号16位值(`UInt16`)的集合，对应字符串UTF-16表示中的每个比特：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/UTF16_2x.png" alt="UTF-16表示" width="450"/>
</p>

```swift
for codeUnit in dogString.utf16 {
    print("\(codeUnit) ", terminator: "")
}
print("")
// Prints "68 111 103 8252 55357 56374 "
```

再一次，前三个`codeUnit`值代表字符`D`，`o`和`g`，它们的UTF-16代码单元与UTF-8字符串的表示拥有同样的值（因为这些纯Unicode代表ASCII字符）。

第四个`codeUnit`值是与十六进制值`203C`相等的十进制值，代表着字符`DOUBLE EXCLAMATION MARK`的纯Unicode`U+203C`。这个字符可以以UTF-16单个代码单元表示。

第五和第六个`codeUnit`值(`55357`和`56374`)是字符`DOG FACE`UTF-16代理表示。它们一个是`U+D38D`(十进制`55357`)高级代理，一个是`U+DC36`(十进制`56374`)低级代理。

### 纯Unicode表示

可以通过遍历`unicodeScalars`属性来访问字符串值的纯Unicode表示。属性是`unicodeScalarView`类型，它是一个`unicodeScalar`类型值的集合。

每个`unicodeScalar`有一个返回纯21位值的`value`属性，以`UInt32`值形式表示：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/UnicodeScalar_2x.png" alt="纯Unicode表示" width="450"/>
</p>

```swift
for scalar in dogString.unicodeScalars {
    print("\(scalar.value) ", terminator: "")
}
print("")
// Prints "68 111 103 8252 128054 "
```

`value`属性的前三个`UnicodeScalar`值(68, 111, 103)再一次代表字符`D`，`o`和`g`。

第四个`codeUnit`值(`8252`)是与十六进制`203C`相等的十进制值，代表着字符`DOUBLE EXCLAMATION MARK`的纯Unicode`U+203C`。

`value`属性的第五位和最后一位`UnicodeScalar`，`128054`，是十六进制值`1F436`的十进制，代表着字符`DOG FACE`的纯Unicode`U+1F436`。

作为另外一种请求`value`属性的方式，每个`UnicodeScalar`值可用来创建一个新`String`值，例如用字符串插值：
```swift
for scalar in dogString.unicodeScalars {
    print("\(scalar) ")
}
// D
// o
// g
// ‼
// 🐶
```

[< 基本操作符](Basic_Operators.md) || [集合类型 >](Collection_Types.md)
