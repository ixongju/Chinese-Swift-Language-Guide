# 控制流

Swift提供多种控制流语句。包括用`while`循环多次执行一个任务；用`if`，`guard`和`switch`语句基于某些条件执行不同的代码分支；和诸如`break`，`continue`等将代码执行流程转到其他点的语句。

Swift也提供`for-in`循环，这使遍历数组，字典，范围，字符串和其他序列变得简单。

Swift的`switch`语句相对而言比其他类C语言中的强大的多。分支可以匹配不同的样式，包括区间匹配，元组和特定类型转换。匹配的值可以包装成临时常量或变量，以在分支代码块内使用，复杂的匹配条件可以用`where`分句表示。

## For-In循环

可以用`for-in`循环遍历序列，例如数组中的元素，数字范围或字符串中的字符。

下面的例子用`for-in`循环遍历数组中的元素：
```swift
let names = ["Anna", "Alex", "Brian", "Jack"]
for name in names {
  print("Hello, \(name)!")
}
// Hello, Anna!
// Hello, Alex!
// Hello, Brian!
// Hello, Jack!
```

也可以通过遍历来访问字典的键值对。字典被遍历时，每个元素会以`(key, value)`元组的形式被返回，可以将元组`(key, value)`拆解成显式命名的常量以在循环体中使用。下面的示例代码中，字典的键集合被拆解到`animalName`常量里，值集合被拆解到`legCount`常量里。
```swift
let numberOfLegs = ["spider": 8, "ant": 6, "cat": 4]
for (animalName, legCount) in numberOfLegs {
  print("\(animalName)s have \(legCount) legs")
}
// cats have 4 legs
// ants have 6 legs
// spiders have 8 legs
```

字典的内容是内部无序的，遍历并不能确保元素被取出的顺序。特别的，元素的插入顺序不代表被遍历的顺序。更多关于数组和字典，参见[集合类型](Collection_Types.md)。

也可以对数字范围用`for-in`循环。下面的例子打印五次表中得前几位：
```swift
for index in 1...5 {
  print("\(index) times 5 is \(index * 5)")
}
// 1 times 5 is 5
// 2 times 5 is 10
// 3 times 5 is 15
// 4 times 5 is 20
// 5 times 5 is 25
```

被遍历的序列是1到5的数字范围，闭合范围运算符表明包括1和5。`index`的值被设置成范围的第一个数字`1`，循环中的语句开始被执行。这种情况下，循环体仅包含一句代码，用来打印`index`当前值乘以五的结果。语句被执行后，`index`的值被更新到数字范围的第二位`2`，`print(_:separator:terminator:)`方法再次被调用。这个过程一直被重复执行，知道数字范围末尾。

上面的例子中，`index`是一个常量，它的值在循环遍历开始时就自动被设定。因此，`index`在使用前不需要声明。当它被包含在循环声明中时就已经被隐式声明，不需要用`let`声明关键字。

```swift
let base = 3
let power = 10
var answer = 1
for _ in 1...power {
    answer *= base
}
print("\(base) to the power of \(power) is \(answer)")
// Prints "3 to the power of 10 is 59049"
```

上面的例子计算一个数乘以另一个数的积（3乘以10）。首先1乘以3，用一个1到10的闭合范围执行十次。对于这个计算，每次循环各个计数器的值都不必须的---代码只是执行循环正确的次数。循环变量位置用下划线(`_`)会让这个值被忽略，在循环遍历过程中，不提供对当前值的访问。

