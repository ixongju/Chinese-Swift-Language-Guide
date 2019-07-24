## 基本运算符
运算符是用作检查，改变或组合值的符号或短语。例如，`let i = 1 + 2`中的加号（`+`）将两个数相加，`enteredDoorCode && passedRetinaScan`中的逻辑与运算符（`&&`）连结两个布尔值。

Swift支持大部分C中的运算符，同时添加了一些能消除常见编码错误的功能。为了避免赋值运算符（`=`）在需要使用等号运算符（`==`）时候被误用，赋值运算符（`=`）不返回值。数学运算符（`+`,`-`,`*`,`/`等）侦测且不允许值越界，以避免在计算后的值大于或小于值的类型能储存的范围的时候出现不可预测的错误。你可以用Swift中的溢出运算符选择值的溢出行为，如[高级运算符](Advanced_Operators.md#溢出运算符)中所述。

Swift也提供C中没有的范围运算符，例如`a..<b`和`a...b`，就是值范围的快捷表达式。

本节叙述Swift中常用运算符。[高级运算符](Advanced_Operators.md)中介绍了Swift中的高级运算符，并描述了如何自定义运算符，和为自定义类型实现标准运算符。

## 术语

运算符分为一元运算符（`unary`），二元运算符（`binary`），三元运算符（`ternary`）：
* 一元运算符操作单个目标（如`-a`）。一元前置运算符直接出现在目标的前面（如`!b`)，一元后置运算符直接出现在目标的后面（如`c!`）。
* 二元运算符操作两个目标（如`2 + 3`），二元运算符都是中缀（`infix`），所以他们在两个目标之间。
* 三元运算符操作三个目标。与C语言一样，Swift只有一个三元运算符，三元条件运算符（`a ? b : c`）。

被运算符操作的是操作数（`operands`）。在`1 + 2`表达式中，加号`+`是二元运算符，操作数是值`1`和`2`。

## 赋值运算符
赋值运算符（如`a = b`)可以用值`b`初始化或更新`a`的值：
```swift
let b = 10
var a = 5
a = b
// a is now equal to 10
```
如果赋值语句的右边是一个有多个值的元组，它的值可以一次性被拆解成多个常量或变量：
```swift
let (x, y) = (1, 2)
// x is equal to 1, and y is equal to 2.
```

与C或Objective-C中的赋值运算符不同的是，Swift运算符不返回值。下面的语句无效：
```swift
if x = y {
  // This is not valid, because x = y does not return a value.
}
```

这一功能能防止赋值运算符被误当作等于运算符（`==`）使用。通过使`if x = y`无效，Swift帮你避免了此类错误。

## 数学运算符

Swift支持所有数字类型的数学运算符：
* 加法（`+`）
* 减法（`-`）
* 乘法（`*`）
* 除法（`/`）
```swift
1 + 2       // equals 3
5 - 3       // equals 2
2 * 3       // equals 6
10.0 / 2.5  // equals 4.0
```

