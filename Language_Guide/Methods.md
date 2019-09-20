# 方法

*方法* 是与某一类型关联的函数。类，结构体和枚举都可以定义实例方法，实例方法为给定类型的实例封装任务或功能。类，结构体和枚举也可以定义类型方法，类型方法与类型关联。类型方法类似Objective-C中的类方法。

在Swift中结构体和枚举可以定义方法，这与C和Objective-C大不相同。在Objective-C中，类是唯一能定义方法的类型。在Swift中，你可以选择使用类，结构体或枚举，且仍然具有为类型定义方法的灵活性。

## 实例方法

*实例方法* 是属于某一特定类实例的函数。通过提供访问和修改实例属性的方法，或通过提供与实例目的相关的功能，来实现这些实例的功能。实例方法与函数语法相同，[函数](Functions.md)中有述。

将实例方法写在类型的花括号之内。实例方法能隐式访问所述类型的所有其他实例方法和属性。实例方法只能被其所述类型的某一实例调用。不能在没有实例的情况下调用。

下例中，定义一个简单的类`Counter`，可以用来统计动作发生的次数：
```swift
class Counter {
    var count = 0
    func increment() {
        count += 1
    }
    func increment(by amount: Int) {
        count += amount
    }
    func reset() {
        count = 0
    }
}
```

`Counter`类定义了三个实例方法：
* `increment()`将统计数据加1
* `increment(by: Int)`将统计数据增加指定整数
* `reset()`将统计数据重置为零

`Counter`类同时定义了一个变量属性，`count`，用来追踪当前统计值。

用点语句调用实例方法：
```swift
let counter = Counter()
// the initial counter value is 0
counter.increment()
// the counter's value is now 1
counter.increment(by: 5)
// the counter's value is now 6
counter.reset()
// the counter's value is now 0
```

