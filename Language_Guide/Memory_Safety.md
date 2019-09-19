# 内存安全

默认地，Swift会阻止代码中发生不安全的行为。例如，Swift确保变量在使用前已经初始化，内存回收后就不再访问，检查数组索引的越界错误。

通过要求修改内存位置的代码，独享该内存的访问权限，Swift也确保(同时)多次访问同一内存区域不会发生冲突。因为Swift自动管理内存，大多数时候你不需要考虑访问内存。尽管如此，知道那个地方会发生潜在冲突比较重要，那样你就可以避免写出，对内存访问权限有冲突的代码。如果你的代码没有冲突，你将会得到一个编译时或运行时错误。

## 弄懂内存访问冲突

当你做诸如为变量设置值或给函数传递参数这种操作时，就会发生访问内存。例如，下面的代码包含读访问和写访问：
```swift
// A write access to the memory where one is stored.
var one = 1

// A read access from the memory where one is stored.
print("We're number \(one)!")
```

当代码的不同部分同时访问内存的同一位置时，就会发生访问冲突。同时多重访问一个内存位置会产生不可预测和数据不一致的行为。在Swift中，有一些修改跨越几行代码值的方法，使得在值的修改过程中访问该值变得可能。

考虑一下你如何更新写在纸上的预算，就会看到类似的问题。更新预算是一个两步过程：首先，添加预算的名称和金额，然后，修改总金额以反应出当前列表中的预算。更新前和更新后，你可以从预算中读取任何信息，且能得到正确答案，如下面图表所示。

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/memory_shopping_2x.png" alt="弄懂内存访问冲突" width="400"/>
</p>

当你给预算添加项目时，是处于临时，无效的状态，因为没有刷新预算总数以反应当前新加的预算项目。在添加项目的过程中读取总数会得到不正确的信息。

这个例子也演示了一个当修正内存访问冲突时会遇到的挑战：有多重方式修复会产生不同答案的冲突，且哪一种答案是正确的并不明显。在这个例子中，取决于你需要原始总数还是更新之后的总数，`$5`或`$320`都可能是正确答案。在你修正访问冲突之前，你必须决定计划做什么。

