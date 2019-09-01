# 协议

*协议* 定义了适合特定任何或功能的方法，属性和其他要求的蓝图。协议可以被类，结构体或枚举采用以提供这些要求的实际实现。任何满足了协议要求的类型被认为*实现*了该协议。

实现了协议的类型必须实现指定的要求之外，还可以扩展协议以提供这些要求的实现，或实现额外的实现了协议的类型能够使用的功能。

## 协议语法

定义协议的语法与定义类，结构体和枚举的非常语法相似：
```swift
protocol SomeProtocol {
    // protocol definition goes here
}
```

自定义类型通过把协议名放在类型名后面，用冒号隔开，表明它们满足了特定协议。多个协议可以列表，用逗号隔开：
```swift
struct SomeStructure: FirstProtocol, AnotherProtocol {
    // structure definition goes here
}
```

如果一个类有父类，将父类名称列所有实现了的协议名前面，后面跟上逗号：
```swift
class SomeClass: SomeSuperclass, FirstProtocol, AnotherProtocol {
    // class definition goes here
}
```

## 属性要求

一个协议可以要求实现了该协议的类型提供一个特定名称和类型的实例属性或类型属性。协议不指定属性是否是储存属性或计算属性——它只指定需要的属性名称和类型。协议也指定了每个属性是否必须是*可读*(`gettable`)或*可读可写*(`gettable and settables`)。

如果一个协议要求一个属性*可读可写*(`gettable and settables`)，这该属性要求不能被一个常量储存属性或一个只读计算属性所满足。如果协议只要求一个属性*可读*(`gettable`)，则该要求可以被任何种类的属性所满足，且如果对于你自己的代码，同时可写(`settable`)比较有用的话，也是有效的。

属性要求经常被声明为变量属性，加上前缀`var`关键字。可获取和可设置的属性通过在类型声明后面写上`{ get set }`来表明，可获取属性用`{ get }`表明。
```swift
protocol SomeProtocol {
    var mustBeSettable: Int { get  set }
    var doesNotNeedToBeSettable: Int { get }
}
```

当在协议中定义类型属性要求时，在前面加上`static`关键字。即使类型属性要求被类实现时可以用前缀`class`或`static`，这个规则也适用：
```swift
protocol AnotherProtocol {
    static var someTypeProperty: Int { get set }
}
```

下面的例子，一个协议只有一个实例属性要求：
```swift
protocol FullyNamed {
    var fullName: String { get }
}
```

协议`FullyNamed`要求实现协议的类型提供一个完全限定的名字。协议并没有指定任何其他与类型相关的东西——它只指定了类型必须可以为自己提供一个全名。协议表明任何实现了`FullyNamed`的类型必须有一个名为`fullName`的可获取实例属性，该属性类型为`String`。
```swift
struct Person: FullyNamed {
    var fullName: String
}
let john = Person(fullName: "John Appleseed")
// john.fullName is "John Appleseed"
```

这个例子定义了一个结构体`Person`，代表特定名称的人。它声明实现`FullyNamed`协议作为定义第一行的一部分。

`Person`的每个实例都有一个名为`fullName`的储存属性，类型为`String`。这符合`FullyNamed`可以的唯一要求，且意味着`Person`正确地遵从协议。（如果协议要求不被满足，Swift会在编译时报错。）

下面是更复杂的类，它实现并遵从`FullyNamed`协议：
```swift
class Starship: FullyNamed {
    var prefix: String?
    var name: String
    init(name: String, prefix: String? = nil) {
        self.name = name
        self.prefix = prefix
    }
    var fullName: String {
        return (prefix != nil ? prefix! + " " : "") + name
    }
}

var ncc1701 = Starship(name: "Enterprise", prefix: "USS")
// ncc1701.fullName is "USS Enterprise"
```

上面的类以计算只读属性的形式为飞船实现了`fullName`属性要求。每个`Starship`类的实例都强制保存`name`和一个可选的`prefix`。如果`prefix`有值，`fullName`属性会使用它，并把它作为创建飞船全名的前缀。

## 方法要求

协议可以要求实现协议的类型实现指定的实例方法和类型方法。这些方法以与编写普通实例方法和类型方法完全一致的方式被写成协议定义的一部分，只是不需要大括号和方法体。允许使用可变参数，遵从与编写普通方法相同的规则。但是，不能在协议的定义中为方法参数指定默认值。

与类属性要求一样，在协议中定义类方法要求时，加上`static`前缀。即便是当被类实现时类型方法要求被加上`class`或`static`前缀，也适用：
```swift
protocol SomeProtocol {
    static func someTypeMethod()
}
```