不同于C和Objective-C中的数学运算符，Swift默认不允许数值溢出。你也可以用Swift的溢出运算符处理值的溢出行为。参见[高级运算符](Advanced_Operators.md#溢出运算符)。

加法运算符也支持字符串的连结：
```swift
"hello, " + "world" // equals "hello, world"
```

### 取余运算符
取余运算符（`a % b`）计算多少个`b`可以填入`a`中，并且返回剩余的值。（被称为*取余*）。
> 注意：
> 取余运算在其他语言中被称作取模运算。但是，在Swift中，严格地说对于负数意味着，它是取余运算而不是取模运算。

下面描述取余运算如何工作。为了计算`9 % 4`，你首先需要知道`9`中可以填多少个`4`：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/remainderInteger_2x.png" alt="取余运算符" width="300"/>
</p>

你可以将`2`个`4`填入`9`中，剩余`1`（后面橘黄色）。

在Swift中，这样写：
```swift
9 % 4 // equals 1
```

为了决定`a % b`的值，运算符`%`计算下列方程的解，然后返回余数作为输出：

a = (b x `some multiplier`) + `remainder`

`some multiplier`是用`b`填`a`最多的次数。

将9和4带入方程中：

9 = (4 x 2) + 1

对于负数`a`也使用相同的方法：
```swift
-9 % 4 // equals -1
```
得到余数-1。

b是负数时，符号会被忽略。这意味着`a % b`和`a % -b`是一样的结果。

### 一元减法运算符

数值的符号可以用前缀`-`切换，即一元减法运算符：
```swift
let three = 3
let minusThree = -three  // minusThree equals -3
let plusThree = -minusThree // plusThree equals 3, or "minus minus three"
```
一元减法运算符直接放在被操作数的前面，不需要空格。

### 一元加法运算符

一元加法运算符直接返回被操作的数，不做任何改变：
```swift
let minusSix = -6
let alsoMinusSix = +minusSix // alsoMinusSix equals -6
```
即使一元加法运算符实际上什么也不做，在你使用一元减法运算符表示负数时，可以用它来对称地表示正数。

## 复合赋值运算符

如C语言一样，Swift提供结合赋值运算和其他运算的复合赋值运算符。一个例子是赋值加运算：
```swift
var a = 1
a += 2
// a is now equal to 3
```

表达式`a += 2`是`a = a + 2`的简写。更高效地，加法运算和赋值运算被结合起来赋予给一个能同时执行这两种运算的运算符。
> 注意：
复合运算符并不返回值，例如，你不能这么写`let b = a += 2`。

更多关于Swift标准库操作符的信息，参见[运算符声明](https://developer.apple.com/documentation/swift/swift_standard_library/operator_declarations)

## 比较运算符

Swift支持C语言的所有标准比较运算符：
* 等于（a == b）
* 不等于（a != b）
* 大于（a > b）
* 小于（a < b）
* 大于或等于（a >= b）
* 小于或等于（a <= b）
> 注意：
Swift同时提供两个身份运算符（=== 和 !==），用来测试两个引用是否都指向同一个实例。更多请看[结构体和类（Structures and Classes）](Structures_and_Classes.md#身份运算符)

每个比较运算符返回一个`Bool`值指明语句是否是`true`：
```swift
1 == 1   // true because 1 is equal to 1
2 != 1   // true because 2 is not equal to 1
2 > 1    // true because 2 is greater than 1
1 < 2    // true because 1 is less than 2
1 >= 1   // true because 1 is greater than or equal to 1
2 <= 1   // false because 2 is not less than or equal to 1
```

比较运算符一般都用在条件语句中，例如`if`语句：
```swift
let name = "world"
if name == "world" {
    print("hello, world")
} else {
    print("I'm sorry \(name), but I don't recognize you")
}
// Prints "hello, world", because name is indeed equal to "world".
```

更多关于`if`语句，参见[控制流](Control_Flow.md)

你可以比较两个含有相同类型和数量的元素的元组。元组的比较从左到右进行，每次比较一个，直到两个值不相等。这两个值被比较，结果决定着整个元组比较的结果。若果所有的元素都相等，则元组相等。例如：
```swift
(1, "zebra") < (2, "apple")   // true because 1 is less than 2; "zebra" and "apple" are not compared
(3, "apple") < (3, "bird")    // true because 3 is equal to 3, and "apple" is less than "bird"
(4, "dog") == (4, "dog")      // true because 4 is equal to 4, and "dog" is equal to "dog"
```
上面的例子中，可以看出第一行是从左到右的比较。因为1小于2，`(1, "zebra")`被认为小于`(2, "apple")`，其余的值会被忽略。`zebra`比`apple`小并没什么关系，比较结果已经被第一组元素决定了。然后，当元组的第一组元素相等时，就会比较第二组——第二行和第三行就是这么进行的。

只有当运算符可被用于每个单独的元素时，才能将该运算符用于比较元组。例如，下面列出的代码中，你可以比较一个`(String, Int)`类型的元组，是因为`String`和`Int`的值可以用`<`运算符进行比较，相反，两个`(String, Bool)`的元组不能用`<`运算符比较，因为`<`运算符不能用于比较`Bool`值。
```swift
("blue", -1) < ("purple", 1)        // OK, evaluates to true
("blue", false) < ("purple", true)  // Error because < can't compare Boolean values
```

> 注意：
Swift标准库包含元素个数在7个以下的元组的比较运算符。要比较元素个数7个以上的元组，你需要自己实现该操作符。

## 三元条件运算符
三元运算符是运算三个目标的特殊操作符，格式为`question ? answer1 : answer2`。它是根据`question`的真假来计算两个表达式中的一个的简写。当`question`是真，会计算并返回`answer1`的值；否则，计算并返回`answer2`的值。

三元运算符是下面代码的简写：
```swift
if question {
  answer1
} else {
  answer2
}
```

再来个计算table row高度的例子。如果row有头部，则其高度需要比内容的高度高50，如果没有头部，只比内容高20：
```swift
let contentHeight = 40
let hasHeader = true
let rowHeight = contentHeight + (hasHeader ? 50 : 20)
// rowHeight is equal to 90
```

上面的例子是下面的简写：
```swift
let contentHeight = 40
let hasHeader = true
let rowHeight: Int
if hasHeader {
    rowHeight = contentHeight + 50
} else {
    rowHeight = contentHeight + 20
}
// rowHeight is equal to 90
```

第一个例子中用到的三元运算符意味着rowHeight可以只用一行代码设置正确的值，比第二个例子中的更简洁。

三元运算符为决定用二个表达式中的哪一个提供了一个高效地简写。但要小心使用，如果过多使用三元运算符，会导致代码难以阅读。不要将多个三元运算符放到一个复合语句中。

## Nil-Coalescing运算符
(暂时不知道怎么翻译这个名词)
**nil-Coalescing** 运算符（`a ?? b`）用于当可选值`a`包含值时解包可选值`a`，或当`a`是`nil`时返回默认值`b`。`a`表达式是可选类型。`b`表达式的值必须与`a`中值的类型相同。

nil-Coalescing运算符是下面代码的简写：
```swift
a != nil ? a! : b
```

上面的代码用三元运算符，当a不是nil时强制解包其值，否则返回值b。nil-Coalescing运算符用一种更优雅的方式提供简洁，易读地检查并解包方法。
> 注意：
如果a的值是非nil，则不会计算b的值。这被称为短路评估。（short-circuit evaluation）。

下面的代码用nil-coalescing运算符从一个默认颜色和一个可选用户自定义颜色中做出选择：
```swift
let defaultColorName = "red"
var userDefinedColorName: String? // default to nil

var colorNameToUse = userDefinedColorName ?? defaultColorName
// userDefinedColorName is nil, so colorNameToUse is set to the default of "red"
```
`userDefinedColorName`被定义为可选`String`，默认值是`nil`。因为是可选类型，所以你可以用nil-coalescing去考量它。上例中，这个操作符用于给`String`变量`colorNameToUse`指定初始值。因为`userDefinedColorName`为`nil`，表达式`userDefinedColorName ?? defaultColorName`返回`defaultColorName`的值，`red`。

如果给`userDefinedColorName`赋非`nil`的值然后用nil-coalescing运算符检查，则会使用`userDefinedColorName`内部的值而不是默认值：
```swift
userDefinedColorName = "green"
colorNameToUse = userDefinedColorName ?? defaultColorName
// userDefinedColorName is not nil, so colorNameToUse is set to "green"
```

## 范围运算符

Swift有好几个范围运算符，它们是值的范围表达式的缩写。

### 封闭范围运算符

封闭范围运算符定义了一个从`a`到`b`的范围，包括`a`和`b`。且`a`的值不得比`b`大。

当你想遍历一个范围中所有值的时候，封闭范围运算符比较有用。如`for-in`循环：
```swift
for index in 1...5 {
  print("\(index) times 5 is \(index * 5)")
}
// 1 times 5 is 5
// 2 times 5 is 10
// 3 times 5 is 15
// 4 times 5 is 20
// 5 times 5 is 25
```

关于`for-in`循环，参见[控制流](Control_Flow.md)。

### 半开范围运算符

半开范围运算符（`a..<b`）定义了一个从`a`到`b`的范围，但是不包括`b`。之所以说是半开，是因为它包括第一个值，不包括第二个值。如封闭范围运算符，`a`的值必须比`b`的值小。如果`a`等于`b`，则范围是空。

当使用从零开始的列表，例如数组时，半开范围运算符就比较有用。要历数整个列表长度：
```swift
let names = ["Anna", "Alex", "Brian", "Jack"]
let count = names.count
for i in 0..<count {
  print("Person \(i + 1) is called \(names[i])")
}
// Person 1 is called Anna
// Person 2 is called Alex
// Person 3 is called Brian
// Person 4 is called Jack
```

注意数组包含四个元素，但`0..<count`只历数到`3`（最后一个元素的索引），因为它是半开范围。更多信息请看[数组](Collection_Types.md#数组)。

### 单边范围

单边范围运算符可选择范围开始的方向——例如，一个包含从2开始到数组最后虽有元素的范围。这种情况下，你可以省略一边的范围运算符。这种范围被称为*单边范围*因为只有一边有值。例如：
```swift
for name in names[2...] {
  print(name)
}
// Brian
// Jack

for name in names[..2] {
  print(name)
}
// Anna
// Alex
// Brian
```

半开范围运算符也有一个只写末尾值的单边格式。就如包含两边的范围一样，末尾值可以不包含。例如：
```swift
for name in names[..<2] {
  print(name)
}
// Anna
// Alex
```

单边范围可以还有其他用途，并不限于脚标。你不能遍历一个省略了首位值的单边范围，因为不清楚从哪里开始遍历。你可以遍历一个省略了末尾值的单边范围；尽管如此，因为范围的无限延续，你需要确保循环有一个明确的结尾。你也可以检查一个单边范围是否包括某一个值，如下列代码所示：
```swift
let range = ...5
range.contains(7)   // false
range.contains(4)   // true
range.contains(-1)  // true
```

## 逻辑运算符

逻辑运算符可以修改或结合布尔逻辑值`true`和`false`。Swift支持三种基于C的标准逻辑运算符：
* 逻辑否（`!a`）
* 逻辑与（`a && b`）
* 逻辑或（`a || b`）

### 逻辑否运算符

逻辑否运算符用于反转布尔值，让`true`变成`false`，`false`变成`true`。

逻辑否运算符是前置操作符，出现在操作值的前面，不加空格。可以读作“非`a`”，如下例所示：
```swift
let allowedEntry = false
if !allowedEntry {
  print("ACCESS DENIED")
}
// Prints "ACCESS DENIED"
```

语句`if !allowedEntry`可以读作“如果非allowedEntry”。随后的代码只在“非allowedEntry”为真的时候才会执行；也就是`allowedEntry`为`false`时。

如这个例子样，好的变量和常量的名字的选择有助于让代码可读和简洁，避免双重否定和易混淆的逻辑语句。

### 逻辑与运算符

逻辑与运算符（`a && b`）创建当所有的值是`true`时整个表达式的值才是`true`的逻辑表达式。

如果有一个值是`false`，则整个表达式的值是`false`。事实上，如果第一个值是`false`，第二个值就不会被判断，因为已经可以判断整个表达式的值不是`true`。这被称为*最短评估*。

下面的例子考量两个`Bool`值，且只有当两个值都是`true`时才允许访问之后的代码：
```swift
let enteredDoorCode = true
let passedRetinaScan = false
if enteredDoorCode && passedRetinaScan {
  print("Welcome!")
} else {
  print("ACCESS DENIED")
}
// Prints "ACCESS DENIED"
```

### 逻辑或运算符

逻辑或运算符（`a || b`）是由两个相邻管道字符组成的中缀运算符。用逻辑或运算符创建当两个中的一个是`true`时整个表达式的值是`true`的表达式。

如上面的逻辑与运算符，逻辑或运算符用最短评估考量表达式。如果逻辑或表达式的左边是`true`，右边的部分将不被计算，因为右边的表达式影响不到整个表达式的输出。

下面的例子中，第一个布尔值（`hasDoorKey`）是`false`，但是第二个值（`knowsOverridePassword`）是`true`。因为第一个值是`true`，整个表达式是`true`，所以可以通行：
```swift
let hasDoorKey = false
let knowsOverridePassword = true
if hasDoorKey || knowsOverridePassword {
  print("Welcome!")
} else {
  print("ACCESS DENIED")
}
// Prints "Welcome!"
```

### 结合逻辑运算符

可以结合多个逻辑运算符来创建更长的复合表达式：
```swift
if enteredDoorCode && passedRetinaScan || hasDoorKey || knowsOverridePassword {
  print("Welcome!")
} else {
  print("ACCESS DENIED")
}
// Prints "Welcome!"
```
这个例子用了`&&`和`||`运算符来创建更长的复合表达式。但是，`&&`和`||`仍然只能操作两个值，所以这实际上是三个小表达式链接在一起。这个例如可以读作：
（略）

基于`enteredDoorCode`，`passedRetinaScan`，`hasDoorKey`的值，第一个表达式是`false`。但是，emergency override password已知，所以整个复合表达式仍然是`true`。

> 注意：
Swift的逻辑运算符`&&`和`||`是左结合，意味着多个逻辑运算符的复合表达式中，首先考虑最左边的子表达式。

### 显式括号

有时候在不明确需要括号的时候用上括号也很有用，可以使表达式的意图更易读。在上面的例子中，加上括号可以使复合表达式第一部分意图更明确：
```swift
if (enteredDoorCode && passedRetinaScan) || hasDoorKey || knowsOverridePassword {
    print("Welcome!")
} else {
    print("ACCESS DENIED")
}
// Prints "Welcome!"
```

括号的使用清楚地表明前两个值被视为整个逻辑的一部分。复合表达式的输出不变，但是整个复合表达式的意图更加明确。可读性总是比简洁性优先的；将括号用在可以使你的意图更明显的地方。

[< 基础](The_Basic.md) || [字符串和字符 >](Strings_and_Characters.md)
