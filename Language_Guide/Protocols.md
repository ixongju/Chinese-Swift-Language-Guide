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

如果子类重写了父类的指定初始化方法，同时实现满足了协议初始化方法要求，用`required`和`override`修饰符标记初始化方法实现：
```swift
protocol SomeProtocol {
    init()
}
class SomeSuperClass {
    init() {
        // initializer implementation goes here
    }
}

class SomeSubClass: SomeSuperClass, SomeProtocol {
    // "required" from SomeProtocol conformance; "override" from SomeSuperClass
    required override init() {
        // initializer implementation goes here
    }
}
```

### 可失败初始化方法要求

协议可以为实现了协议的类型定义可失败初始化方法要求，如[可失败初始化方法](Initialization.md#可失败初始化方法)中所定义的。

一个可失败初始化方法要求可以被实现了协议的类型中的一个可失败初始化方法或一个非可失败初始化方法满足。一个非可失败初始化方法要求可以被一个非可失败初始化方法或一个隐式解包可失败初始化方法满足。

## 协议作为类型

实际上，协议本身不实现任何功能。尽管如此，你可以在代码中把协议当作一个完整的类型来使用。有时候把协议用作类型可以称为*存在类型*([existential type](https://en.wikipedia.org/wiki/Type_system#Existential_types))，这种叫法源于这样的描述：“这里有一个实现了协议的类型*T*”。

可以把协议用在其他类型允许的地方，包括：
* 作为函数，方法或初始化方法的参数类型或返回类型
* 作为常量，变量或属性的类型
* 作为数组，字典或其他容器中元素的类型

> 注意：
因为协议就是类型，所以用名称开头字母用大写（如`FullyNamed`和`RandomNumberGenerator`）以与Swift中其他类型（如`Int`，`String`和`Double`）相匹配。

下面是把协议用作类型的例子：
```swift
class Dice {
    let sides: Int
    let generator: RandomNumberGenerator
    init(sides: Int, generator: RandomNumberGenerator) {
        self.sides = sides
        self.generator = generator
    }
    func roll() -> Int {
        return Int(generator.random() * Double(sides)) + 1
    }
}
```

这个例子定义了一个类`Dice`，表示棋盘游戏中的一个n面的骰子。`Dice`实例有一个整数属性`sides`，代表骰子的面数，和一个属性`generator`，提供一个用于创建骰子结果的随机数。

属性`generator`的类型是`RandomNumberGenerator`。因此，你可以把该属性设置为遵守了`RandomNumberGenerator`协议的任何类型。对于赋值给这个属性的实例，没有任何其他的要求，只需要该实例遵守了`RandomNumberGenerator`协议。以你为它是`RandomNumberGenerator`类型，`Dice`类中的代码只需通过应用在实现了协议的生成器上的方法与`generator`交互。这意味着它不能使用任何定义在底层生成器中的其他方法或属性。尽管如此，你可以用从父类向下转换为子类的方法，从协议类型向下转换成一个底层类型，如[向下转换](Type_Casting.md#向下转换)中所述。

`Dice`也有一个初始化方法，用来设置初始状态。这个初始化方法有一个参数`generator`，其类型是`RandomNumberGenerator`。当初始化`Dice`实例时，你可以传入任何一个实现了该协议的类型的值给这个参数。

`Dice`提供一个实例方法`roll`，返回一个从1到骰子面数的整数。这个方法调用生成器的`random()`方法创建一个介于`0.0`到`1.0`的新随机数，然后用这个随机数创建正确范围内骰子的值。因为已知`generator`实现了`RandomNumberGenerator`协议，所以能确保`generator`有`random()`方法。

下面是如何用`Dice`类，结合`LinearCongruentialGenerator`实例作为随机数生成器，创建一个六面骰子：
```swift
var d6 = Dice(sides: 6, generator: LinearCongruentialGenerator())
for _ in 1...5 {
    print("Random dice roll is \(d6.roll())")
}
// Random dice roll is 3
// Random dice roll is 5
// Random dice roll is 4
// Random dice roll is 5
// Random dice roll is 4
```

## 委托

*委托* 是一种允许类或结构体将部分职责传递（或委托）给另一个类型的实例的设计模式。通过定义封装了委托责任的协议，确保实现了协议的类型（被称为委托）能提供被委托的功能，来实现这个设计模式。委托可被用于响应特定的动作，或从外部资源获取数据，而不需要知道该资源的底层类型。

下面的例子为基于骰子的棋盘游戏定义了两个协议：
```swift
protocol DiceGame {
    var dice: Dice { get }
    func play()
}
protocol DiceGameDelegate: AnyObject {
    func gameDidStart(_ game: DiceGame)
    func game(_ game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int)
    func gameDidEnd(_ game: DiceGame)
}
```

`DiceGame`协议可以被使用了骰子的任何游戏采用。为了防止强循环引用，委托被声明为弱引用，参见[类实例间的强循环引用](Automatic_Reference_Counting.md#类实例间的强循环引用)。将协议标记为类专用(class-only)，可以使本节稍后的`SnakesAndLadders`类声明其委托必须为弱引用。类专用(class-only)的协议在继承格式后面写上`AnyObject`，如[类专用协议](#类专用协议)中所述。

下面是[控制流](Control_Flow.md)章节中引入的*蛇与梯子*游戏的一个版本。这个版本适合用`Dice`实例作为摇骰子；采用`DiceGame`协议；并通知`DiceGameDelegate`游戏进度：
```swift
class SnakesAndLadders: DiceGame {
    let finalSquare = 25
    let dice = Dice(sides: 6, generator: LinearCongruentialGenerator())
    var square = 0
    var board: [Int]
    init() {
      board = Array(repeating: 0, count: finalSquare + 1)
      board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
      board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
    }
    weak var delegate: DiceGameDelegate?
    func play() {
        square = 0
        delegate?.gameDidStart(self)
        gameLoop: while square != finalSquare {
            let diceRoll = dice.roll()
            delegate?.game(self, didStartNewTurnWithDiceRoll: diceRoll)
            switch square + diceRoll {
                case finalSquare:
                  break gameLoop
                case let newSquare where newSquare > finalSquare:
                  continue gameLoop
                default:
                  square += diceRoll
                  square += board[square]
            }
        }
        delegate?.gameDidEnd(self)
    }
}
```

关于*蛇和梯子*游戏的描述，参见[Break](Control_Flow.md#Break)。

这个版本的游戏被包装成遵守了`DiceGame`协议的`SnakesAndLadders`类。为了实现协议它提供了一个可读属性`dice`和一个`play()`方法。（`dice`属性声明为常量属性，因为在初始化之后就不需要改变，且协议只要求其可读。）

在类初始化方法`init()`中设置*蛇和梯子*游戏棋盘。所有的游戏逻辑移到协议方法`play()`中，该方法利用协议属性`dice`提供骰子点数。

注意`delegate`属性被定义成可选`DiceGameDelegate`，因为委托对于玩游戏并不是必须的。由于是可选类型，`delegate`属性自动被设置成`nil`。之后，游戏的实例化者可以选择为该属性设置合适的委托。因为`DiceGameDelegate`协议是类专用，你可以将委托声明为`weak`以防止循环引用。

`DiceGameDelegate`提供三个方法跟踪游戏进度。这三个方法与`play()`方法中的游戏逻辑相协调，在游戏开始，新回合开始或游戏结束时会被调用。

因为`delegate`属性是*可选*`DiceGameDelegate`，每次`play()`调用委托的方法时，会使用可选链。如果`delegate`属性是空，这些委托方法会优雅地失败而不会报错。如果`delegate`属性是非空，委托方法被调用，将`SnakesAndLadders`实例当作参数传递。

下一个例子展示了一个类`DiceGameTracker`，该类遵守`DiceGameDelegate`协议：
```swift
class DiceGameTracker: DiceGameDelegate {
    var numberOfTurns = 0
    func gameDidStart(_ game: DiceGame) {
        numberOfTurns = 0
        if game is SnakesAndLadders {
            print("Started a new game of Snakes and Ladders")
        }
        print("The game is using a \(game.dice.sides)-sided dice")
    }
    func game(_ game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int) {
        numberOfTurns += 1
        print("Rolled a \(diceRoll)")
    }
    func gameDidEnd(_ game: DiceGame) {
      print("The game lasted for \(numberOfTurns) turns")
    }
}
```

`DiceGameTracker`实现了`DiceGameDelegate`所要求的三个方法。它用这些方法统计游戏回合次数。当游戏开始时，把`numberOfTurns`属性设置为零，新回合开始时增一，游戏结束时打印出回合总数。

上面`gameDidStart(_:)`的实现中使用`game`参数打印即将开始的游戏的介绍性信息。`game`属性类型是`DiceGame`，而不是`SnakesAndLadders`，所以`gameDidStart(_:)`仅可以访问和使用实现作为`DiceGame`协议一部分的方法和属性。然而，该方法依然可以用类型转换查询底层实例的类型。在这个例子中，检查`game`底层是否是`SnakesAndLadders`的一个实例，如果是就打印相应信息。

`gameDidStart(_:)`方法也访问了被传入的`game`参数的`dice`属性。因为已知`game`遵守了`DiceGame`协议，确保有`dice`属性，所以不管什么类型的游戏，`gameDidStart(_:)`方法都可以访问并且打印骰子的`sides`属性。

下面是`DiceGameTracker`的应用：
```swift
let tracker = DiceGameTracker()
let game = SnakesAndLadders()
game.delegate = tracker
game.play()
// Started a new game of Snakes and Ladders
// The game is using a 6-sided dice
// Rolled a 3
// Rolled a 5
// Rolled a 4
// Rolled a 5
// The game lasted for 4 turns
```

## 用扩展添加协议一致性

可以扩展一个现有类型遵循并实现一个新协议，即便你没有权限访问现有类型的源码。扩展可以给现有类型添加属性，方法和下标，因此可以添加可能的协议要求。更多关于扩展，参见[扩展](Extensions.md)。

> 注意：
当在扩展中给实例的类型添加了协议一致性时，类型的现有实例自动遵守和实现协议。

举个例子，协议`TextRepresentable`，可以被任何能被呈现为文本的类型实现。这可能是关于自身的描述，或当前状态的文本版本：
```swift
protocol TextRepresentable {
    var textualDescription: String { get }
}
```




## 类专用协议

## 检查协议一致性


### 用扩展声明遵守协议









[< 扩展](Extensions.md) || [泛型 >](Generics.md)
