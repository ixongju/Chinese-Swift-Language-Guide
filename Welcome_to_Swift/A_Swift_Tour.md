## 快速开始

按照传统，第一个程序是打印一个“Hello, world!”。在Swift中，只需要一行代码：
```swift
print("Hello, world!")
// Prints "Hello, world!"
```
如果你用过`C`或`Objective-C`，这个语法对你应该很熟悉。在Swift中，这一行代码是一个完整的程序。你不需要引入额外的输入/输出或处理字符串的库。在全局范围编写的代码会作为程序的入口，所以你不需要编写`main()`函数。也不需要在每个声明的末尾写上分号（;）。

本导览通过展示完成多种不同编程任务，来为你提供开始用Swift编写代码的足够信息。如果有不懂的地方，请不要担心，本指南后面会给出解释。

### 简单的值
用`let`创建常量，用`var`创建变量。在编译时不需要知道常量的值，但必须为常量指定一次值。这意味着你可以用常量定义指定一次多处使用的值。
```swift
var myVariable = 42
myVariable = 50
let myConstant = 42
```
一个常量或变量必须拥有与你所指定的值一致的类型。但，你不必每次都指定明确指定类型。创建常量或变量的时候顺便提供一个值，编译器会推导类型。在上面的例子中，编译器或推导出`myVariable`的类型是整数型，因为初始值`42`是一个整数。

如果初始值没有提供足够的信息（例如，没有设置初始值），则通过在变量后面加上类型以指定，类型与变量用冒号（:）隔开。
```swift
let implicitInteger = 70
let implicitDouble = 70.0
let explicitDouble: Double = 70
```

> 试一试：
> 创建一个常量，显式指定为`Float`类型，值设置为`4`。

一个类型的值不隐式转换为其他类型。如果你需要将一个值转换为另一个类型，显式地创建一个需要类型的实例。
```swift
let label = "The width is "
let width = 94
let widthLabel = label + String(width)
```
> 试一试：
> 去掉`String`转换器，看看会报什么错？

有一个简单方法在字符串中嵌入值：将值放在括号中，然后在括号前面加斜杠。例如：
```swift
let apples = 3
let oranges = 5
let appleSummary = "I have \(apples) apples."
let fruitSummary = "I have \(apples + oranges) pieces of fruit."
```

> 试一试：
> 使用`\()`在字符串中包含一个浮点计算，并在问候中包含人名。