> 注意：
> 如果你编写协同或多线程代码，内存访问冲突可能是一个熟悉的问题。尽管如此，上面讨论到的访问冲突可能发生在单线程中，且不涉及协同或多线程代码。
>
> 如果在单线程中遇到内存访问冲突，Swift确保你会在编译时或运行时得到一个错误。关于多线程代码，用[Thread Sanitizer](https://developer.apple.com/documentation/code_diagnostics/thread_sanitizer)协助侦测跨线程的访问冲突。

### 内存访问的特征

一个函数对于其所有的输入输出参数有长期的写入权限。对输入输出参数的写入权限，从所有的非输入输出参数被计算完毕开始持续到整个函数调用期间。如果有多个输入输出参数，写入权限开始的顺序与参数出现的顺序相同。

拥有长期写入权限的一个结果就是你不能访问输入输出参数的原始变量，即使作用域和访问控制允许–––任何对原始值的访问会导致冲突。例如：
```swift
var stepSize = 1

func increment(_ number: inout Int) {
    number += stepSize
}

increment(&stepSize)
// Error: conflicting accesses to stepSize
```

上面的代码中，`stepSize`是一个全局变量，一般在`increment(_:)`中可以访问。然而，对`stepSize`的读取权限与对`number`的写入权限重叠。如下面的图表所示，`number`和`stepSize`指向同一内存位置。读取和写入访问指向同一内存且重叠，产生冲突：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/memory_increment_2x.png" alt="内存访问的特征" width="400"/>
</p>

改变这个冲突的一种方法是显式复制`stepSize`：
```swift
// Make an explicit copy.
var copyOfStepSize = stepSize
increment(&copyOfStepSize)

// Update the original
stepSize = copyOfStepSize
// stepSize is now 2
```

当在调用`increment(_:)`之前复制一份`stepSize`，很明显`copyOfStepSize`的值被增加了当前的`stepSize`。写入访问开始之前读取访问已经结束，就没有冲突。

对输入输出参数拥有长期写入权限的另一个结果是，给有多个输入输出参数的函数传入同一个变量会产生冲突。例如：
```swift
func balance(_ x: inout Int, _ y: inout Int) {
    let sum = x + y
    x = sum / 2
    y = sum - x
}
var playerOneScore = 42
var playerTwoScore = 30
balance(&playerOneScore, &playerTwoScore)  // OK
balance(&playerOneScore, &playerOneScore)
// Error: conflicting accesses to playerOneScore
```

上面的`balance(_:_:)`函数即将两个参数的总值平分给它们。用`playerOneScore`和`playerTwoScore`当作参数调用该函数不会产生冲突–––同时有两个写入访问，但是在内存的不同位置上。相反，把`playerOneScore`当作两个参数的值传入会产生冲突，因为这试图在内存中相同的位置同时执行两个写入操作。

> 注意：
> 因为操作符是函数，它们也能对输入输出参数拥有长期访问权限。例如，如果`balance(_:_:)`是操作符函数`<^>`，编写`playerOneScore <^> playerOneScore`会导致与`(&playerOneScore, &playerOneScore)`一样的冲突。

## 在方法中对self的访问冲突

结构体的可变方法在被调用时有对`self`的写入权限。例如，想想一个游戏，游戏中每个玩家都有一个健康值，当受伤之后会减少，并且有一个能量值，当使用特技时会减少：
```swift
struct Player {
    var name: String
    var health: Int
    var energy: Int

    static let maxHealth = 10
    mutating func restoreHealth() {
        health = Player.maxHealth
    }
}
```

在上面的方法`restoreHealth()`中，对`self`的写入访问从方法开始持续到方法返回。这个例子中，`restoreHealth()`中没有其他代码可以重叠访问`Player`实例的属性。下面的`shareHealth(with:)`方法接受另一个`Player`实例作为输入输出参数，可能导致重叠访问。
```swift
extension Player {
    mutating func shareHealth(with teammate: inout Player) {
        balance(&teammate.health, &health)
    }
}

var oscar = Player(name: "Oscar", health: 10, energy: 10)
var maria = Player(name: "Maria", health: 5, energy: 10)
oscar.shareHealth(with: &maria)  // OK
```

在上面的例子中，对玩家Oscar调用`shareHealth(with:)`方法与玩家Maria分享健康值不会引起冲突。调用方法期间，有对`oscar`的写入访问，因为在可变方法中`oscar`就是`self`的值，同时有对`maria`的写入访问，因为`maria`被当作输入输出参数传入。如下面图表所示，它们访问了内存的不同位置。即使这两次访问时间重叠，但是不会冲突：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/memory_share_health_maria_2x.png" alt="在方法中对self的访问冲突" width="400"/>
</p>

尽管如此，如果把`oscar`当作参数传入`shareHealth(with:)`，会发生冲突：
```swift
oscar.shareHealth(with: &oscar)
// Error: conflicting accesses to oscar
```

在方法执行期间，可变方法需要对`self`写入访问，且同时输入输出参数需要对`teammate`的写入访问。在方法中，`self`和`teammate`指向内存的同一位置–––如下图表所示。这两个写入访问指向同一内存且重叠了，于是产生冲突：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/memory_share_health_oscar_2x.png" alt="在方法中对self的访问冲突" width="400"/>
</p>

## 属性访问冲突

诸如结构体，元组和枚举等类型是由独立的值组成，例如结构体的属性，或元组的元素。因为它们是值类型，改变任何部分的值就改变了整个值，意味着对一个属性的读写访问需要对整个值的读写访问。例如，对一个元组的元素重叠写入访问会产生冲突：
```swift
var playerInformation = (health: 10, energy: 20)
balance(&playerInformation.health, &playerInformation.energy)
// Error: conflicting access to properties of playerInformation
```

在上面的例子中，对元组的元素调用`balance(_:_:)`会产生冲突，因为有对`playerInformation`的重叠写入访问。`playerInformation.health`和`playerInformation`被同时当作输入输出参数传入，意味着在函数被调用时`balance(_:_:)`需要对两者进行写入访问。这两种情况中，对元组元素的写入访问需要对整个元组的写入访问。这意味着对`playerInformation`有两个写入访问重叠了，所以引起冲突。

下面的代码展示了对储存在全局变量中结构体属性的重叠写入访问时出现的相同错误。
```swift
var holly = Player(name: "Holly", health: 10, energy: 10)
balance(&holly.health, &holly.energy) // Error
```

实践中，大多数对结构体属性的重叠访问是安全的。例如，如果上例中的变量`holly`被修改成局部变量而不是全局变量，编译器可以验证，对结构体储存属性的重叠访问是安全的：
```swift
func someFunction() {
    var oscar = Player(name: "Oscar", health: 10, energy: 10)
    balance(&oscar.health, &oscar.energy)  // OK
}
```

在上面的例子中，Oscar的健康值和能量值被传递给`balance(_:_:)`的两个输入输出参数。编译器可以验证内存是安全的，因为这两个储存属性不以任何方式互动。

并不需要总是限制对结构体属性的重叠访问，以保证内存安全。内存安全是一种理想的保证，独享访问比内存安全要求更严格–––意味着一些代码保证内存安全，即使其违反了对内存的独享访问。如果编译器能保证对内存的非独享访问是安全的，Swift允许这种内存安全代码。特别地，如果适合以下条件，则对结构体属性的重叠访问将被证明是安全的：
* 正在访问实例的储存属性，而不是计算属性或类属性。
* 结构体是局部变量的值，而不是全局变量的值。
* 结构体没有被任何闭包捕获，或仅被非逃逸闭包捕获。

如果编译器不能确保访问是安全的，将不允许该访问。

[< 自动引用计数](Automatic_Reference_Counting.md) || [访问控制 >](Access_Control.md)
