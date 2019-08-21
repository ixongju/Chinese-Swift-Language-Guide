# 反初始化

一个类实例被回收之前，其反初始化方法会立即被调用。与用`init`关键字编写初始化方法类似，用`deinit`关键字编写反初始化方法。反初始化方法只对类类型有效。

## 反初始化如何工作

Swift会自动回收不再需要的实例，以释放资源。Swift通过 *自动引用计数(ARC)* 处理实例的内存管理，如[自动引用计数](Automatic_Reference_Counting.md)中所述。当实例被回收时，你不需要特地手动清理。但是，当你使用自己的资源时，你也许需要自己执行一些额外的清理。例如，创建一个类，用它打开一个文件并写入一些数据，你可能需要在类实例被回收前，关闭文件。

类的定义中，每个类只能有一个反初始化方法。反初始化方法不接受任何参数，且无括号。
```swift
deinit {
  // perform the deinitialization
}
```

反初始化方法会在实例回收前被自动调用。不允许手动调用反初始化方法。父类的反初始化方法被子类继承，父类反初始化方法在子类反初始化方法实现后自动被调用。即使子类没有提供自身的反初始化方法，父类的反初始化方法总是被调用。

因为实例直到反初始化方法被调用后才被回收，所以反初始化方法可以访问实例所有的属性，且可以依据这些属性修改实例的行为（例如，查看需要被关闭的文件名称）。

## 反初始化方法实践

下面是一个反初始化实践例子。例中为一个简单游戏定义了两个类型`Bank`和`Player`。`Bank`类管理虚拟货币，在一次交易中不能拥有超过10000的硬币。游戏里面只能有一个`Bank`，所以`Bank`被实现为有类型属性和类型方法的类，来管理当前状态：
```swift
class Bank {
    static var coinsInBank = 10_000
    static func distribute(coins numberOfCoinsRequested: Int) -> Int {
        let numberOfCoinsToVend = min(numberOfCoinsRequested, coinsInBank)
        coinsInBank -= numberOfCoinsToVend
        return numberOfCoinsToVend
    }
    static func receive(coins: Int) {
        coinsInBank += coins
    }
}
```

`Bank`用属性`coinsInBank`统计当前拥有硬币的数量。同时提供两个方法---`distribute(coins:)`和`receive(coins:)`---处理硬币的发布和回收。

`distribute(coins:)`在发布硬币前，检查是否有足够的硬币。如果不够，`Bank`会返回一个比请求数字小的数（如果银行没有剩余硬币，则返回0）。 它返回一个整数表示被提供的金币的总数。

`receive(coins:)`方法只是简单地将收到的硬币数添加到银行硬币仓库中。

`Player`类描述游戏中的一个玩家。任何时刻每个玩家钱包中都有一定数量的硬币。这个用`coinsInPurse`属性表示：
```swift
class Player {
    var coinsInPurse: Int
    init(coins: Int) {
        coinsInPurse = Bank.distribute(coins: coins)
    }
    func win(coins: Int) {
        coinsInPurse += Bank.distribute(coins: coins)
    }
    deinit {
        Bank.receive(coins: coinsInPurse)
    }
}
```

初始化期间，每个被初始化的`Player`实例从银行获取一定数量的硬币，如果硬币数量不足，一个玩家可能获取较少的硬币。

`Player`类定义了一个方法`win(coins:)`，该方法从银行获取一定数量的硬币，然后添加到玩家的钱包中。`Player`类同时实现了反初始化方法，`Player`实例被回收前会被调用。在这里，反初始化方法简单地将玩家所有的硬币返还给银行。
```swift
var playerOne: Player? = Player(coins: 100)
print("A new player has joined the game with \(playerOne!.coinsInPurse) coins")
// Prints "A new player has joined the game with 100 coins"
print("There are now \(Bank.coinsInBank) coins left in the bank")
// Prints "There are now 9900 coins left in the bank"
```

如果硬币足够，将会请求100个硬币，来创建一个`Player`实例。`Player`实例被厨房在一个可选`Player`变量`playerOne`中。之所以用可选变量，是因为玩家可能随时离开游戏。可选值帮你跟踪当前游戏中是否有玩家。

因为`playerOne`是可选值，所以当使用其`coinsInPurse`属性打印当前硬币数量是，需要用感叹号(`!`)，`win(coins:)`被调用时也是如此：
```swift
playerOne!.win(coins: 2_000)
print("PlayerOne won 2000 coins & now has \(playerOne!.coinsInPurse) coins")
// Prints "PlayerOne won 2000 coins & now has 2100 coins"
print("The bank now only has \(Bank.coinsInBank) coins left")
// Prints "The bank now only has 7900 coins left"
```

这里，玩家赢了2000硬币。现在玩家钱包中有2100硬币，银行中只剩下7900个硬币：
```swift
playerOne = nil
print("PlayerOne has left the game")
// Prints "PlayerOne has left the game"
print("The bank now has \(Bank.coinsInBank) coins")
// Prints "The bank now has 10000 coins"
```

现在，通过将`playerOne`设置为`nil`，意思是“没有玩家实例”，来表明玩家离开游戏。当这个情况发生时，`playerOne`变量对`Player`实例的引用被中断。没有其他属性或变量引用`Player`实例，为了释放内存，它将被回收。在回收前，反初始化方法自动被调用，该玩家拥有的硬币被返还给银行。

[< 初始化](Initialization.md) || [可选链 >](Optional_Chaining.md)