用连续的三个引号（"""）创建占用多行的字符串。引号中字符串的缩进被忽视，缩进与末尾的引号保持一致。例如：
```swift
let quotation = """
I said "I have \(apples) apples."
And then I said "I have \(apples + oranges) pieces of fruit."
"""
```

用方括号（`[]`）创建数组或字典，用方括号括起索引或键的方式来访问元素。最后一个元素后面可以带上逗号（,）。
```swift
var shoppingList = ["catfish", "water", "tulips"]
shoppingList[1] = "bottle of water"

var occupations = [
    "Malcolm": "Captain",
    "Kaylee": "Mechanic",
]
occupations["Jayne"] = "Public Relations"
```

数组会随着元素的加入而自动增长。
```swift
shoppingList.append("blue paint")
print(shoppingList)
```

用初始化语法创建空数组或字典。
```swift
let emptyArray = [String]()
let emptyDictionary = [String: Float]()
```

如果类型信息可以被推断出来，你可以用[]创建空数组，用[:]创建空字典。例如，当你为变量赋新值或传递实参给函数。
```swift
shoppingList = []
occupations = [:]
```

### 控制流
用`if`和`switch`实现条件语句，使用`for-in`、`while`和`repeat-while`实现循环语句。条件或循环的括号是可选的。条件体和循环体的大括号（`{}`）是必须的。
```swift
let individualScores = [75, 43, 103, 87, 12]
var teamScore = 0
for score in individualScores {
  if score > 50 {
    teamScore += 3
  } else {
    teamScore += 1
  }
}
print(teamScore)
// Prints "11"
```
如果`if`语句中，条件语句必须是布尔语句，这意味着`if score { ... }`是错误的，并不会隐式地与`0`做比较。

你可以用`if`和`let`来处理可能缺失的值。（可能缺失的值）以可选值的方式呈现。可选值包含一个值或`nil`，表明该值可能缺失。在类型后面写上问号（
`?`）将值标明为可选值。
```swift
var optionalString: String? = "Hello"
print(optionalString == nil)
// Prints "false"

var optionalName: String? = "John Appleseed"
var greeting = "Hello!"
if let name = optionalName {
  greeting = "Hello, \(name)"
}
```

> 试一试：将`optionalName`改成`nil`，`greeting`将得到什么？添加一个`else`语句，当`optionalName`是`nil`的时候，为`greeting`设置不同的值。

如果可选值是`nil`，条件为`false`，大括号中的代码将被跳过。否则，将从可选值中解包，然后指定给`let`后面的常量，这样能使解包出来的值在代码块中能被访问。

另外一个处理可选值的方法是通过`??`操作符提供默认值。如果可选值缺失，就用默认值。
```swift
let nickName: String? = nil
let fullName: String = "John Appleseed"
let informalGreeting = "Hi \(nickName ?? fullName)"
```

Switches语句支持任何类型的数据和多种比较运算 - 不仅限于整数和等式比较。
```swift
let vegetable = "red pepper"
switch vegetable {
  case "celery":
    print("Add some raisins and make ants on a log.")
  case "cucumber", "watercress":
    print("That would make a good tea sandwish.")
  case let x where x.hasSuffix("pepper"):
    print("Is it a spicy \(x)?")
  default:
    print("Everything tastes good in soup.")
}
```
> 试一试：去掉`default`分支，看看报什么错？

注意如果在模式中用`let`如何将匹配的值赋值给常量。

执行完匹配的分支之后，程序退出switch语句。不继续执行下一个分支（`case`），所以没有必要在分支后面显式地加上`break`。

用`for-in`为每个键值对提供一对名称的方式遍历字典。字典是无序的集合，所以键值被随机遍历。
```swift
let interestingNumbers = [
    "Prime": [2, 3, 5, 7, 11, 13],
    "Fibonacci": [1, 1, 2, 3, 5, 8],
    "Square": [1, 4, 9, 16, 25],
]
var largest = 0
for (kind, numbers) in interestingNumbers {
    for number in numbers {
        if number > largest {
            largest = number
        }
    }
}
print(largest)
// Prints "25"
```
> 试一试：添加一个变量，追踪哪种数字最大，最大的数是什么。

用`while`语句重复执行满足条件才会停的代码块。循环条件可以在末尾，以确保循环至少运行一次。
```swift
var n = 2
while n < 100 {
    n *= 2
}
print(n)
// Prints "128"

var m = 2
repeat {
    m *= 2
} while m < 100
print(m)
// Prints "128"
```
可以使用`..<`操作符生成一系列索引来保持索引循环。
```swift
var total = 0
for i in 0..<4 {
  total += i
}
print(total)
// Prints "6"
```

用`..<`创建一个不包含上限的范围，用`...`创建一个包含上下限的范围。

### 函数和闭包
用`func`声明函数。用函数名后面跟上括号括起的参数来调用函数。用`->`将参数和函数的返回类型隔开。
```swift
func greet(person: String, day: String) -> String {
    return "Hello \(person), today is \(day)."
}
greet(person: "Bob", day: "Tuesday")
```

> 试一试
> 去掉`day`参数，添加一个关于午餐问候的参数。

默认情况下，函数用形参名作为实参的标志。可以在形参前面添加实参标签，或用下划线（`_`）标明不用实参标签。
```swift
func greet(_ person: String, on day: String) -> String {
    return "Hello \(person), today is \(day)."
}
greet("John", on: "Wednesday")
```

用复合值更新元祖。例如，用函数返回多个值。元组的元素可以通过名称或数字访问。
```swift
func calculateStatistics(scores: [Int]) -> (min: Int, max: Int, sum: Int) {
    var min = scores[0]
    var max = scores[0]
    var sum = 0

    for score in scores {
        if score > max {
            max = score
        } else if score < min {
            min = score
        }
        sum += score
    }

    return (min, max, sum)
}
let statistics = calculateStatistics(scores: [5, 3, 100, 3, 9])
print(statistics.sum)
// Prints "120"
print(statistics.2)
// Prints "120"
```

函数可以嵌套。嵌套函数可以访问外部函数中声明的变量。你可以用嵌套函数管理（外部）函数中长或复杂的代码。
```swift
func returnFifteen() -> Int {
  var y = 10
  func add() {
    y += 5
  }
  add()
  return y
}

returnFifteen()
```

函数是头等类型（first-class type)。这意味着函数可以作为另一个函数的返回值。
```swift
func makeIncrementer() -> ((Int) -> Int) {
    func addOne(number: Int) -> Int {
        return 1 + number
    }
    return addOne
}
var increment = makeIncrementer()
increment(7)
```

一个函数可以把另一个函数当作实参使用。
```swift
func hasAnyMatches(list: [Int], condition: (Int) -> Bool) -> Bool {
    for item in list {
        if condition(item) {
            return true
        }
    }
    return false
}
func lessThanTen(number: Int) -> Bool {
    return number < 10
}
var numbers = [20, 19, 7, 12]
hasAnyMatches(list: numbers, condition: lessThanTen)
```

实际上，函数是闭包的特例：一个可以稍后使用的代码块。闭包中的代码可以访问在闭包被创建的作用域中可用的变量和函数，即便闭包在不同的作用域被使用 --- 在使用嵌套函数的时候，你已经看到了这样的例子。你可以用大括号（`{}`）括起代码从而创建一个无名闭包。用`in`关键字将实参和返回值与函数体分开。
```swift
numbers.map({ (number: Int) -> Int in
   let result = 3 * number
   return result
})
```
> 试一试：重写闭包，当`result`是奇数时，返回`0`。

可以通过几个方式更简洁地编写闭包。当闭包的类型已知时，例如，`delegate`的回调。可以省略参数的类型、返回值或两者都省略。单个语句闭包隐式返回其唯一语句的值。
```swift
let mappedNumbers = numbers.map({ number in 3 * number})
print(mappedNumbers)
// Prints "[60, 57, 21, 36]"
```

你可以用数字来引用参数，这在短的闭包中很有用。作为函数最后一个参数的闭包，可以直接放在括号后面使用。当闭包是唯一的参数的时候，可以省略括号。
```swift
let sortedNumbers = numbers.sorted { $0 > $1 }
print(sortedNumbers)
// Prints "[20, 19, 12 ,7]"
```

### 对象和类

在`class`关键字后面加上类名的方式创建类。类的属性声明方式与变量和常量相同，只是它只存在于类的上下文中。同样，方法的声明跟函数的声明方式一致。
```swift
class Shape {
  var numberOfSides = 0
  func simpleDescription() -> String {
    return "A shape with \(numberOfSides) sides."
  }
}
```
> 试一试：用`let`添加一个常量属性，添加一个使用参数的方法。

用类名后面接括号的方式创建类的实例。用点号（`.`）访问实例的属性和方法。
```swift
var shape = Shape()
shape.numberOfSides = 7
var shapeDescription = shape.simpleDescription()
```

这个版本的`Shape`类缺了一个重要的东西：创建类实例的时候设置类的初始化方法。用`init`创建。
```swift
class NamesShape {
  var numberOfSides: Int = 0
  var name: String

  init(name: String) {
    self.name = name
  }

  func simpleDescription() -> String {
    return "A shape with \(numberOfSides) sides."
  }
}
```
注意`self`是如何被用于在初始化方法中区分属性`name`和实参`name`的。创建类的实例的时候，初始化方法的实参将像函数调用一样传递。每个属性都需要被赋值——在声明中（`numberOfSides`）或初始化方法中（`name`）。

如果像在对象被回收前执行一些清理工作，请使用`deinit`创建反初始化方法。

子类在类名后面加上父类名，用分号（`;`）隔开。类无需继承自标准根类，所以你可以根据需要省略父类名称。

子类中重写了的父类方法实现的方法，用`override`标记，如果意外重写了方法，而没有标记`override`，编译器将报错。编译器也会检测标记有`override`但实际上没有重写父类的方法。
```swift
class Square: NamedShape {
    var sideLength: Double

    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 4
    }

    func area() -> Double {
        return sideLength * sideLength
    }

    override func simpleDescription() -> String {
        return "A square with sides of length \(sideLength)."
    }
}
let test = Square(sideLength: 5.2, name: "my test square")
test.area()
test.simpleDescription()
```
> 试一试：创建一个以半径和名称为初始化参数，名为`Circle`的`NamedShape`的子类。实现`area()`和`simpleDescription()`方法。

除了简单属性的储存之外，属性也可以有`getter`和`setter`方法。
```swift
class EquilateralTriangle: NamedShape {
  var sideLength: Double = 0.0

  init(sideLength: Double, name: String) {
    self.sideLength = sideLength
    self.init(name: name)
    numberOfSides = 3
  }

  var perimeter: Double {
    get {
      return 3.0 * sideLength
    }
    set {
      sideLength = newValue / 3.0
    }
  }

  override func simpleDescription() -> String {
    return "An equilateral triangle with sides of length \(sideLength)."
  }
}
```
在`perimeter`的`setter`方法中，新的值有一个隐式的名称`newValue`。你可以在`set`后面加上括号在括号中提供显式名称。

注意`EquilateralTriangle`的初始化方法有三个不同步骤：
1. 给子类声明的属性赋值
2. 调用父类的初始化方法
3. 改变被父类定义的属性的值。任何利用方法，`getter`或`setter`的附加设置都可以在这里进行。

如果你不需要计算属性，但是仍然需要在设置属性值前后执行代码，用`willSet`和`didSet`。你提供的代码在初始化方法之外改变时会被执行。例如，下面的类确保三角形的边长与四边形的边长相等。
```swift
class TriangleAndSquare {
  var triangle: EquilateralTriangle {
    willSet {
      square.sideLength = newValue.sideLength
    }
  }
  var square: Square {
    willSet {
      triangle.sideLength = newValue.sideLength
    }
  }
  init(size: Double, name: String) {
    square = Square(sideLength: size, name: name)
    triangle = EquilateralTriangle(sideLength: size, name: name)
  }
}
var triangleAndSquare = TriangleAndSquare(size: 10, name: "another test shape")
print(triangleAndSquare.square.sideLength)
// Prints "10.0"
print(triangleAndSquare.triangle.sideLength)
// Prints "10.0"
triangleAndSquare.square = Square(sideLength: 50, name: "larger square")
print(triangleAndSquare.triangle.sideLength)
// Prints "50.0"
```

当使用可选值时，你可以在方法，属性或角标前写上`?`。如果问号之前的值是`nil`，则问好后面的一切会被忽略，整个表达式的值也将是`nil`。否则，可选值的值将被解包，问号之后的一些运算将用解包的值。上面两种情况下，整个表达式成为一个可选值。
```swift
let optionalSquare: Square? = Square(sideLength: 2.5, name: "optional square")
let sideLength = optionalSquare?.sideLength
```

### 枚举和结构体

















[< 版本兼容](Version_Compatibility.md) || [基础 >](../Language_Guide/Basic_Operators.md)
