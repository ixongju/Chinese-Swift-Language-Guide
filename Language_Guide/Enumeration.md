# 枚举

枚举为一组相同类型的值定义一个常用类型，能让你一类型安全的方式使用这些值。

如果你熟悉C语言，你应该知道C语言枚举为一组整数值分配相关联的名称。Swift中的枚举更灵活，不需要为枚举的每一个情况提供值。如果一个值（原始值）被提供给枚举的每种情况，这个值可以是字符串，字符，或一个整数或浮点数值。

或者，枚举的项可以指定任何类型的关联值，就像其他语言中的联合或不定类型。你可以将一组相关情况定义为枚举的一部分，每种情况都有一个适当类型的关联值。

Swift中的枚举是第一级别类型。它能实现许多由类支持传统功能，例如计算属性为枚举提供当前值的额外信息。实例方法为枚举实例的值提供函数功能。枚举也可以定义初始化方法，以提供初始情况值；也可以在原有实现基础上扩展功能；也可以实现协议提供标准功能。

更多信息，查看[属性]()，[方法]()，[初始化]()，[扩展]()和[协议]()。

## 枚举语法
用`enum`关键字命名枚举，并将枚举体放入大括号内：
```swift
enum SomeEnumeration {
  // enumeration definition goes here
}
```

指南针四个方向的枚举：
```swift
enum CompassPoint {
  case north
  case south
  case east
  case west
}
```
定义在枚举中的值就是枚举的分支。你可以用`case`关键字添加新的枚举分支。

> 注意：不像C语言和Objective-C，Swift的枚举分支并不默认是数值。上例中，CompassPoint中的north，south，east和west并不明确表示为0，1，2和3，不同的枚举分支本身就是值，具有明确定义的CompassPoint类型。

多个枚举分支可以书写成一行，用逗号隔开：
```swift
enum Planet {
  case mercury, venus, earth, mars, jupiter, saturn, uranus, neptune
}
```

每个枚举定义了一个新类型。就像Swift中其他类型一样，名称以大写开头。用单数而不是复数命名枚举，以便他们看起来不言而喻：
```swift
var directionToHead = CompassPoint.west
```

directionToHead的类型会在用CompassPoint初始化的时候被推导出来。一旦directionToHead被设置成CompassPoint的某一个值，你可以用简单的点号语法设置成其他值：
```swift
directionToHead = .east
```

directionToHead类型已知的情况下，设置值得时候可以省略类型。这使得使用显式枚举类型值时代码可读性更高。

## 用Switch语句匹配枚举的值

你可以用`switch`语句匹配每一个枚举值：
```swift
directionToHead = .south
switch directionToHead {
  case .north:
      print("Lots of planets have a north")
  case .south:
      print("Watch out for penguins")
  case .east:
      print("Where the sun rises")
  case .west:
      print("Where the skies are blue")
}
// Prints "Watch out for penguins"
```

你可以这样读上面的代码：
“考量directionToHead的值，如果等于.north，则打印"Lots of planets have a north"...”。
等等。

就像[控制流](Control_Flow.md)中提到的，当考量枚举的值时，`switch`语句必须彻底。如果`.west`被省略，则代码无法通过编译，因为它没有考虑到`CompassPoint`的所有情况。要求彻底确保枚举不会被意外疏漏。

当不需要考量枚举的每一种情况是，用`default`关键字代替没有没提到的：
```swift
let somePlanet = Planet.earth
switch somePlanet {
case .earth:
    print("Mostly harmless")
default:
    print("Not a safe place for humans")
}
// Prints "Mostly harmless"
```

## 遍历枚举

对于一些枚举，取得一个所有情况的集合比较有用。你可以通过将`:CaseIterable`添加在枚举名后面类实现。Swift将枚举的所有值的集合公开为枚举类型的`allCases`属性。
```swift
enum Beverage: CaseIterable {
    case coffee, tea, juice
}
let numberOfChoices = Beverage.allCases.count
print("\(numberOfChoices) beverages available")
// Prints "3 beverages available"
```
在上面的例子中，使用`Beverage.allCases`访问枚举`Beverage`所有值得集合。你可以像使用其他集合类型一样使用`allCases`——集合中的元素是枚举类型的实例，在本例中它们是`Beverage`值。例子中，统计所有情况的个数，下面用`for`循环遍历所有情况：
```swift
for beverage in Beverage.allCases {
  print(beverage)
}
// coffee
// tea
// juice
```

上面用到了让枚举实现`CaseIterable`协议的语法。关于协议，请看[协议](Protocols.md)。