下面的例子定义了一个只有一个实例方法要求的协议：
```swift
protocol RandomNumberGenerator {
    func random() -> Double
}
```

协议`RandomNumberGenerator`，要求任何实现了该协议的类型有一个实例方法`random`，当被调用时该方法返回一个`Double`值。即使没有被指定为协议的一部分，假设这个值是一个`0.0`从`1.0`(不包括`1.0`)到的数。

关于每个随机数是怎样生成的，协议`RandomNumberGenerator`不做任何假设——它只是简单地要求生成器提供一个生成随机数的标准方法。

下面是一个满足并实现`RandomNumberGenerator`协议的类的实现。这个类实现了一个被称为*线性同余生成器([linear congruential generator](https://en.wikipedia.org/wiki/Linear_congruential_generator))* 的伪随机数生成算法：
```swift
class LinearCongruentialGenerator: RandomNumberGenerator {
    var lastRandom = 42.0
    let m = 139968.0
    let a = 3877.0
    let c = 29573.0
    func random() -> Double {
        lastRandom = ((lastRandom * a + c).truncatingRemainder(invidingBy: m))
        return lastRandom / m
    }
}
let generator = LinearCongruentialGenerator()
print("Here's a random number: \(generator.random())")
// Prints "Here's a random number: 0.3746499199817101"
print("And another one: \(generator.random())")
// Prints "And another one: 0.729023776863283"
```

## 可变方法要求

有时候需要一个方法能修改其实例。对于值类型的实例方法(也就是，结构体和枚举)，在方法的`func`关键字前面写上`mutating`关键字，来允许该方法修改其所属的实例和该实例的任何属性。这个操作在[在实例方法中修改值类型](Methods.md#在实例方法中修改值类型)中有叙述。

如果定义一个会改变实现了协议的类型的实例的协议实例方法要求，在协议的定义中用`mutating`关键字标记该方法。这能使结构体和枚举实现协议并满足该方法要求。

> 注意：
如果将协议实例方法要求标记为`mutating`，当为一个类实现该方法时，不需要写上`mutating`关键字。只对结构体和枚举使用`mutating`。

下面的例子定义了一个协议`Togglable`，该协议定义了一个实例方法要求`toggle`。顾名思义，`toggle()`方法旨在触发或反转实现此协议类型的状态，一般是通过修改该类型的一个属性。

在协议的定义中，方法`toggle()`被`mutating`关键字标记，表明当被调用时，该方法会改变实现了该协议实例的状态：
```swift
protocol Togglable {
    mutating func toggle()
}
```

如果为结构体或枚举实现`Togglable`协议，则结构体或枚举可以通过提供一个被标记为`mutating`的`toggle()`方法实现来实现协议。

下面的方法定义了一个枚举`OnOffSwitch`。这个枚举能触发两个状态，用枚举分支`on`和`off`表示。枚举的`toggle`实现被标记为`mutating`，以满足`Togglable`协议要求：
```swift
enum OnOffSwitch: Togglable {
    case off, on
    mutating func toggle() {
        switch self {
        case .off:
          self = .on
        case .on:
          self = .off
        }
    }
}
var lightSwitch = OnOffSwitch.off
lightSwitch.toggle()
// lightSwitch is now equal to .on
```

## 初始化方法要求

协议可以要求实现了协议的类型实现指定的初始化方法。在协议定义中用与定义一般初始化方法相同的方式定义这些初始化方法，但是没有大括号或初始化方法体：
```swift
protocol SomeProtocol {
    init(somePatameter: Int)
}
```

### 协议初始化方法要求的类实现

可以在实现了协议的类中以指定初始化方法或便捷初始化方法的方式实现协议初始化方法要求。在这两种情况下，必须`required`修饰符标记初始化方法的实现：
```swift
class SomeClass: SomeProtocol {
    required init(somePatameter: nil) {
        // initializer implementation goes here
    }
}
```

使用`required`修饰符确保在实现了协议的类的所有子类中提供显式的或继承的初始化方法要求的实现，这样一来它们也符合协议。

关于必须初始化方法，参见[必须初始化方法](Initialization.md#必须初始化方法)。

> 注意：
在被`final`修饰符标记的类中，不需要用`required`标记协议初始化方法的实现，因为最终类不能被继承。关于最终类，参见[防止重写](Inheritance.md#防止重写)。



## 检查协议一致性


## 用扩展添加协议











[< 扩展](Extensions.md) || [泛型 >](Generics.md)
