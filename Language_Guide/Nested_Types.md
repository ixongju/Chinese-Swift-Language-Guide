# 嵌套类型

通常创建枚举用于支持某一个类或结构体的功能。类似地，定义只为在更复杂类型的上下文中使用的工具类或结构体可以变得很方便。为了达到此目的，Swift允许你定义嵌套类型，也就是你可以把枚举，类和结构体嵌套在他们所支持类型的定义里面。

要嵌套一个类型到另一个类型中，将定义写在其所支持类型的大括号内。类型可以根据需要嵌套到所需要的级别。

## 类型嵌套实践

下面的例子定义了一个结构体`BlackjackCard`，模拟二十一点游戏中用到的纸牌。结构体`BlackjackCard`包含两个嵌套的枚举`Suit`和`Rank`。

在二十一点中，`Ace`纸牌有一个值1或11。这个功能用结构体`Values`表示，该结构体嵌套在枚举`Rank`中：
```swift
struct BlackjackCard {

    // nested Suit enumeration
    enum Suit: Character {
        case spades = "♠", hearts = "♡", diamonds = "♢", clubs = "♣"
    }

    // nested Rank enmeration
    enum Rank: Int {
        case two = 2, three, four, five, six, seven, eight, nine, ten
        case jack, queen, king, ace
        struct Values {
            let first: Int, second: Int?
        }
        var values: Values {
            switch self {
                case .ace:
                  return Values(first: 1, second: 11)
                case .jack, .queen, .king:
                  return Values(first: 10, second: nil)
                default:
                  return Values(first: self.rawValue, second: nil)
            }
        }
    }

    // BlackjackCard properties and methods
    let rank: Rank, suit: Suit
    var description: String {
        var output = "suit is \(suit.rawValue),"
        output += " value is \(rank.values.first)"
        if let second = rank.values.second {
            output += " or \(second)"
        }
        return output
    }
}
```

枚举`Suit`描述了四中常见纸牌，与一个`Character`(字符)原始值一起表示符号。

枚举`Rank`描述了十三中可能的纸牌排名，与一个`Int`原始值一起表示面值。（`Int`的原始值不适用于Jack，Queen，King和Ace纸牌。）

如上面提到的，枚举`Rank`自身定义了更深的嵌套结构体，`Values`。这个结构体封装大多数纸片有一个值，但是Ace有两个值的事实。为了表示这个，结构体`Values`定义了两个属性：
* `first`，`Int`类型
* `second`，`Int?`类型，或"可选的`Int`"

`Rank`也定义了一个计算属性`values`，该属性返回一个`Values`结构体实例。计算属性考量纸牌的级别，然后根据其级别用合适的值初始化一个新`Values`实例。对于`jack`，`queen`，`king`和`ace`用特别的值。对于数字纸牌，用级别的原始值`Int`。

结构体`BlackjackCard`本身有两个属性---`rank`和`suit`。也定义了一个计算属性`description`，该属性用`rank`和`suit`中储存的值创建纸牌名称和值的描述信息。`description`属性用可选绑定检查是否有第二个值需要展示，如果有，则为第二个值添加额外描述细节。

因为`BlackjackCard`是一个没有自定义初始化方法的结构体，它有一个隐藏的成员初始化方法，如中[结构类型的成员初始化方法](Initialization.md#结构类型的成员初始化方法)所述。你可以用这个初始化方法初始化一个新常量`theAceOfSpades`：
```swift
let theAceOfSpades = BlackjackCard(rand: .ace, suit: .spades)
print("theAceOfSpades: \(theAceOfSpades.description)")
// Prints "theAceOfSpades: suit is ♠, value is 1 or 11"
```

即使`Rank`和`Suit`被嵌套在`BlackjackCard`中，他们的类型可以从上下文推导出来，实例的初始化也能仅仅从分支名(`.ace`和`.spades`)推导出枚举分支(`Rank.ace`和`Suit.spades`)。在上面的例子中，`description`属性正确地报告了黑桃Ace有一个1或11的值。

## 引用嵌套类型

为了在定义上下文以外使用嵌套类型，在类型名前面加上嵌套该类型的类型名：
```swift
let heartsSymbol = BlackjackCard.Suit.hearts.rawValue
// heartsSymbol is "♡"
```

对于上面的例子，这能让`Suit`，`Rank`和`Values`的名称有意地保持简短，因为它们的名称自然是由定义的上下文所决定的。

[< 类型转换](Type_Casting.md) || [扩展 >](Extensions.md)