## 关联值

上面的例子展示了枚举定义了一个代表自身的值。你可以设置一个常量或变量给`Planet.earth`，然后进行检查。然后，有时候与枚举值一起存储一个不同类型的值显得比较有用。这个额外信息被称为关联值，每次使用枚举的值，它都会不同。

你可以定义枚举储存任何给定类型的关联值，枚举的每个值得关联值根据需要可以不同。在其他语言中，与这种枚举类似的类型被称为区别联合或标记联合。

例如，假设库存跟踪相同需要通过两种不同类型的条形码来跟踪产品。一些产品用UPC格式的条形码标记，会用到数字1-9。每个条形码有一个系统数字，后面跟5个制造商数字和5个产品数字。然后是检验码以验证条形码是否被正确扫描：
![](https://docs.swift.org/swift-book/_images/barcode_UPC_2x.png)
其余的产品使用二维码，可以用ISO-8859-1字符，最长长度为2953个字符。
![](https://docs.swift.org/swift-book/_images/barcode_QR_2x.png)
库存跟踪系统可以方便地将条形码存储为四个整数的元组，将二维码存储为一个字符串。

在Swift中，定义任意类型的产品码的枚举可以是以下形式：
```swift
enum Barcode {
  case upc(Int, Int, Int, Int)
  case qrCode(String)
}
```
可以读作：
“定义一个名为`Barcode`的枚举类型，可以接受一个关联了(`Int`,`Int`,`Int`,`Int`)的`upc`，或一个关联了`String`类型的`qrCode`。”

这个定义不提供任何实际的整数或字符串值，只是定义了枚举Barcode的`.upc`和`.arCode`可以储存的变量或常量的类型。

你可以这样创建枚举实例：
```swift
var productBarcode = Barcode.upc(8, 85909, 51226, 3)
```
这个例子中，创建一个名为`productBarcode`的变量，指定其值是关联了(`8`, `85909`, `51226`, `3`)的`Barcode.upc`。

你可以指定不同类型的产品码：
```swift
productBarcode = .qrCode("ABCDEFGHIJKLMNOP")
```
这时，原来的`Barcode.upc`和与其关联的整数值被替换为`Barcode.qrCode`和与其关联的字符串值。`Barcode`类型的常量或变量可以储存`.upc`或`.qrCode`（及其关联值），但同时只能储存其中一种。

你可以用switch语句检查产品码的类型，与[用Switch语句匹配枚举的值]()中描述地相似。这是，关联值被抽出称为switch语句的一部分。你可以以变量（用`var`）或常量（用`let`）的形式抽出关联值，然后在switch的分支中使用。
```swift
switch productBarcode {
  case .upc(let numberSystem, let manufacturer, let product, let check):
    print("UPC: \(numberSystem), \(manufacturer), \(product), \(check).")
  case .qrCode(let productCode):
    print("QR code: \(productCode).")
}
```
如果所有的关联值都以常量或变量的形式被抽出，则你可以在分支的最前面使用`var`或`let`：
```swift
switch productBarcode {
case let .upc(numberSystem, manufacturer, product, check):
    print("UPC : \(numberSystem), \(manufacturer), \(product), \(check).")
case let .qrCode(productCode):
    print("QR code: \(productCode).")
}
// Prints "QR code: ABCDEFGHIJKLMNOP."
```

## 原始值

关于关联值的条形码示例，显示枚举如何声明储存不同的值。作为关联值的替代，枚举可以预先填充默认值（称为原始值），它们都是想同类型的。

下面是一个与枚举值一起存储ASCII原始值的例子：
```swift
enum ASCIIControlCharacter: Character {
  case tab = "\t"
  case lineFeed = "\n"
  case carriageReturn = "\r"
}
```
在这里一个原始值为字符的枚举被定义为`ASCIIControlCharacter`，并被设置了一些常用的ASCII控制符。字符值在[字符串和字符](Strings_and_Characters.md)中有详述。

原始值可以是字符串，字符或任何整型和浮点型。在枚举的声明中，每个原始值必须唯一。

> 注意：原始值与关联值不同。像上面的例子一样，首次定义枚举的时候原始值设置成预设值。枚举中每个值的原始值是相同的。关联值在你用枚举创建新的常量或变量的时候被设定，并且每次这样做的时候，关联值可以不同。

#### 隐式指定原始值

当使用储存整数或字符串原始值的枚举时，你不必为每一个值指定原始值。当你不指定原始值时，Swift会自动帮你指定。

例如，使用整型为原始值时，隐式的每个值得原始值比前一个增加1。如果没有赋值，则为0。

下面的例子是改版后的`Planet`枚举，以整型为原始值依次表示太阳系的行星：
```swift
enum Planet: Int {
case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune
}
```

上例中，`Planet.mercury`有一个显式的原始值`1`，`Planet.venus`有一个隐式的原始值`2`，等等。

当字符串被当做原始值时，隐式的原始值就是值的名称。

下面是改良版的`CompassPoint`枚举，字符串值表示各自的方向名：
```swift
enum CompassPoint: String {
  case north, south, east, west
}
```
上例中，`CompassPoint.south`有一个隐式的原始值“south”。

你可以用`rawValue`属性访问枚举的原始值。
```swift
let earthsOrder = Planet.earth.rawValue
// earthsOrder is 3

let sunsetDirection = CompassPoint.west.rawValue
// sunsetDirection is "west"
```

#### 从原始值初始化

如果你定义了一个原始值类型的枚举，则枚举自动拥有一个接受原始值类型参数（该参数名为`rawValue`）的初始化方法，该方法返回枚举实例或`nil`。你可以用这个初始化方法穿件枚举的实例。

例：用原始值`7`标记`Uranus`:
```swift
let passiblePlanet = Planet(rawValue: 7)
```

但是，不是所有整数型都能找到匹配。介于此，原始值初始化方法总是返回一个可选的枚举值。上例中，`possiblePlanet`是`Planet?`类型，或“可选的`Planet`”。

> 注意：原始值初始化方法是一个可能会失败的初始化方法，因为不是每个原始值类型的值都能成功创建枚举实例。关于初始化方法，参见[可失败初始化方法]()。

如果你尝试查找第11位行星，可选`Planet`类型通过原始值初始化方法将会返回`nil`：
```swift
let positionToFind = 11
if let somePlanet = Planet(rawValue: positionToFind) {
  switch somePlanet {
    case .earth:
      print("Mostly harmless")
    default:
      print("Not a safe place for humans")
  }
} else {
  print("There isn't a planet at position \(positionToFind)")
}
// Prints "There isn't a planet at position 11"
```

这个例子中，用可选值绑定的方法试图用原始值11访问`Planet`。条件语句`if let somePlanet = Planet(rawValue: 11)`创建一个可选类型的`Planet`，用可选Planet给`somePlanet`赋值，如果能获取值的话。这种情况下，无法用11获取Planet，所以`else`分支被执行。

## 递归枚举

递归枚举是，一个拥有一个或多个以其他枚举实例作为关联值的枚举。在枚举分支前面表上`indirect`来表明该枚举是递归枚举。这告诉编译器需要插入必要的间接层。

例如，下面是一个保存算数运算的枚举：
```swift
enum ArithmeticExpression {
  case number(Int)
  indirect case addition(ArithmeticExpression, ArithmeticExpression)
  indirect case multiplication(ArithmeticExpression, ArithmeticExpression)
}
```

你也可以在枚举最开头写上`indirect`关键字：
```swift
indirect enum ArithmeticExpression {
    case number(Int)
    case addition(ArithmeticExpression, ArithmeticExpression)
    case multiplication(ArithmeticExpression, ArithmeticExpression)
}
```

这个枚举可以储存三类数学运算：一个纯数字，加法，乘法。`addition`和`multiplication`分支的关联值也是数学运算——这些关联值可以嵌套枚举。如，算式`(5 + 4) * 2`在乘号的左边和右边均有一个数字。因为数据被嵌套，用来存储数据的枚举也需要支持嵌套——这意味着枚举需要递归。下面的代码显示如何用递归枚举`ArithmeticExpression`创建算式`(5 + 4) * 2`：
```swift
let five = ArithmeticExpression.number(5)
let four = ArithmeticExpression.number(4)
let sum = ArithmeticExpression.addition(five, four)
let product = ArithmeticExpression.multiplication(sum, ArithmeticExpression.number(2))
```
一个处理递归数据最直接的方式就是递归函数。例如，下面的函数就用来处理算式：
```swift
func evaluate(_ expression: ArithmeticExpression) -> Int {
  switch expression {
    case let .number(value):
      return value
    case let .addition(left, right):
      return evaluate(left) + evaluate(right)
    case let .multiplication(left, right):
      return evaluate(left) * evaluate(right)
  }
}

print(evaluate(product))
// Prints "18"
```
这个函数通过返回关联值来计算数字。通过分别计算算式的左边和右边，然后将它们相加或相乘。

[< 闭包](Closures.md) || [结构体和类 >](Structures_and_Classes.md)
