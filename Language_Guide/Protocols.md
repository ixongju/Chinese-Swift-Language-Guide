# 协议

*协议* 定义了适合特定任何或功能的方法，属性和其他要求的蓝图。协议可以被类，结构体或枚举采用以提供这些要求的实际实现。任何满足了协议要求的类型被认为*实现*了该协议。

遵守了协议的类型必须实现指定的要求之外，还可以扩展协议以提供这些要求的实现，或实现额外的遵守了协议的类型能够使用的功能。

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

如果定义一个会改变遵守了协议的类型的实例的协议实例方法要求，在协议的定义中用`mutating`关键字标记该方法。这能使结构体和枚举实现协议并满足该方法要求。

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

协议可以要求遵守了协议的类型实现指定的初始化方法。在协议定义中用与定义一般初始化方法相同的方式定义这些初始化方法，但是没有大括号或初始化方法体：
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

协议可以为遵守了协议的类型定义可失败初始化方法要求，如[可失败初始化方法](Initialization.md#可失败初始化方法)中所定义的。

一个可失败初始化方法要求可以被遵守了协议的类型中的一个可失败初始化方法或一个非可失败初始化方法满足。一个非可失败初始化方法要求可以被一个非可失败初始化方法或一个隐式解包可失败初始化方法满足。

## 协议作为类型

实际上，协议本身不实现任何功能。尽管如此，你可以在代码中把协议当作一个完整的类型来使用。有时候把协议用作类型可以称为*存在类型*([existential type](https://en.wikipedia.org/wiki/Type_system#Existential_types))，这种叫法源于这样的描述：“这里有一个遵守了协议的类型*T*”。

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

*委托* 是一种允许类或结构体将部分职责传递（或委托）给另一个类型的实例的设计模式。通过定义封装了委托责任的协议，确保遵守了协议的类型（被称为委托）能提供被委托的功能，来实现这个设计模式。委托可被用于响应特定的动作，或从外部资源获取数据，而不需要知道该资源的底层类型。

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

上面的`Dice`类可以被扩展以遵循和实现`TextRepresentable`：
```swift
extension Dice: TextRepresentable {
    var textualDescription: String {
        return "A \(sides)-sided dice"
    }
}
```

这个扩展遵循新协议，如同`Dice`本身提供了原始实现。协议名写在类型名之后，用逗号隔开，扩展的大括号中提供了协议所有要求的实现。

现在任何`Dice`实例可以被当作`TextRepresentable`：
```swift
let d12 = Dice(sides: 12, generator: LinearCongruentialGenerator())
print(d12.textualDescription)
// Prints "A 12-sided dice"
```

类似地，`SnakesAndLadders`类也可被扩展以遵循和实现`TextRepresentable`协议：
```swift
extension SnakesAndLadders: TextRepresentable {
    var textualDescription: String {
        return "A game of Snakes and Ladders with \(finalSquare) squares"
    }
}
print(game.textualDescription)
// Prints "A game of Snakes and Ladders with 25 squares"
```

### 有条件地实现协议

一个泛型类型可以只在特定条件下满足协议的要求，例如只有当类型的泛型参数实现了协议的时候。可以通过扩展类型时列举约束条件让泛型类型有条件地实现一个协议。通过编写泛型`where`子句在要实现的协议名后面添加约束条件。更多关于泛型`where`子句，参见[泛型Where子句](Generics.md#泛型Where子句)。

下面的扩展使`Array`实例，在当储存的元素类型实现了`TextRepresentable`协议时实现`TextRepresentable`协议。
```swift
extension Array: TextRepresentable where Element: TextRepresentable {
    var textualDescription: String {
        let itemsAsText = self.map { $0.textualDescription }
        return "[" + itemsAsText.joined(separator: ", ") + "]"
    }
}
let myDice = [d6, d12]
print(myDice.textualDescription)
// Prints "[A 6-sided dice, A 12-sided dice]"
```

### 用扩展声明协议采用

如果一个类型已经实现了协议的所有要求，但是还没有声明其遵循该协议，你可以用一个空扩展使其遵循该协议：
```swift
struct Hamster {
    var name: String
    var textualDescription: String {
        return "A hamster named \(name)"
    }
}
extension Hamster: TextRepresentable {}
```

现在`Hamster`实例可以用在需要`TextRepresentable`的任何地方：
```swift
let simonTheHamster = Hamster(name: "Simon")
let somethingTextRepresentable: TextRepresentable = simonTheHamster
print(somethingTextRepresentable.textualDescription)
// Prints "A hamster named Simon"
```

> 注意：
> 只通过满足协议要求，并不能使类型自动遵循某个协议。必须显式声明类型遵循了某一协议。

## 协议类型的集合

协议可被用于集合如数组或字典中储存的类型，如[协议作为类型](#协议作为类型)中提到。下面的例子创建一个`TextRepresentable`数组：
```swift
let things: [TextRepresentable] = [game, d12, simonTheHamster]
```

现在可以遍历这个数组，并打印每个项的文本描述：
```swift
for thing in things {
    print(thing.textualDescription)
}
// A game of Snakes and Ladders with 25 squares
// A 12-sided dice
// A hamster named Simon
```

注意`thing`常量的类型是`TextRepresentable`。不是`Dice`，`DiceGame`或`Hamster`，即使实际实例类型底层是它们之一。尽管如此，因为类型是`TextRepresentable`，且任何`TextRepresentable`类型的实例被认为有`textualDescription`属性，所以在每次循环时访问`thing.textualDescription`是安全的。

## 协议继承

一个协议可以*继承*一个或多个其他协议，并可以在继承而来的要求之上添加额外要求。协议继承的语法与类继承的语法相似，协议可以列出多个继承协议，用逗号分开：
```swift
protocol InheritingProtocol: SomeProtocol, AnotherProtocol {
    // protocol definition goes here
}
```

下面是一个继承上面提到的`TextRepresentable`协议的例子：
```swift
protocol PrettyTextRepresentable: TextRepresentable {
    var prettyTextualDescription: String { get }
}
```

这个例子定义一个新协议`PrettyTextRepresentable`，继承自`TextRepresentable`。任何遵循了`PrettyTextRepresentable`的类型都必须满足`TextRepresentable`所描述的要求，加上`PrettyTextRepresentable`所描述的额外要求。在这个例子中，`PrettyTextRepresentable`添加了单个提供一个返回一个`String`的可读属性`prettyTextualDescription`的要求。

`SnakesAndLadders`类可以被扩展以遵循和实现`PrettyTextRepresentable`协议：
```swift
extension SnakesAndLadders: PrettyTextRepresentable {
    var prettyTextualDescription: String {
        var output = textualDescription + ":\n"
        for index in 1...finalSquare {
            switch board[index] {
            case let ladder where ladder > 0:
                output += "▲ "
            case let snake where snake < 0:
                output += "▼ "
            default:
                output += "○ "
            }
        }
        return output
    }
}
```

这个扩展表明其遵循`PrettyTextRepresentable`协议并为`SnakesAndLadders`类型提供一个`prettyTextualDescription`属性实现。任何属于`PrettyTextRepresentable`必须同时也是`TextRepresentable`，所以`prettyTextualDescription`的实现以访问`TextRepresentable`协议的`textualDescription`属性开始组成输出字符串。它链接一个逗号和一个换行符，然后把这当作文本表现的开始。然后遍历棋盘方块数组，链接代表每个方块内容的几何图形。
* 如果方块值大于`0`，则是梯子的底部，用▲代替。
* 如果方块值小于`0`，则是蛇的头部，用▼代替。
* 否则，方块的值是`0`，是“自由”方块，用○代替。

现在可以用属性`prettyTextualDescription`打印任何`SnakesAndLadders`实例的文本描述：
```swift
print(game.prettyTextualDescription)
// A game of Snakes and Ladders with 25 squares:
// ○ ○ ▲ ○ ○ ▲ ○ ○ ▲ ▲ ○ ○ ○ ▼ ○ ○ ○ ○ ▼ ○ ○ ▼ ○ ▼ ○
```

## 类专用协议

可以通过在协议继承列表中添加`AnyObject`协议来限制协议采用为类类型（而不是结构体或枚举）。
```swift
protocol SomeClassOnlyProtocol: AnyObject, SomeInheritedProtocol {
    // class-only protocol definition goes here
}
```

在上面的例子中，`SomeClassOnlyProtocol`只能被类类型采用。尝试编写采用了`SomeClassOnlyProtocol`协议的结构体或枚举将会导致编译时错误。

> 注意：
> 当协议要求定义的行为，假设或要求遵守了协议的类型有引用语义而不是值语义时，用类专用协议。更多关于引用和值语义，参见[结构体和枚举是值类型](Structures_and_Classes.md#结构体和枚举是值类型)和[类是引用类型](Structures_and_Classes.md类是引用类型)。

## 组合协议

要求一个类型同时实现多个协议比较有用。你可以用*组合协议*把多个协议组合到单个要求中。组合协议表现为就像你定义了一个组合了所有协议要求的临时协议。组合协议没有定义任何新协议类型。

组合协议有这样的格式`SomeProtocol & AnotherProtocol`。你可以根据需要列入更多协议，用`&`符号分开。除了协议列表，组合协议也可以包含一个类类型，可以用该类类型指定所需的父类。

下面的例子将两个协议`Named`和`Aged`结合成单个组合协议作为函数参数：
```swift
protocol Named {
    var name: String { get }
}
protocol Aged {
    var age: Int { get }
}
struct Person: Named, Aged {
    var name: String
    var age: Int
}
func wishHappyBirthday(to celebrator: Named & Aged) {
    print("Happy birthday, \(celebrator.name), you're \(celebrator.age)!")
}
let birthdayPerson = Person(name: "Malcolm", age: 21)
wishHappyBirthday(to: birthdayPerson)
// Prints "Happy birthday, Malcolm, you're 21!"
```

在这个例子中，`Named`协议要求有一个可读的`String`属性`name`。`Aged`协议要求有一个可读的`Int`属性`age`。这两个协议都被结构体`Person`采用。

例子中也定义了一个函数`wishHappyBirthday(to:)`。参数`celebrator`的类型是`Named & Aged`，意思是“任何实现了协议`Named`和`Aged`的类型”。哪一个具体那些被传给函数并没有关系，只要它实现了这两个要求的协议。

例子中创建了一个`Person`新实例`birthdayPerson`，然后把这个实例传给函数`wishHappyBirthday(to:)`。因为`Person`实现了这两个协议，这被称为有效，函数`wishHappyBirthday(to:)`可以打印其生日问候。

下面的例子，将`Named`协议和前面例子中的`Location`类组合起来：
```swift
class Location {
    var latitude: Double
    var longitude: Double
    init(latitude: Double, longitude: Double) {
        self.latitude = latitude
        self.longitude = longitude
    }
}
class City: Location, Named {
    var name: String
    init(name: String, latitude: Double, longitude: Double) {
        self.name = name
        super.init(latitude: latitude, longitude: longitude)
    }
}
func beginConcert(in location: Location & Named) {
    print("Hello, \(location.name)!")
}

let seattle = City(name: "Seattle", latitude: 47.6, longitude: -122.3)
beginConcert(in: seattle)
// Prints "Hello, Seattle!"
```

函数`beginConcert(in:)`接受一个`Location & Named`类型的参数，意思是“任何`Location`的子类且实现了`Named`协议”。本例中，`City`满足了这两者。

把`birthdayPerson`传入函数`beginConcert(in:)`中是无效的，因为`Person`不是`Location`的子类。同样，如果创建一个`Location`的子类但是没有实现`Named`协议，用该类型的实例调用`beginConcert(in:)`也是无效的。

## 检查协议一致性

如[类型转换](Type_Casting.md)中所述，可以用`is`和`as`操作符检查协议的一致性，和转换为特定协议。协议的检查和转换与类型的检查和转换语法相同：
* 如果实例实现了协议，`is`操作符返回`true`，如果没有，则返回`false`。
* `as?`版本的向下转换操作符返回协议类型的一个可选值，如果实例没有实现该协议，则该值为`nil`。
* `as!`版本的向下转换操作符强制向下转换为协议类型，如果转换未成功，则触发运行时错误。

下面的例子定义了一个协议`HasArea`，有唯一的可读`Double`属性`area`要求：
```swift
protocol HasArea {
    var ares: Double { get }
}
```

下面两个类`Circle`和`Country`，两者都实现了`HasArea`协议：
```swift
class Circle: HasArea {
    let pi = 3.1415927
    var radius: Double
    var area: Double { return pi * radius * radius }
    init(radius: Double) { self.radius = radius }
}
class Country: HasArea {
    var area: Double
    init(area: Double) { self.area = area }
}
```

`Circle`类基于`radius`属性，将`area`属性要求实现为计算属性。`Country`类直接将`area`属性要求实现为储存属性。两个类都遵守了`HasArea`协议。

下面的类`Animal`，没有遵守`HasArea`协议：
```swift
class Animal {
    var legs: Int
    init(legs: Int) { self.legs = legs }
}
```

类`Circle`，`Country`和`Animal`没有共同的基础类。尽管如此，他们都是类，所以这三个类型的实例可被用于初始化存放`AnyObject`类型值的数组：
```swift
let objects: [AnyObject] = [
    Circle(radius: 2.0),
    Country(area: 243_610),
    Animal(legs: 4)
]
```

数组`objects`被包含一个2个单位半径的`Circle`实例，一个用平方米表示的英国面积的`Country`实例和一个有四条腿的`Animal`实例的数组字面量初始化。

现在可以遍历数组`objects`，数组中的每个对象可以被检查其是否实现了`HasArea`协议：
```swift
for object in objects {
    if let objectWithArea = object as? HasArea {
        print("Area is \(objectWithArea.area)")
    } else {
        print("Something that doesn't have an area")
    }
}
// Area is 12.5663708
// Area is 243610.0
// Something that doesn't have an area
```

当数组中的对象遵循了`HasArea`协议时，`as?`操作符返回的可选值会被可选绑定解包到常量`objectWithArea`中。常量`objectWithArea`被认为是`HasArea`类型，所以可以安全地访问并打印其`area`属性。

注意转换过程并没有改变底层对象。它们仍然是`Circle`，`Country`和`Animal`。然而，当他们被存入`objectWithArea`常量时，它们只被认为是`HasArea`类型，且还能访问其`area`属性。

## 可选协议要求

可以为协议定义*可选要求*。这些要求不一定要被遵守了协议的类型实现。在协议定义中，可选要求用`optional`修饰符作为前缀。可选要求可用，以至你可以编写与Objective-C交互的代码。协议和可选要求需要用`@objc`属性标记。注意`@objc`协议只能被继承自Objective-C类的类或其他的`@objc`类采用。不能被结构体或枚举采用。

当使用可选要求中的方法或属性时，其类型自动变成可选。举个例子，方法`(Int) -> String`变成了`((Int) -> (String))?`。注意是整个函数类型包裹在可选只中，而不是方法的返回值。

可选协议要求可以被可选链调用，以解释遵守了协议的类型没有实现要求的可能性。通过在调用方法是在方法名后面写上问号来检查可选方法是否被实现，例如`someOptionalMethod?(someArgument)`。更多关于可选链，参见[可选链](Optional_Chaining)。

下面的例子定义了一个整数计数类`Counter`，该类用外部数据源提供要增加的数字。这个数据源被协议`CounterDataSource`定义，该协议有两个可选要求：
```swift
@objc protocol CounterDataSource {
    @objc optional func increment(forCount count: Int) -> Int
    @objc optional var fixedIncrement: Int { get }
}
```

`CounterDataSource`协议定义了一个可选方法要求`increment(forCount:)`和一个可选属性要求`fixedIncrement`。这些要求定义了数据源为`Counter`实例提供合适增长数的两种不同方法。

> 注意：
> 严格地讲，你可以编写一个遵守`CounterDataSource`，且实现了一个协议要求的自定义类。毕竟，它们都是可选。即使技术上允许这么做，但这不是一个好的数据源。

下面定义的`Counter`类，有一个`CounterDataSource?`类型的可选属性`dataSource`：
```swift
class Counter {
    var count = 0
    var dataSource: CounterDataSource?
    func increment() {
        if let amount = dataSource?.increment?(forCount: count) {
            count += amount
        } else if let amount = dataSource?.fixedIncrement {
            count += amount
        }
    }
}
```

`Counter`类把当前值储存在一个变量属性`count`中。`Counter`类也定义了一个方法`increment`，每次调用该方法会增加`count`属性。

方法`increment()`首先尝试通过寻找数据源的`increment(forCount:)`实现获取增长数。`increment()`方法尝试用可选链调用`increment(forCount:)`方法，然后把当前数值当作方法的唯一参数传入。

注意这里使用了*二重*可选链。第一，`dataSource`可能为`nil`，所以`dataSource`后面有一个问号表明只有当`dataSource`不为`nil`时才会调用`increment(forCount:)`。第二，即使`dataSource`存在，也无法保证其实现了`increment(forCount:)`，因为这是个可选要求。这里，`increment(forCount:)`没有实现的情况也被可选链处理了。只有当`increment(forCount:)`存在是才会调用–––也就是，不为`nil`时。这就是为什么`increment(forCount:)`后面也写上了问号。

因为调用`increment(forCount:)`可能因为这两种原因之一失败，所以方法调用返回可选的`Int`值。在`CounterDataSource`定义中`increment(forCount:)`被定义为返回一个非可选`Int`值也如此。即使有两个可选链操作符，一个接着一个，结果仍然被打包在一个可选值中。更多关于使用多个可选链操作符，参见[链接多级可选链](Optional_Chaining.md#链接多级可选链)。

调用`increment(forCount:)`之后，返回的可选`Int`被可选绑定解包到常量`amount`中。如果可选`Int`包含一个值–––也就是，委托和方法都存在，方法返回一个值–––解包的`amount`被加到储存属性`count`中，增量计算完毕。

如果无法从`increment(forCount:)`获取一个值–––因为`dataSource`是`nil`或数据源没有实现`increment(forCount:)`–––作为替换`increment()`方法尝试从数据源的`fixedIncrement`属性获取值。`fixedIncrement`属性也是可选要求，所以其值是可选`Int`值，即便在`CounterDataSource`协议定义中`fixedIncrement`被定义为非可选`Int`属性。

下面是一个简单的`CounterDataSource`实现，每次被查询数据源会返回一个常量值`3`。通过实现可选属性要求`fixedIncrement`来做到这个：
```swift
class ThreeSource: NSObject, CounterDataSource {
    let fixedIncrement = 3
}
```

可以把`ThreeSource`的实例用作一个新`Counter`实例的数据源：
```swift
var counter = Counter()
counter.dataSource = ThreeSource()
for _ in 1...4 {
    counter.increment()
    print(counter.count)
}
// 3
// 6
// 9
// 12
```

上面的代码创建一个新`Counter`实例；将其数据源设置为一个新`ThreeSource`实例；然后调用计数器的`increment()`方法四次。如你我所愿，每次调用`increment()`计数器的`count`属性增三。

下面是一个更复杂的数据源`TowardsZeroSource`，该数据源让`Counter`实例的数从当前`count`值增加或减少至零：
```swift
class TowardsZeroSource: NSObject, CounterDataSource {
    func increment(forCount count: Int) -> Int {
        if count == 0 {
            return 0
        } else if count < 0 {
            return 1
        } else {
            return -1
        }
    }
}
```

类`TowardsZeroSource`实现了`CounterDataSource`协议中的可选方法`increment(forCount:)`，用实参`count`的值计算出增加的方向。如果`count`已经是零，则方法返回`0`表明不需要更进一步的计算。

可以用`TowardsZeroSource`实例和现有的`Counter`实例从`-4`计算到零。一旦计数器到达零，将不进行任何计算：
```swift
counter.count = -4
counter.dataSource = TowardsZeroSource()
for _ in 1...5 {
    counter.increment()
    print(counter.count)
}
// -3
// -2
// -1
// 0
// 0
```

## 协议扩展

协议可被扩展以为遵守了协议的类型提供方法，初始化方法，下标和计算属性的实现。这允许你为协议本身定义行为，而不是在每个单独的类型或全局函数中。

举个例子，`RandomNumberGenerator`协议可以被扩展提供一个`randomBool()`方法，该方法用必须方法`random()`的值返回一个随机`Bool`值：
```swift
extension RandomNumberGenerator {
    func randomBool() -> Bool {
        return random() > 0.5
    }
}
```

通过为协议创建一个扩展，所有实现了该协议的类型自动拥有了这个方法实现，而不需要任何其他修改。
```swift
let generator = LinearCongruentialGenerator()
print("Here's a random number: \(generator.random())")
// Prints "Here's a random number: 0.3746499199817101"
print("And here's a random Boolean: \(generator.randomBool())")
// Prints "And here's a random Boolean: true"
```

协议扩展可以为遵守了协议的类型添加实现，但是不能使协议扩展或继承其他协议。协议继承总是在协议声明中指定。

### 提供默认实现

可以用协议扩展为该协议的任何方法或计数属性要求提供默认实现。如果一个遵守了协议的类型提供了方法或属性的自身实现，则该实现会替代扩展提供的实现被使用。

> 注意：
> 扩展提供的有默认实现的协议要求与可选协议要求不同。即使对于遵守协议的类型而言，不需要提供两者，默认实现要求不需要用可选链就能被调用。

举个例子，`PrettyTextRepresentable`协议，继承自`TextRepresentable`协议，可以提供`prettyTextualDescription`属性的默认实现，简单地返回`textualDescription`属性的值：
```swift
extension PrettyTextRepresentable {
    var prettyTextualDescription: String {
        return textualDescription
    }
}
```

### 给协议扩展添加约束

当定义协议扩展时，可以指定在方法和属性可用之前，遵循协议的类型必须满足的约束。用泛型`where`子句将这些约束写在要扩展的协议名之后。更多关于泛型`where`子句，参见[泛型Where子句](Generics.md#泛型Where子句)。

举个例子，可以为应用在任何元素遵循了`Equatable`协议的集合上的`Collection`协议定义一个扩展。通过把集合的元素约束为`Equatable`协议，标准库的一部分，可以使用`==`和`!=`操作符检查两个元素的相等或不等。
```swift
extension Collection where Element: Equatable {
    func allEqual() -> Bool {
        for element in self {
            if element != self.first {
                return false
            }
        }
        return true
    }
}
```

如果集合中所有的元素都相等，则方法`allEqual()`返回`true`。

考虑一下两个整数数组，一个所有的元素都相同，另一个不同：
```swift
let equalNumbers = [100, 100, 100, 100, 100]
let differentNumbers = [100, 100, 200, 100, 200]
```

因为数组都遵循`Collection`协议，且整数遵循`Equatable`协议，`equalNumbers`和`differentNumbers`可以使用`allEqual()`方法：
```swift
print(equalNumbers.allEqual())
// Prints "true"
print(differentNumbers.allEqual())
// Prints "false"
```

> 注意：
> 如果遵循了协议的类型满足多个为相同方法或属性提供实现的扩展的约束要求，Swift会用与最具体的约束相对应的实现。（多个扩展中，约束最具体的那个扩展的实现）

[< 扩展](Extensions.md) || [泛型 >](Generics.md)