函数参数可以包名称（在函数体中使用）和实参标签（调用函数时用），如[函数形参标签和实参名称](Functions.md#函数形参标签和实参名称)中所示。方法参数也如此，因为方法就是与某一类型关联的函数。

### `self`属性

每个类型的实例有一个隐藏属性`self`，`self`完全等同于实例本身。在实例方法中用`self`属性指代当前实例。

上例中`increment()`方法可以写成这样：
```swift
func increment() {
  self.count += 1
}
```

在实践中，你不需要经常使用`self`。任何时候，使用一个已知属性或方法名，如果不显式书写`self`，Swift会猜测引用了当前实例的属性或方法。这种猜测在`Counter`三个实例方法中使用`count`中得到演示。

这个规则的主要例外，是实例方法的形参名和实例的属性具有相同的名称。这种情况下，参数名优先，所以需要一个更合理的方式引用属性。这时，可以用`self`属性来区分参数名和属性名。

这里，`self`区分一个名为`x`方法参数和名为`x`的实例属性：
```swift
struct Point {
    var x = 0.0, y = 0.0
    func isToTheRightOf(x: Double) -> Bool {
        return self.x = x
    }
}
let somePoint = Point(x: 4.0, y: 5.0)
if somePoint.isToTheRightOf(x: 1.0) {
    print("This point is to the right of the line where x == 1.0")
}
// Prints "This point is to the right of the line where x == 1.0"
```

如果不使用`self`前缀，Swift会认为两个`x`都指代方法参数。

### 在实例方法中修改值类型

结构体和枚举是*值类型*。默认地，不能在实例方法中修改值类型的属性。

但是，如果你想在某一方法中修改结构体或枚举的属性，你可以选择在改变该方法的行为。然后该方法就可以在方法体中修改属性，方法结束，方法作出的改变都会写回到原始结构体中。方法也可以为隐式属性`self`赋值一个全新的实例，当方法结束时，新实例会替换已存在的实例。

可以在`func`关键字前面写上`mutating`关键字，来选择此行为：
```swift
struct Point {
    var x = 0.0, y = 0.0
    mutating func moveBy(x deltaX: Double, y deltaY: Double) {
        x += deltaX
        y += deltaY
    }
}
var somePoint = Point(x: 1.0, y: 1.0)
somePoint.moveBy(x: 2.0, y: 3.0)
print("The point is now at (\(somePoint.x), \(somePoint.y))")
// Prints "The point is now at (3.0, 4.0)"
```

上面的`Point`结构体定义了一个可变的方法`moveBy(x:y:)`，用来以特定的数移动`Point`实例，实际上，调用这个方法时修改了点。`mutating`关键字被添加在定义的前面以允许方法修改实例属性。

注意，不能用结构体类型的常量调用可变方法，因为即便属性是变量，也属性不能被改变，如[常量结构体实例的储存属性](Properties.md#常量结构体实例的存储属性)中所述：
```swift
let fixedPoint = Point(x: 3.0, y: 3.0)
fixedPoint.moveBy(x: 2.0, y: 3.0)
// this will report an error
```

## 在可变方法中给self赋值

可变方法可以为隐藏的`self`属性赋值全新的实例。上面例子中`Point`可以写成下面那样：
```swift
struct Point {
  var x = 0.0, y = 0.0
  mutating func moveBy(x deltaX: Double, y deltaY: Double) {
    self = Point(x: x + deltaX, y: y + deltaY)
  }
}
```

这个版本的可变方法`moveBy(x:y:)`创建了一个新结构体，`x`和`y`值被设置为新的位置。调用这个版本的结果与上一个版本的结构是完全相同的。

枚举的可变方法可以把隐藏的`self`设置为相同枚举中不同的分支：
```swift
enum TriStateSwitch {
    case off, low, high
    mutating func next() {
        switch self {
        case .off:
            self = .low
        case .low:
            self = .high
        case .high:
            self = .off
        }
    }
}
var ovenLight = TriStateSwitch.low
ovenLight.next()
// ovenLight is now equal to .high
ovenLight.next()
// ovenLight is now equal to .off
```

这个例子定义了一个三个状态的枚举。每次调用`next()`方法时，分支在三个状态（`off`,`low`和`high`）中循环。

## 类型方法

如上所述，实例方法是在某一类型实例上调用的方法。你也可以定义用类型本身调用的方法。这种方法被称为类型方法。在方法关键字`func`前面写上`static`关键字表明该方法为类型方法。类可以用`class`关键字代替，以允许子类能重写该方法的父类实现。

> 注意：
在Objective-C中，你只能为Objective-C类定义类型级别的方法。在Swift中，你可以为类，结构体和枚举定义类型级别的方法。每个类型方法被限定给其支持的类型。

跟实例方法一样，通过点语法调用类型方法。但是，在类型上调用类型方法，而不是该类型的实例上。下面是如何调用类`SomeClass`的类型方法：
```swift
class SomeClass {
  class func someTypeMethod() {
    // type method implementation goes here
  }
}
SomeClass.someTypeMethod()
```

在类型方法的方法体中，隐藏属性`self`指代类型本身，而不是该类型的实例。这意味着你可以用`self`区分类属性和类方法参数，即如同你用`self`区分实例属性和实例方法参数样。

更普遍的，你在类型方法体中使用的任何非标准方法名和属性名（译者：例如`SomeClass`的`someMethod`类型方法的标准方法名是`SomeClass.someMethod`，非标准方法名是`someMethod`），将指代类型级别的方法和属性。一个类型方法可以用方法名调用另一个类型方法，而不需要在类型名前添加前缀。类似地，结构体和枚举的类型方法，不需要类型名前缀，可以通过属性名访问类型属性。

下面的例子定义了一个结构体`LevelTracker`，用来追踪玩家在一个游戏关卡别或状态的进度。这是个单人游戏，但是可以在同一设备上存放多个玩家信息。

初次玩游戏时，所有的关卡都是锁定的（除了第一关）。每次玩家完成一个关卡，该关卡对该设备上的所有玩家开放。`LevelTracker`结构体用类型属性和类型方法跟踪哪一个游戏关卡被解锁。同时也跟踪每个单独玩家的当前关卡。
```swift
struct LevelTracker {
    static var highestUnlockedLevel = 1
    var currentLevel = 1

    static func unlock(_ level: Int) {
        if level > highestUnlockedLevel { highestUnlockedLevel = level }
    }

    static func isUnlocked(_ level: Int) -> Bool {
        return level <= highestUnlockedLevel
    }

    @discardableResult
    mutating func advance(to level: Int) -> Bool {
        if LevelTracker.isUnlocked(level) {
            currentLevel = level
            return true
        } else {
            return false
        }
    }
}
```

`LevelTracker`结构体记录任何玩家解锁的最高关卡。这个值被存放在类属性`highestUnlockedLevel`中。

`LevelTracker`也定义了两个类型方法使用`highestUnlockedLevel`属性。第一个是类型方法`unlock(_:)`，当新等级解锁时，该方法更新`highestUnlockedLevel`的值。第二个是便利类型方法`isUnlocked(_:)`，如果某一关卡已经解锁，它返回`true`。（注意，即使不写成`LevelTracker.highestUnlockedLevel`这个方法可以访问类型属性`highestUnlockedLevel`）。

除了类型属性和类型方法，`LevelTracker`跟踪一个单独玩家的游戏进度。用`currentLevel`实例方法记录当前玩家的关卡。

为帮助管理`currentLevel`属性，`LevelTracker`定义了一个实例方法`advance(to:)`。更新`currentLevel`之前，该方法检查请求的新等级是否解锁。`advance(to:)`方法返回一个布尔值表明是否能够设置`currentLevel`。因为调用`advance(to:)`方法时忽略返回值不一定是错误，所以函数被`@discardableResult`特性标记。更多关于特性，参见[特性](Language_Reference/Attributes.md)。

如下所示，`LevelTracker`结构体与`Player`类配合，跟踪与更新单个玩家的进度：
```swift
class Player {
    var tracker = LevelTracker()
    let playerName: String
    func complete(level: Int) {
        LevelTracker.unlock(level + 1)
        tracker.advance(to: level + 1)
    }
    init(name: String) {
        playerName = name
    }
}
```

`Player`类创建一个`LevelTracker`新实例用来追踪该玩家的进度。也提供一个`complete(level:)`方法，当任何时候玩家完成某一等级时就会被调用。这个方法为所有玩家解锁下一个等级，并更新玩家进度至下一个等级。（`advance(to:)`方法的布尔返回值被忽略，因为在上一行代码中，等级被`LevelTracker.unlock(_:)`解锁完毕。）

你可以用`Player`类为新玩家创建一个实例，看看当玩家完成等级1时会发生什么：
```swift
var player = Player(name: "Argyrios")
player.complete(level: 1)
print("highest unlock level is now \(LevelTracker.highestUnlockedLevel)")
// Prints "highest unlocked level is now 2"
```

如果你创建第二个玩家，该玩家试图移动到尚未没任何玩家解锁的等级，尝试设置玩家当前等级会失败：
```swift
player = Player(name: "Beto")
if player.tracker.advance(to: 6) {
    print("player is now on level 6")
} else {
    print("level 6 has not yet been unlocked")
}
// Prints "level 6 has not yet been unlocked"
```

[< 属性](Properties.md) || [下标 >](Subscripts.md)
