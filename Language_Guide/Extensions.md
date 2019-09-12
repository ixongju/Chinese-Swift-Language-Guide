# 扩展

*扩展* 给现有的类，结构体，枚举或协议类型添加新功能。包括为无法访问源码的类型添加扩展（被称为*逆向建模*）。扩展跟Objective-C中的类别(`category`)类似。（与Objective-C的类别不同的是，Swift的扩展没有名称。）

Swift中的扩展可以：
* 添加计算实例属性和计算类属性
* 定义实例方法和类型方法
* 定义下标
* 定义和使用嵌套类型
* 使现有的类型实现一个协议

在Swift中，你甚至可以扩展一个协议，以提供协议规定的实现，或添加遵守了协议的类型能使用额外功能。更多详细内容，参见[协议扩展](Protocols.md#协议扩展)。

> 注意：
扩展可以为一个类型添加新功能，但是不能重写现有的功能。

## 扩展语法

用`extension`关键字声明扩展：
```swift
extension SomeType {
  // new functionality to add to SomeType goes here
}
```

扩展可以让一个现有类型实现一个或多个协议。要遵循协议，像为类或结构体编写协议一样为扩展编写协议：
```swift
extension SomeType: SomeProtocol, AnotherProtocol {
  // implementation of protocol requirements goes here
}
```

用这种方式遵循协议在[用扩展添加协议一致性](Protocols.md#用扩展添加协议一致性)中有详述。

扩展可被用于扩展现有的泛型类型，如[扩展一个泛型类型](Generics.md#扩展一个泛型类型)中所述。也可以扩展一个泛型类型类型以有条件地添加功能，如[扩展泛型Where子句](Generics.md#扩展泛型Where子句)中所述。

> 注意：
如果定义一个扩展为现有类型添加新功能，该功能在该类型的所有实例中都可用，即使这些实例在定义扩展之前已经被创建。

## 计算属性

扩展可以为现有的类型添加计算实例属性和计算类型属性。这个例子为Swift内建类型`Double`添加五个计算属性，为使用距离单位提供支持：
```swift
extension Double {
  var km: Double { return self * 1_000.0 }
  var m: Double { return self }
  var cm: Double { return self / 100.0 }
  var mm: Double { return self / 1_000.0 }
  var ft: Double { return self / 3.28084 }
}
let oneInch = 25.4.mm
print("One inch is \(oneInch) meters")
// Prints "One inch is 0.0254 meters"
let threeFeet = 3.ft
print("Three feet is \(threeFeet) meters")
// Prints "Three feet is 0.914399970739201 meters"
```

这些计算属性表明一个`Double`值可以被用作某个长度单位。即使它们被实现为计算属性，这些属性名称可被点语法用在浮点型字面量上，作为把字面值转换成距离的一种方式。

在这个例子中，`Double`值`1.0`被认为代表“一米”。这也就是为什么`m`计算属性直接返回自身——表明`1.m`被认为是`Double`值`1.0`。

为了表示以米为精度其他单位需要一些转化。一千米等于1000米，所以`km`计算属性将值乘以`1_000.00`转换为用米描述的值。类似地，一米有3.28084英寸，所以`ft`计算属性除以`Double`值`3.28084`，将值从英寸转换为米。

这些属性都是只读计算属性，为简便起见，所省略书写`get`关键字。返回值是`Double`类型，可被用于任何`Double`可被接受的数学计算中：
```swift
let aMarathon = 42.km + 195.m
print("A marathon is \(aMarathon) meters long")
// A marathon is 42195.0 meters long
```

> 注意：
扩展可以添加新计算属性，但是不能添加储存属性，也不能为现有的属性添加属性观察者。

## 初始化方法

扩展可以给现有类型添加新初始化方法。这能让你扩展其他类型，让其能够接受你自定义类型作为初始化参数，或添加该类型原始实现中没有的额外的初始化选项。

扩展可以给类添加便捷初始化方法。但是不能给类添加新指定初始化方法或反初始化方法。指定初始化方法和反初始化方法必须被原始类的实现提供。

如果使用扩展给一个为所有储存属性提供默认值，且没有自定义初始化方法的值类型添加初始化方法，你可以在扩展初始化方法中调用该类型的默认初始化方法和成员初始化方法。如果你已经在值类型的原始实现中编写了初始化方法，则不能这么做，如[值类型初始化方法委托](Initialization.md#值类型初始化方法委托)中所述。

如果用扩展给另一个模块中的结构体添加初始化方法，则新初始化方法在调用模块中定义的初始化方法之前不能访问`self`。

下面的例子定义了一个结构体`Rect`来表示一个几何矩形。例中也定义了两个辅助结构体`Size`和`Point`，这两个结构体都为它们的属性提供了默认值`0.0`：
```swift
struct Size {
    var width = 0.0, height = 0.0
}
struct Point {
    var x = 0.0, y = 0.0
}
struct Rect {
    var origin = Point()
    var size = Size()
}
```

因为结构体`Rect`为属性提供了默认值，它自动拥有一个默认初始化方法和一个成员初始化方法，如[默认初始化方法](Initialization.md#默认初始化方法)中所示。这些初始化方法可被用于创建新`Rect`实例：
```swift
let defaultRect = Rect()
let memberwiseRect = Rect(origin: Point(x: 2.0, y: 2.0), size: Size(width: 5.0, height: 5.0))
```

你可以扩展`Rect`结构体添加另一个接受指定中心点和大小的初始化方法：
```swift
extension Rect {
  init(center: Point, size: Size) {
    let originX = center.x - (size.width / 2)
    let originY = center.y - (size.height / 2)
    self.init(origin: Point(x: originX, y: originY), size: size)
  }
}
```

新初始化器先根据提供的`center`点和`size`值计算相应的原点。然后调用结构体的自动成员初始化器`init(origin:size:)`，将原点和大小值存放到相应的属性中：
```swift
let centerRect = Rect(center: Point(x: 4.0, y: 4.0), size: Size(width: 3.0, height: 3.0))
// centerRect's origin is (2.5, 2.5) and its size is (3.0, 3.0)
```

> 注意：
如果用扩展提供一个新初始化方法，你仍然需要负责确保每个实例在初始化方法执行完毕之后都被完全初始化。

## 方法

扩展可以为现有类型添加新实例方法。下面的例子为`Int`类型添加新实例方法`repetitions`：
```swift
extension Int {
  func repetitions(task: () -> Void) {
    for _ in 0..<self {
      task()
    }
  }
}
```

`repetitions(task:)`方法接受单个`() -> Void`类型的参数，该参数是一个无参数无返回值的函数。

定义完扩展后，你可以在任何整型值上调用`repetitions(task:)`方法以执行一个任务该整数次：
```swift
3.repetitions {
  print("Hello!")
}
// Hello!
// Hello!
// Hello!
```

## 可变实例方法

用扩展添加的实例方法也可以修改（或改变）实例本身。结构体和枚举修改`self`或自身属性的方法必须标记为`mutating`，跟原始实现中的可变方法一样。

下面的例子为Swift的`Int`类型添加可变方法`square`，该方法将计算原始值的平方：
```swift
extension Int {
  mutating func square {
    self = self * self
  }
}
var someInt = 3
someInt.square()
// someInt is now 9
```

## 下标

扩展可以为现有类型添加下标。这个例子为Swift的`Int`类型添加整型下标。下标`[n]`返回从右开始十进制数`n`所表示的位置的数：
* `123456789[0]`返回`9`
* `123456789[1]`返回`8`

...等等：
```swift
extension Int {
  subscript(digitIndex: Int) -> Index {
    var decimalBase = 1
    for _ in 0..<digitIndex {
      decimalBase *= 10
    }
    return (self / decimalBase) % 10
  }
}
746381295[0]
// returns 5
746381295[1]
// returns 9
746381295[2]
// returns 2
746381295[8]
// returns 7
```

如果`Int`值对于查询的索引没有足够的位数，下标会返回`0`，就像在数字的左边补上零：
```swift
746381295[9]
// returns 0, as if you had requested:
0746381295[9]
```

## 嵌套类型

扩展可以给现有的类，结构体和枚举添加嵌套类型：
```swift
extension Int {
  enum Kind {
    case negative, zero, positive
  }
  var kind: Kind {
    switch self {
    case 0:
        return .zero
    case let x where x > 0:
        return .positive
    default:
        return .negative
    }
  }
}
```

这个例子为`Int`添加了嵌套枚举。这个枚举`Kind`表示特定整数的类型。具体地说，它表示数字是否是负数，0或正数。

这个例子也为`Int`添加了新计算实例属性`kind`，该属性为正数返回合适的`Kind`枚举分支。

嵌套枚举可被用于任何`Int`值：
```swift
func printIntegerKinds(_ numbers: [Int]) {
  for number in numbers {
    switch number.kind {
    case .negative:
      print("- ", terminator: "")
    case .zero:
      print("0 ", terminator: "")
    case .positive:
      print("+ ", terminator: "")
    }
  }
  print("")
}
printIntegerKinds([3, 19, -27, 0, -6, 0, 7])
```

函数`printIntegerKinds(_:)`，接受`Int`值数组，然后按顺序遍历这些值。对于数组中的每个值，函数查看该整数的计算属性`kind`，然后打印相应的描述。

> 注意：
已知`number.kind`的类型为`Int.Kind`。因此，在`switch`语句中所有的`Int.Kind`分支可以使用简写，如`.negative`，而不是`Int.Kind.negative`。

[< 嵌套类型](Nested_Types.md) || [协议 >](Protocols.md)