在一些情况下，你或许想使用包含两端的闭合范围。想象一下在时钟表面每一分钟的标记。你想从0分钟开始画60个标记。用半开范围运算符包括开始点，不包括终止点。更多关于范围，参见[范围运算符](Basic_Operators.md#范围运算符)。

```swift
let minutes = 60
for tickMark in 0..<minutes {
  // render the tick mark each minute (60 times)
}
```

有些用户希望界面上少一点标记。他们希望每5分钟一个标记。用`stride(from:to:by:)`函数跳过不需要的标记：
```swift
let minuteInterval = 5
for tickMark in stride(from: , to: minutes, by: minuteInterval) {
  // render the tick mark every 5 minutes (0, 5, 15 ... 45, 50 ,55)
}
```

闭合范围也可用，用`stride(from:through:by:)`替代：
```swift
let hours = 12
let hourInterval = 3
for tickMark in stride(from: 3, through: hours, by: hourInterval) {
    // render the tick mark every 3 hours (3, 6, 9, 12)
}
```

## While循环

`while`循环会循环执行一段语句，知道某一条件变成`false`。当开始第一次遍历时，并不知道遍历次数的情况下，常常用`while`循环。Swift提供两种`while`循环：
* `while`在每次循环开始之前，计算条件的值
* `repeat-while`在每次循环之后，计算条件的值

### While
`while`循环从计算一个条件语句开始。如果条件为`true`，则一套语句会被重复执行，直到条件变为`false`。

下面是`while`循环的一般格式：
```swift
while `condition` {
  `statements`
}
```

这个例子中，玩一个*蛇与梯子*的游戏（*蛇梯棋*）：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/snakesAndLadders_2x.png" alt="集合类型" width="600"/>
</p>

游戏规则如下：
- 棋盘有25个方块，目标是到达或超过第25个方块
- 玩家从第一个方块开始（方块0），也就是棋盘的左下角
- 每一轮，投掷一枚骰子，然后按照骰子上的数字，向图中虚线箭头方向移动
- 如果落到了梯子的底部，则可以爬上梯子
- 如果落到了蛇的头部，则滑落到蛇的尾部

棋盘代表一个`Int`数组。数组大小基于`finalSquare`常量，用它来初始化数组并用来作为判断游戏获胜的条件。因为玩家从方块0出发，棋盘用26个`0`初始化，而不是25个：
```swift
let finalSquare = 25
var board = [Int](repeating: 0, count: finalSquare + 1)
```

一些方块被设置成特殊值，以代表蛇和梯子。梯子底部的方块有一个正数，代表移动的步数，而蛇头部的方块有一个负数，代表后退的步数。
```swift
board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
```

方块3包含一个梯子的底部，能让你移动到方块11。为实现这个，`board[03]`等于`+08`，相当于正数`8`（3与11的差值）。为了对齐值和语句，一元加号运算符(`+i`)会和一元减号运算符(`-i`)一起被显式地使用，并且小于10的数组前面会补上零。（这两种写法在技术上不是严格要求的，但可以使代码更简洁。）
```swift
var square = 0
var diceRoll = 0
while square < finalSquare {
    // roll the dice
    diceRoll += 1
    if diceRoll == 7 { diceRoll = 1 }
    // move by the rolled amount
    square += diceRoll
    if square < board.count {
        // if we're still on the board, move up or down for a snake or a ladder
        square += board[square]
    }
}
print("Game over!")
```

上面的例子用非常简单的方法掷骰子。让`diceRoll`从`0`开始，而不会生成一个随机数。每次`while`循环，`diceRoll`被加1，然后检查是否变得太大。当增加到7时，骰子变得太大，会被重置为1。结果是一连串的`diceRoll`值，如`1`，`2`，`3`，`4`，`5`，`6`，`1`，`2`等。

投资骰子之后，玩家根据`diceRoll`向前移动。有可能玩家超出了方块25，那样游戏就结束。为了应对这种情况，代码检查了`square`小于`board`的`count`属性。如果`square`无效，则存放的值`board[square]`被加到`square`上，以让玩家顺着蛇或梯子移动。

> 注意：
> 如果不进行检查，`board[square]`可能会尝试着访问越界的值，这会触发运行时错误。

当前`while`循环会结束，循环条件被检查，决定是否继续执行循环。如果玩家被移动或超出了数字25，循环条件为`false`，游戏结束。

`while`循环适合这种情况，因为循环开始时，尚不知道游戏长度。相反，执行循环，直到满足特定条件。

## Repeat-While

`while`循环的两一个变种，是`repeat-while`循环，在考虑循环条件之前，先执行一次循环体。然后继续重复执行循环体直到循环条件为`false`。
> 注意：
> Swift中的`repeat-while`循环与其他语言中的`do-while`循环类似。

下面是`repeat-while`循环的一般格式：
```swift
repeat {
 `statements`
} while `condition`
```

下面还是*蛇与梯子*的例子，用`repeat-while`循环，而不是`while`循环。`finalSquare`，`board`，`square`和`diceRoll`用与`while`循环中相同的方法初始化。

```swift
let finalSquare = 25
var board = [Int](repeating: 0, count: finalSquare + 1)
board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
var square = 0
var diceRoll = 0
```

在这个版本的游戏中，第一步是检查梯子或蛇。没有梯子直接把玩家传送到方块25，所以通过一个梯子，不会立马赢了游戏。因此，第一步检查梯子或蛇是安全的。

游戏开始时，玩家在方块0。`board[0]`总是等于`0`，没有影响：
```swift
repeat {
    // move up or down for a snake or ladder
    square += board[square]
    // roll the dice
    diceRoll += 1
    if diceRoll == 7 { diceRoll = 1 }
    // move by the rolled amount
    square += diceRoll
} while square < finalSquare
print("Game over!")
```

代码检查完蛇和梯子后，掷骰子然后根据骰子移动玩家。当前循环结束。

循环条件（`while square < finalSquare`）与前面一样，但这次要等到第一次循环结束才会检查。`repeat-while`循环结构比前面例子中的`while`循环更适合这个游戏。在`repeat-while`中，`square += board[square]`总是在通过循环条件确认方块在棋盘之内后立即执行。这样移除了上例中 `while`循环数组边界检查的需要。

## 条件语句

基于某些条件执行不同代码片段通常很有用。你也许想在错误发生时，执行额外的代码片段，或在某值变得太大或太小时显式一个信息。要这样做，你可以将代码条件化。

Swift提供两种给代码添加条件分支的方法：`if`语句和`switch`语句。通常，判断简单的条件，有多种不同结果时，用`if`语句。`switch`语句适用于更复杂的有多个排列可能性的条件，并且在模式匹配时帮助选择执行合适的代码分支的情况下也很有用。

### If

在最简单的格式中，`if`语句有一个单独的条件。当条件为`true`时，会执行一系列语句。
```swift
var temperatureInFahrenheit = 30
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
}
// Prints "It's very cold. Consider wearing a scarf."
```

上面的例子检查温度是否小于或等于32华氏度（水的结冰点）。如果是，打印一个信息。否则，不打印，`if`语句结束后，其余代码继续执行。

`if`语句可以为`if`条件为`false`的情况，提供另外的语句，称为`else`子句。这些语句用`else`关键字标明：
```swift
temperatureInFahrenheit = 40
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
} else {
    print("It's not that cold. Wear a t-shirt.")
}
// Prints "It's not that cold. Wear a t-shirt."
```

两个分支中的一个总会被执行。因为温度上升到了40华氏度，没有冷到要围围巾，所以`else`语句被触发。

可以将多个`if`语句链接在一起来考虑另外的子句。
```swift
temperatureInFahrenheit = 90
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
} else if temperatureInFahrenheit >= 86 {
    print("It's really warm. Don't forget to wear sunscreen.")
} else {
    print("It's not that cold. Wear a t-shirt.")
}
// Prints "It's really warm. Don't forget to wear sunscreen."
```

这里，另外一个`if`语句被用于一个暖和的温度。仍然有`else`子句，它针对任何不太冷和不太热的温度打印响应。

最后一个`else`是可选的。但是，如果不需要考虑所有的条件，则可以将其省略。
```swift
temperatureInFahrenheit = 72
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
} else if temperatureInFahrenheit >= 86 {
    print("It's really warm. Don't forget to wear sunscreen.")
}
```

因为既不太冷也不太热才会触发`if`或`else if`语句，所以没有打印信息。

### Switch

`swift`语句将一个值与多个可能的模式进行比较。然后，根据第一个匹配成功的模式，执行相应的代码块。`switch`语句提供了`if`语句替代方案，用来响应多个潜在状态。

最简单的格式中，`switch`语句将一个值与一个或多个同类型值进行比较：
```swift
switch `some value to consider` {
case `value 1`:
    `respond to value 1`
case `value 2`,
     `value 3`:
    `respond to value 2 or 3`
default:
    `otherwise, do something else`
}
```

每个`swift`语句有多个可能的 *情况(分支)*组成，他们都以`case`关键字开始。除了与特定值做比较外，Swift提供了几种让每个分支指定更复杂的匹配模式的方式。这些将在本章节后面讨论。

就像`if`语句，每个`case`是分开的代码执行分支。`switch`语句决定应该执行哪个分支。这个过程称为切换被考量的值。

每个`switch`语句必须*彻底*。也就是说，被考量类型的每个可能的值，必须匹配一个`switch`分支。如果不能做到为每个可能的值提供一个分支，可以定义一个默认分支来覆盖没有显式列举出来的值。默认分支用`default`关键字标明，且必须在最后面。

这个例子用`switch`语句考量单个小写字符`someCharacter`：
```swift
let someCharacter: Character = "z"
switch someCharacter {
case "a":
    print("The first letter of the alphabet")
case "z":
    print("The last letter of the alphabet")
default:
    print("Some other character")
}
// Prints "The last letter of the alphabet"
```

`switch`语句第一个分支匹配英文字母表的第一个字母`a`，第二个分支匹配`z`。因为对于每个可能的字符，`switch`必须有一个对应的分支，且不限于字母，`switch`语句用`default`分支匹配`a`和`z`之外的情况。这个措施确保`switch`语句彻底。

#### 非隐式穿透

与C和Objective-C中的`switch`语句相反，Switch中的`switch`语句默认不会穿透每个分支底部，去执行下一个分支。相反，第一个匹配的分支执行完毕之后，整个`switch`语句执行完毕，而不需要显式的`break`语句。这使`switch`语句比C中的更安全和易用，且避免意外执行多个分支。

> 注意：虽然Swift并不要求写`break`，可以用`break`语句匹配或忽略某一个分支，或在一个匹配的分支被执行完之前跳出该分支。详情请参考[中断Switch语句](#中断Switch语句)。

每个分支必须包含至少一个可执行语句。下面的代码无效，因为第一个分支是空：
```swift
let anotherCharacter: Character = "a"
switch anotherCharacter {
case "a": // Invalid, the case has an empty body
case "A":
    print("The letter A")
default:
    print("Not the letter A")
}
// This will report a compile-time error.
```

不想C中的`switch`语句，这个`switch`语句不匹配同时`a`和`A`，它会报编译时错误：分支`case "a":`没有任何可执行语句。这种做法避免从一个分支意外穿透到另一个分支，是代码更安全，意图更清晰。

要`switch`用单个分支同时匹配`"a"`和`"A"`，将它们结合到一个复合分支中，用逗号隔开：
```switch
let anotherCharacter: Character = "a"
switch anotherCharacter {
  case "a", "A":
    print("The letter A")
  default:
    print("Not the letter A")
}
// Prints "The letter A"
```

为了更加可读，复合分支可以写成多行，关于复合分支，参见[复合分支](#复合分支)。

> 注意：
> 要显式穿透某一分支，用`fallthrough`关键字，在[穿透](#穿透)中有描述。

### 范围匹配

可以检查`switch`分支中的值是否被包含在范围中。这个例子用数字范围为任何大小的数字提供一个自然语言数字：
```swift
let approximateCount = 62
let countedThings = "moons orbiting Saturn"
let naturalCount: String
switch approximateCount {
case 0:
    naturalCount = "no"
case 1..<5:
    naturalCount = "a few"
case 5..<12:
    naturalCount = "several"
case 12..<100:
    naturalCount = "dozens of"
case 100..<1000:
    naturalCount = "hundreds of"
default:
    naturalCount = "many"
}
print("There are \(naturalCount) \(countedThings).")
// Prints "There are dozens of moons orbiting Saturn."
```

上面的例子中，`approximateCount`被`switch`语句评估。在每个分支中将该值与一个数字或范围比较。因为`approximateCount`的值在12到100之间，`naturalCount`被赋值为`dozens of`，然后，程序跳出`switch`语句。

### 元组

可以在同一个`switch`语句中，用元组测试多个值。元组中的元素可以与不同的值或值范围进行测试。另外，用下划线(`_`)，称为通配模式，匹配任何可能的值。

下面的例子用点(x,y)，用元组表示为(`Int`, `Int`)，并把点分布到例子下面的坐标系中：
```switch
let somePoint = (1, 1)
switch somePoint {
case (0, 0):
    print("\(somePoint) is at the origin")
case (_, 0):
    print("\(somePoint) is on the x-axis")
case (0, _):
    print("\(somePoint) is on the y-axis")
case (-2...2, -2...2):
    print("\(somePoint) is inside the box")
default:
    print("\(somePoint) is outside of the box")
}
// Prints "(1, 1) is inside the box"
```

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/coordinateGraphSimple_2x.png" alt="元组" width="300"/>
</p>

`switch`语句决定点是在原始位置(0, 0)，还是在红色x轴或黄色y轴上，原点(0, 0)周围4x4的范围内，或该范围外。

不像C，Switch允许多个`switch`分支考量相同的一个或多个值。事实上，点(0, 0)可以匹配所有上述四种情况。但是，如果多匹配变得可能，第一个匹配分支总是可用。点(0, 0)首先匹配`case (0, 0)`，其余分支会被忽略。

### 值绑定

一个`switch`分支可以给值命名一个临时常量或变量，以在分支体中使用。这个行为被称为*值绑定*，因为值被绑定到分支体的临时常量或变量中。

下面的例子用点(x,y)，用元组表示为(`Int`, `Int`)，并把点分布到例子下面的坐标系中：
```swift
let anotherPoint = (2, 0)
switch anotherPoint {
case (let x, 0):
    print("on the x-axis with an x value of \(x)")
case (0, let y):
    print("on the y-axis with a y value of \(y)")
case let (x, y):
    print("somewhere else at (\(x), \(y))")
}
// Prints "on the x-axis with an x value of 2"
```
<p align="center">
<img src="https://docs.swift.org/swift-book/_images/coordinateGraphMedium_2x.png" alt="值绑定" width="300"/>
</p>

`switch`语句判定点是否在红色x轴上，黄色y轴上，或其他地方。

三个`switch`分支声明了包含常量`x`和`y`的占位符，从`anotherPoint`中临时获取一个或全部元组值。第一个分支，`case (let x, 0)`，匹配任何y值为0的点，并将该点的x值赋值给临时常量`x`。类似的，第二个分支，`case (0, let y)`，匹配任何x值为0的点，并将该点的y值赋值给临时常量`y`。

临时常量被声明后，可以在分支代码块中使用他们。在这里，他们被用于打印点的分布。

这个`switch`语句没有`default`分支。最后一个分支，`case let (x, y)`，声明包含两个占位符的元组，可以匹配其他所有的值。因为`anotherPoint`是一个包含两个值的元组，这个分支能匹配所有可能的情况，不需要`default`分支让`switch`语句变得彻底。

### Where

一个`switch`分支可以用`where`子句判断额外条件。

下面的例子将点分布到下面的坐标系中：
```switch
let yetAnotherPoint = (1, -1)
switch yetAnotherPoint {
case let (x, y) where x == y:
    print("(\(x), \(y)) is on the line x == y")
case let (x, y) where x == -y:
    print("(\(x), \(y)) is on the line x == -y")
case let (x, y):
    print("(\(x), \(y)) is just some arbitrary point")
}
// Prints "(1, -1) is on the line x == -y"
```

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/coordinateGraphComplex_2x.png" alt="Where语句" width="300"/>
</p>

`switch`语句确定点是否在`x == y`绿色对角线上，紫色`x == -y`对角线上或两者。

三个`switch`分支声明常量占位符`x`和`y`，该占位符临时获取元组`yetAnotherPoint`的两个值。这些常量被用做`where`子句的一部分，以创建动态过滤器。`switch`分支只有在`where`子句为`true`的情况下才匹配当前的`point`值。

如前面的例子样，最后一个分支匹配其他所有可能的值，所以不需要用`default`分支使`switch`语句彻底。

### 复合分支

多个共用同一分支体的分支，可以结合起来将匹配模式写在一个`case`后面，用逗号把匹配模式隔开。如果任何一个模式匹配了，则该分支被匹配。如果模式很长，可以写成多行。例如：
```swift
let someCharacter: Character = "e"
switch someCharacter {
case "a", "e", "i", "o", "u":
    print("\(someCharacter) is a vowel")
case "b", "c", "d", "f", "g", "h", "j", "k", "l", "m",
     "n", "p", "q", "r", "s", "t", "v", "w", "x", "y", "z":
    print("\(someCharacter) is a consonant")
default:
    print("\(someCharacter) is not a vowel or a consonant")
}
// Prints "e is a vowel"
```

`switch`语句第一个分支匹配5个小写英文元音。类似地，第二个分支匹配所有的小写英文辅音。最后，`default`分支匹配其余所有字符。

复合分支也可以包含值的绑定。所有复合分支的模式必须包含相同绑定值，每个绑定需要从复合分支所有匹配模式中获取相同类型的值。这确保了，不管复合分支的哪一部分匹配，分支体中的代码总能够访问绑定的值，而它们具有相同的类型。
```swift
let stillAnotherPoint = (9, 0)
switch stillAnotherPoint {
case (let distance, 0), (0, let distance):
    print("On an axis, \(distance) from the origin")
default:
    print("Not on an axis")
}
// Prints "On an axis, 9 from the origin"
```

上面的`case`有两种模式：`(let distance, 0)`匹配x轴上的点，`(0, let distance)`匹配y轴上的点。两个模式都包含`distance`的绑定，并且`distance`在两种匹配中都是整数---这意味着，分支体中的代码总能访问`distance`的值。

## 控制跳转语句

*控制跳转语句* 通过将控制流从一段代码跳到另一段代码，来改变代码的执行顺序。Swift有五个控制跳转语句：
* `continue`
* `break`
* `fallthrough`
* `return`
* `throw`

下面会讨论`continue`，`break`和`fallthrough`语句。`return`语句将在[函数](Functions.md)中讨论，`throw`语句将在[用抛出功能传递错误](Error_Handling.md#用抛出功能传递错误)中讨论。

### Continue

`continue`语句让一个循环停止当前动作，并从头开始下一次循环遍历。它说“当前循环遍历已经完事了”，并且不离开循环。

下面的例子，从一个小写字符串中移除所有的元音和空格，来创建一个拼图短语：
```swift
let puzzleInput = "great minds think alike"
var puzzleOutput = ""
let charactersToRemove: [Character] = ["a", "e", "i", "o", "u", " "]
for character in puzzleInput {
    if charactersToRemove.contains(character) {
        continue
    }
    puzzleOutput.append(character)
}
print(puzzleOutput)
// Prints "grtmndsthnklk"
```

上面的代码中，当任何时候匹配了元音或空格，代码会用`continue`关键字，使当前循环立即结束，然后跳转到下一次遍历的开头。

### Break

`break`语句立即结束整个控制流的执行。当想提前终止`switch`或循环时，可以在`switch`或循环中用上`break`语句。

#### 循环语句中的Break

当在循环中使用时，`break`会立即终止执行循环，并将控制流跳转到循环关闭括号之后的代码。当前循环的遍历不会被进一步执行或开始。，

#### Switch语句中的Break

在`switch`语句中使用`break`时，`break`会使`switch`语句立即停止执行，并将控制流转到`switch`语句反括号(`}`)后面。

这个操作可以用来匹配并忽略一个或多个`switch`语句的分支。因为`swift`语句是彻底的且不允许空分支，有时候需要特地地匹配并忽略一个分支，从而使意图变得明显。你可以在想要忽略的分支里写上`break`语句作为该分支的全部。当`switch`语句匹配了该分支，该分支中的`break`语句会立即终止执行`switch`语句。

> 注意：
只包含注释的分支会报编译错误。注释不是语句，且不会使`switch`语句被忽略。请请`break`语句终止`switch`语句。

下面的例子切换`Character`值，判断该值是否代表四个语言中一个语言的数字符号。为了简洁，一行`switch`分支包含了多个值：
```swift
let numberSymbol: Character = "三"  // Chinese symbol for the number 3
var possibleIntegerValue: Int?
switch numberSymbol {
case "1", "١", "一", "๑":
    possibleIntegerValue = 1
case "2", "٢", "二", "๒":
    possibleIntegerValue = 2
case "3", "٣", "三", "๓":
    possibleIntegerValue = 3
case "4", "٤", "四", "๔":
    possibleIntegerValue = 4
default:
    break
}
if let integerValue = possibleIntegerValue {
    print("The integer value of \(numberSymbol) is \(integerValue).")
} else {
    print("An integer value could not be found for \(numberSymbol).")
}
// Prints "The integer value of 三 is 3."
```

这个例子判断`numberSymbol`是否是拉丁，阿拉伯，中文或泰语中的1-4字符。如果匹配上了，`switch`中的一个分支会给`Int?`可选变量`possibleIntegerValue`设置一个合适的值。

`switch`语句完成时，例子会用一个可选值绑定来判断是否找到一个值。由于是可选类型，变量`possibleIntegerValue`有一个隐式初始值`nil`，所以只有当变量`possibleIntegerValue`被`switch`语句四个分支中的一个设置值后，可选绑定才会成功。

因为上例中列出每个可能的`Character`值是不现实的，所以用`default`分支匹配其他所有字符。`default`分支不需要执行任何操作，所以只写了`break`语句作为分支主体。一旦匹配了`default`，`break`语句会终止执行`switch`语句，代码会从`if let`语句继续执行。

### 穿透

在Swift中，`switch`语句不会穿透每个分支的底部到下一个分支。也就是，当第一个匹配的分支完成时整个`switch`语句也会执行完成。与此相反，C语言需要你在`switch`的每个分支底部显式插入`break`语句以阻止穿透。避免默认穿透意味着Switch的`switch`语句比同样功能的C代码更简洁明了，而且因此防止了意外执行多个分支。

如果你需要C类型的穿透行为，你可以选择使用`fallthrough`关键字逐个添加此行为。下面的例子用`fallthrough`创建数组的文本描述：
```switch
let integerToDescribe = 5
var description = "The number \(integerToDescribe) is"
switch integerToDescribe {
  case 2, 3, 5, 7, 11, 13, 17, 19:
    description += " a prime number, and also"
    fallthrough
  default:
    description += " an integer."
}
print(description)
// Prints "The number 5 is a prime number, and also an integer."
```

这个例子定义了一个字符串变量`description`，并指定初始值。然后函数用`switch`语句考量`integerToDescribe`的值。如果`integerToDescribe`的值是列表素数中之一，函数会向`description`尾部追加文本，表明该数字是素数。然后用`fallthrough`关键字穿透到`default`分支。`default`分支给`description`追加额外文本，然后`switch`语句结束。

如果`integerToDescribe`的值不在素数列表中，则不匹配第一个`switch`分支。因为没有其他指定分支，`integerToDescribe`会匹配`default`分支。

当`switch`语句执行完毕，会用`print(_:separator:terminator:)`函数打印数字描述。这个例子中，数字`5`被正确地识别为素数。

> 注意：
`fallthrough`关键字不会检查其引起穿透到的`switch`分支的条件。`fallthrough`关键字只是使代码执行直接移到下一个分支（`default`分支）代码块中。

#### 标签语句

在Swift中，可以把循环和条件语句嵌套到其他循环和条件语句中，从而创建复杂的控制流结构。然而，循环和条件语句并不都能使用`break`语句提前终止执行。因此，有时候显式指明你想用`break`终止哪一个循环或条件语句比较有用。类似地，如果你有多个嵌套循环，明确`continue`语句影响哪个循环会很有用。

为达到这些目标，你可以用***语句标签***来标记一个循环语句或条件语句。使用条件语句，你可以用一个语句标签配合`break`语句终止标签语句的执行。使用循环语句，你可以用一个语句标签配合`break`或`continue`语句终止或继续标签语句。

通过在语句起始关键字同一行放置标签，后跟冒号，表明该语句是标签语句。下面`while`循环的此语法示例，原理与所有循环和`switch`语句相同：
```swift
  `label name`: while `condition` {
    `statements`
  }
```

下面的例子用`break`和`continue`语句，配合标签`while`循环，改编前面章节中看到过的*蛇和梯子*游戏。这一次，游戏有一个额外规则：
* 为了赢，必须正好落在方块25上。

如果某一次掷骰子使你超出了方块25，你必须重新掷骰子直到你得到让你落在方块25上的数字。

棋盘跟以前一样：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/snakesAndLadders_2x.png" alt="标签语句" width="600"/>
</p>

用与以前相同的方法初始化`finalSquare`，`board`，`square`和`diceRoll`：
```swift
let finalSquare = 25
var board = [Int](repeating: 0, count: finalSquare + 1)
board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
var square = 0
var diceRoll = 0
```

本版本的游戏，使用`while`循环和`switch`语句来实现游戏逻辑。`while`循环有一个语句标签`gameLoop`，表明循环是蛇与梯子游戏的主循环。

`while`循环的循环条件是`while square != finalSquare`，反映出你必须正好落在方块25上：
```swift
gameLoop: while square != finalSquare {
  diceRoll += 1
  if diceRoll == 7 { diceRoll = 1 }
  switch square + diceRoll {
  case finalSquare:
    // diceRoll will move us to the final square, so the game is over
    break gameLoop
  case let newSquare where newSquare > finalSquare:
    // diceRoll will move us beyond the final square, so roll again
    continue gameLoop
  default:
    // this is a valid move, so find out its effect
    square += diceRoll
    square += board[square]
  }
}
print("Game over!")
```

每次循环的开始都会掷骰子。循环用一个`switch`语句考量移动结果来决定移动是否被允许，而不是直接移动玩家：
* 如果掷骰子将把玩家移到最后一个方块，则游戏结束。`break gameLoop`语句将控制跳转到`while`循环外的第一行代码，结束游戏。
* 如果掷骰子将把玩家移到超出最后一个方块，则该移动无效，玩家需要重新掷骰子。`continue gameLoop`语句结束当前循环，重新开始下一次循环。
* 其他所有情况下，掷骰子有效。玩家移动`diceRoll`个方块，游戏逻辑检查所有的蛇和梯子。然后循环结束，控制流返回到`while`循环条件，决定是否需要下一回合。

> 注意：
如果上面例子中`break`语句没有使用`gameLoop`标签，则会终止并跳出`switch`语句，而不是`while`语句。用`gameLoop`标签使要终止哪一个控制语句变得清晰。
当调用`continue gameLoop`跳转到下一次循环时，并不一定非要使用`gameLoop`标签。在游戏中，只有一个循环，对于`continue`语句将要影响哪一个循环并没有歧义。然而，使用`gameLoop`标签配合`continue`语句并无任何害处。这么做能与`break`语句配合标签的使用和保持一致，从而使游戏逻辑更清晰，易读易懂。

## 提前退出

一个`guard`语句，像`if`语句一样，根据表达式的布尔值来执行语句。用`guard`语句来确保为了执行`guard`语句后面的代码，一个条件必须为真。与`if`语句不同的是，`guard`语句总是有`else`子句——如果条件不是真，`else`子句的代码会被执行：
```swift
func greet(person: [String: String]) {
  guard let name = person["name"] else {
      return
  }

  print("Hello \(name)!")

  guard let location = person["location"] else {
    print("I hope the weather is nice near you.")
    return
  }

  print("I hope the weather is nice in \(location).")
}

greet(person: ["name": "John"])
// Prints "Hello John!"
// Prints "I hope the weather is nice near you."
greet(person: ["name": "Jane", "location": "Cupertino"])
// Prints "Hello Jane!"
// Prints "I hope the weather is nice in Cupertino."
```

如果`guard`语句条件得到满足，`guard`语句括号后面的代码继续执行。任何用可选值绑定被指定值的变量或常量都适用于`guard`语句后面的代码。

如果该条件不满足，`else`分支的代码将被执行。该分支必须跳转控制流从而结束`guard`语句所在的代码块。用跳转语句如`return`、`break`、`continue`或`throw`可以做到这个，或者可以调用不返回的函数或方法，如`fatalError(_:file:line:)`。

与使用`if`语句进行相同的检查相比，用`guard`语句能改善代码可读性。它使你无需将通常需要执行的代码写在`else`代码块中，且让处理相反需求的代码紧挨需求。

## 检查API可用性

Swift内置支持检查API可用性，只能确保你不会意外使用给定部署目标不支持的API。

编译器使用SDK中的可用性信息验证代码中用到的所有API是否适用于你的工程所指定的部署目标。如果你试图使用不支持的API，Swift会在编译时报错。

在`if`或`guard`语句中使用*可用性条件*，来有条件地执行代码块，执行与否取决于你使用的API在运行时是否可用。编译器使用可用性条件中的信息验证代码块中的API是否可用。
```swift
if #available(iOS 10, macOS 10.12, *) {
  // Use iOS 10 APIs on iOS, and use macOS 10.12 APIs for macOS
} else {
  // Fall back to earlier iOS and macOS APIs
}
```

上面的可用性信息指定，在iOS中，`if`语句主体只在iOS10及以上版本执行；在macOS中，只在10.12及以上版本执行。最后一个参数，`*`，是必须的，它表明在任何其他平台上，`if`语句主体在你指定的部署目标中最低版本的部署目标上执行。

在通用格式中，可用性条件接受一个平台名称和版本的列表。用平台名称，如`iOS`、`macOS`、`watchOS`和`tvOS`——作为完整列表，参见[声明属性](../Language_Reference/Attributes.md#声明属性)。除了指定iOS 8或macOS 10.10这样的主要版本数字，也可以指定iOS 11.2.6和macOS 10.13.3这样的次要版本数字。
```swift
if #available(`platform name` `version`, `...`, *) {
  `statements to execute if the APIs are available`
} else {
  `fallback statements to execute if the APIs are unavailable`
}
```

[< 集合类型](Collection_Types.md) || [函数 >](Functions.md)
