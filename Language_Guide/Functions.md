# 函数

函数是执行特定任务的自包含代码块。你为函数指定一个标识其功能的名字，需要的时候，这个名字被用于调用该函数执行相关任务。

Swift的统一函数语法足够灵活，从简单的C类型无参数函数，到复杂的Objective-C类型有实参名和形参标签的函数，都能表达出来。实参可以提供默认值来简化函数调用，且可以被用作能够在函数执行完成时，修改被传入的变量的输入输出参数。

Swift中的每个函数都有一个类型，该类型由函数的实参类型和返回类型组成。你可以像使用Swift中其他类型一样使用函数类型，这使将函数当作参数传递给另一个函数，和用函数返回函数变得简单。函数还可以被写在其他函数里，从而把有用的功能概括到嵌套函数块中。

## 定义和调用函数

当定义函数时，可以可选地定义一个或多个命名的，有类型的值作为函数的输入，这些值被称为*形式参数*。也可以可选地定义一个值，函数执行完毕会返回该值，被称为*返回类型*。

每个函数都有一个描述函数执行的任务的函数名称。要使用函数，调用该函数名称，并传入符合形式参数类型的输入值（被称为*实际参数*）。一个函数的实际参数顺序必须与形式参数的顺序一致。

下面例子中的函数名为`greet(person:)`（问候），因为这是该函数要做的事——接受一个人名作为输入，然后问候这个人。为了实现这个，定义一个输入形参——一个`String`值`person`——返回一个`String`类型包含对那个人问候信息的字符串值：
```swift
func greet(person: String) -> String {
  let greeting = "Hello, " + person + "!"
  return greeting
}
```
所有这新信息被定义打包到以`func`关键字开头的函数定义中。用*返回箭头*（`->`，短横线+大于号）指明函数的返回类型，箭头后面接上返回类型：
```swift
print(greet(person: "Anna"))
// Prints "Hello, Anna!"
print(greet(person: "Brian"))
// Prints "Hello, Brian!"
```

通过向`person`形参标签后面传入一个字符串值来调用`greet(person:)`函数，例如`greet(person: "Anna")`。因为函数返回一个字符串值，如上所示，`greet(person:)`可以被包含在`print(_:separator:terminator:)`函数中，以打印该字符串并查看返回值。

> 注意：
函数`print(_:separator:terminator:)`第一个实参没有标签，其余的形参是可选的，因为它们有默认值。这些函数语法的变化在中[函数形参标签和实参名称](#函数形参标签和实参名称)和中[实参默认值](#实参默认值)有讨论。

函数`greet(person:)`的函数体始于定义一个新的字符串常量`greeting`，并设置一个简单的问候信息。然后，用`return`关键字将问候信息传到函数外。在`return greeting`这行代码中，函数结束执行并返回当前的`greeting`值。

可以用不同的输入值多次调用`greet(person:)`函数。上面的例子显示了如果用输入值`Anna`和输入值`Brian`调用会发生什么。函数在每个情况返回一个定制的问候句。

为了简化这个函数，可以把创建信息并入返回语句变成一行：
```swift
func greetAgain(person: String) -> String {
    return "Hello again, " + person + "!"
}
print(greetAgain(person: "Anna"))
// Prints "Hello again, Anna!"
```

## 函数参数和返回值

在Swift中，函数的参数和返回值异常灵活。你可以定义从拥有单个未命名参数的简单工具函数，到拥有有表现力参数名和不同参数选项的复杂函数中的任何函数。

### 无参数函数

函数并不一定要定义输入参数。下面是一个没有参数的函数，任何时候调用该函数返回相同的`String`信息：
```swift
func sayHelloWorld() -> String {
    return "hello, world"
}
print(sayHelloWorld())
// Prints "hello, world"
```

即使不接受任何参数，函数的定义仍然需要在函数名后面写上括号。调用函数的时候，函数名后面仍然写上一对空括号。

### 多参数函数

函数可以有多个输入参数，把它们写在函数括号中，用逗号隔开。

这个函数接受一个人名和一个是否被问候过作为参数，返回合适的问候：
```swift
func greet(person: String, alreadyGreeted: Bool) -> String {
    if alreadyGreeted {
        return greetAgain(person: person)
    } else {
        return greet(person: person)
    }
}
print(greet(person: "Tim", alreadyGreeted: true))
// Prints "Hello again, Tim!"
```

通过传入一个被标记为`person`的`String`实参和一个被标记为`alreadyGreeted`的`Bool`实参到括号里，用逗号隔开来调用`greet(person:alreadyGreeted:)`函数。注意这个函数与上例中展示的`greet(person:)`函数不同。即使这两个函数都有以`greet`为开头的名称，`greet(person:alreadyGreeted:)`含有接受两个参数而`greet(person:)`只接受一个。

### 无返回值函数

函数并不一定要定义返回值。下面是一个打印自身`String`值而不是返回的`greet(person:)`版本：
```swift
func greet(person: String) {
    print("Hello, \(person)!")
}
greet(person: "Dave")
// Prints "Hello, Dave!"
```

因为不需要返回值，函数定义中不包括返回箭头(`->`)或返回类型。

> 注意：
> 严格地讲，这个版本的`greet(person:)`函数仍然返回一个值，即使没有定义返回值。没有定义返回类型的函数返回一个特殊类型`Void`的值。它是一个简单的元组，写作`()`。

当调用该函数时，返回值会被忽略：
```swift
func printAndCount(string: String) -> Int {
    print(string)
    return string.count
}
func printWithoutCounting(string: String) {
    let _ = printAndCount(string: string)
}
printAndCount(string: "hello, world")
// prints "hello, world" and returns a value of 12
printWithoutCounting(string: "hello, world")
// prints "hello, world" but does not return a value
```

第一个函数`printAndCount(string:)`，打印一个字符串，然后作为一个`Int`返回其字符数。第二个函数`printWithoutCounting(string:)`，调用第一个函数，但是忽略其返回值。当调用第二个函数时，依然是第一个函数打印信息，但是没有使用其返回值。

> 注意：
> 返回值可以被忽略，但是一个将要返回一个值的函数必须返回一个值。一个定义了返回类型的函数，不允许函数执行到底部是不返回一个值，且这么做会导致编译时错误。

### 多返回值函数

可以把元组类型用做函数的返回类型，以复合值的形式返回多个值。

下面的例子定义了一个函数`minMax(array:)`，它找出一个`Int`值数组中最小和最大的数字：
```swift
func minMax(array: [Int]) -> (min: Int, max: Int) {
    var currentMin = array[0]
    var currentMax = array[0]
    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}
```

函数`minMax(array:)`返回一个含有两个`Int`值的元组。这些值被标记为`min`和`max`，当查询函数返回值的时候可以用名字访问。

`minMax(array:)`的函数体开始把两个要用到的变量`currentMin`和`currentMax`设置为数组的第一个整数。然后遍历数组中剩下的值，分别检查每个值是否小于或大于`currentMin`和`currentMax`。最后，最大值和最小值以`Int`元组的形式返回。

因为元组的成员值被命名为函数返回类型的一部分，所以可以用点语法获取最小和最大值：
```swift
let bounds = minMax(array: [8, -6, 2, 109, 3, 71])
print("min is \(bounds.min) and max is \(bounds.max)")
// Prints "min is -6 and max is 109"
```

注意函数返回元组的时候，不需要命名元组成员，因为成员名作为函数返回类型的一部分已经被指定。

#### 可选元组返回类型

如果一个函数返回的元组类型有可能完全没有值，可以用*可选*元组类型表明整个元组可以为`nil`。通过在元组类型闭括号后面写上问号，如`(Int, Int)?`或`(String, Int, Bool)`，来返回一个可选元组类型。

> 注意：
> 可选元组如`(Int, Int)?`与包含可选值的元组如`(Int?, Int?)`是不同的。可选元组整个元组是可选的，而不是元组中单独的值是可选的。

上面的`minMax(array:)`函数返回一个包含两个`Int`值的元组。但是，函数并没有对传入的数组执行任何安全检查。如果实参`array`是一个空数组，函数`minMax(array:)`，如上面所定义的，当尝试访问`array[0]`时将会触发一个运行时错误。

为了安全地处理空数组，让`minMax(array:)`返回可选元组类型，并且当数组是空是返回一个`nil`：
```swift
func minMax(array: [Int]) -> (min: Int, max: Int)? {
    if array.isEmpty { return nil }
    var currentMin = array[0]
    var currentMax = array[0]
    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}
```

可以用可选绑定检查这个版本的`minMax(array:)`函数返回的是真实的元组值还是`nil`：
```swift
if let bounds = minMax(array: [8, -6, 2, 109, 3, 71]) {
    print("min is \(bounds.min) and max is \(bounds.max)")
}
// Prints "min is -6 and max is 109"
```

### 隐式返回的函数

如果函数的整个函数体是单个表达式，则函数隐式返回该表达式。例如，下面的两个函数有相同的行为：
```swift
func greeting(for person: String) -> String {
    "Hello, " + person + "!"
}
print(greeting(for: "Dave"))
// Prints "Hello, Dave!"

func anotherGreeting(for person: String) -> String {
    return "Hello, " + person + "!"
}
print(anotherGreeting(for: "Dave"))
// Prints "Hello, Dave!"
```

函数`greeting(for:)`的全部定义是其返回的问候信息，这意味着可以用这种简短形式。另一个函数`anotherGreeting(for:)`返回相同的问候信息，与长函数一样用`return`关键字。任何只是简单返回一行代码的函数，可以省略`return`。

你将在[简写Getter方法声明](Properties.md#简写Getter方法声明)中看到，属性getter方法也可以用隐式返回。

## 函数形参标签和实参名称

函数每个参数都有一个形参标签和一个实参名称。当调用函数时会用到形参标签；每个实参都写在函数调用中，前面放一个形参标签。实参名称在函数实现中用到。默认地，参数把形参名当作形参标签。
```swift
func someFunction(firstParameterName: Int, secondParameterName: Int) {
  // In the function body, firstParameterName and secondParameterName
  // refer to the argument values for the first and second parameters.
}
someFunction(firstParameterName: 1, secondParameterName: 2)
```

所有的参数必须有唯一的名称。虽然多个参数可能有相同的形参标签，唯一的形参标签有助于使代码有更强可读性。

### 指定形参标签

将形参标签写在实参名称前面，用空格隔开：
```swift
func someFunction(argumentLabel parameterName: Int) {
  // In the function body, parameterName refers to the argument value
  // for that parameter.
}
```

下面是`greet(person:)`函数的变异版，它接受一个人名和家乡名然后返回一句问候：
```swift
func greet(person: String, from hometown: String) -> String {
    return "Hello \(person)!  Glad you could visit from \(hometown)."
}
print(greet(person: "Bill", from: "Cupertino"))
// Prints "Hello Bill!  Glad you could visit from Cupertino."
```

使用形参标签可以以一种更富表达力，像语句一样的方式调用函数，同时提供了一个意图清晰明显的函数体。

### 省略形参标签

如果你不需要一个参数的形参标签，将该参数的显式形参标签替换为下划线(`_`)。
```swift
func someFunction(_ firstParameterName: Int, secondParameterName: Int) {
    // In the function body, firstParameterName and secondParameterName
    // refer to the argument values for the first and second parameters.
}
someFunction(1, secondParameterName: 2)
```

如果一个参数有一个形参标签，则调用函数是该形参*必须*被标记。

### 实参默认值

通过在参数类型后指定一个值可以为函数的参数定义默认值。如果定义了默认值，则调用函数时，可以省略该参数。
```swift
func someFunction(parameterWithoutDefault: Int, parameterWithDefault: Int = 12) {
    // If you omit the second argument when calling this function, then
    // the value of parameterWithDefault is 12 inside the function body.
}
someFunction(parameterWithoutDefault: 3, parameterWithDefault: 6) // parameterWithDefault is 6
someFunction(parameterWithoutDefault: 4) // parameterWithDefault is 12
```

把没有默认值的参数放在函数参数列表的开头，在有默认值参数的前面。一般没有默认值的参数对函数有更重要的意义–––不管是否省略默认参数，这么写更容易识别调用是相同的函数（下面详细说明这句的意思）。
> 译者：
> 先看同一功能函数的两种写法：
> ```
> // 有默认值的参数在参数列表前面
> func hello(a: String = "a", b: String, c: String) {}
> hello(a: "000", b: "000")
> hello(b: "000")
> // 上面两个应该是同一函数，但是由于第一个参数不同，一下子很容易错误认为是不同的函数
> // 写成下面就不会出错了
>
> // 有默认值的参数在参数列表后面
> func hello(b: String, c: String, a: String = "a") {}
> hello(b: "000", c: "000", a: "000")
> hello(b: "000", c: "000")
> // 函数调用的前两个参数相同，考虑到a有默认值，与上面相比，相对比较容易地就可以识别出这两个调用是同一个函数
>
> 因为默认参数往往可以省略，为避免出现上面那种调用情况，将默认参数写在参数列表后面比较好
> ```

### 可变参数

*可变参数* 接受指定类型的零个或多个值。用可变参数表明，当调用函数时，可以传入不定数量的参数。通过在参数类型名后面插入三个点号(`...`)来编写可变参数。

被传入给函数的可变参数，可以在函数体中以适当类型的数组形式被使用。例如，一个名为`numbers`，类型为`Double`的可变参数，在函数体中可以被当作一个`[Double]`类型的名为`numbers`的常量数组使用。

下面的例子，为任意长的数字列表进行计算平均值：
```swift
func arithmeticMean(_ numbers: Double...) -> Double {
    var total: Double = 0
    for number in numbers {
        total += number
    }
    return total / Double(numbers.count)
}
arithmeticMean(1, 2, 3, 4, 5)
// returns 3.0, which is the arithmetic mean of these five numbers
arithmeticMean(3, 8.25, 18.75)
// returns 10.0, which is the arithmetic mean of these three numbers
```

> 注意：
> 一个函数最多只能有一个可变参数。

### 输入输出实参

函数实参默认是常量。尝试在函数体中改变实参的值会导致编译时错误。这意味着，你不能不会因为失误而改变实参的值。如果你希望函数能修改实参值，且想当函数结束时保持这个改变，将该实参定义为*输入输出参数*。

在实参类型前面写上`inout`关键字来编写输入输出实参。一个输入输出实参有一个被传入给函数的值，该值被函数修改后，被传出函数替换原始值。更详细地关于输入输出参数的行为和相关编译优化的讨论，参见[输入输出实参](../Language_Reference/Declarations.md#输入输出实参)。

你只能把变量作为输入输出参数。不能把常量或字面量用作输入输出参数，因为无法修改常量和字面量。当把参数当作输入输出参数传入时，在变量名前面写上(`&`)符号，表明参数可以被函数修改。

> 注意：
> 输入输出参数不可以有默认值，可变参数不能被标记为`inout`。

下面是一个例子，函数`swapTwoInts(_:_:)`，接受两个输入输出参数`a`和`b`：
```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

`swapTwoInts(_:_:)`简单地交换`a`和`b`的值。函数通过把`a`的值存入临时常量`temporaryA`中，用`b`给`a`赋值，然后用`temporaryA`给`b`赋值执行交换。

可以用两个`Int`类型的变量调用`swapTwoInts(_:_:)`函数。注意当传入给`swapTwoInts(_:_:)`函数时，`someInt`和`anotherInt`前面带上了`&`符号：
```swift
var someInt = 3
var anotherInt = 107
swapTwoInts(&someInt, &anotherInt)
print("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
// Prints "someInt is now 107, and anotherInt is now 3"
```

上面的例子展示了，`someInt`和`anotherInt`的原始值被`swapTwoInts(_:_:)`修改，即使他们最初是被定义在函数外面。

> 注意：
> 输入输出参数与从函数返回的值不同。上面`swapTwoInts`的例子没有定义返回类型或返回值，但是仍然修改了`someInt`和`anotherInt`的值。输入输出实参是让函数影响外部的另一个方式。

## 函数类型

每个函数都有一个由参数类型和返回类型组成的*函数类型*。

举个例子：
```swift
func addTwoInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}
func multiplyTwoInts(_ a: Int, _ b: Int) -> Int {
    return a * b
}
```

这个例子定义了两个简单的算术函数`addTwoInts`和`multiplyTwoInts`。每个函数接受两个`Int`值，返回一个`Int`值，返回值是相应算术计算的结果。

这两个函数的类型都是`(Int, Int) -> Int`。可以读作：

“一个接受两个参数的函数，参数类型都是`Int`，且返回一个`Int`值。”

举另一个例子，一个没有参数和返回值的函数：
```swift
func printHelloWorld() {
    print("hello, world")
}
```

这个函数的类型是`() -> Void`，或“一个没有参数和返回值的函数”。

### 函数类型应用

像使用Swift中其他类型一样使用函数类型。举个例子，可以定义一个函数类型的常量或变量，然后为该变量赋值一个适当的函数：
```swift
var mathFunction: (Int, Int) -> Int = addTwoInts
```

这可以读作：

“定义一个变量`mathFunction`，其类型是接受两个`Int`值和返回一个`Int`值的函数类型，让这个变量引用函数`addTwoInts`。”

函数`addTwoInts(_:_:)`与变量`mathFunction`有着相同的类型，所以Swift的类型检查允许这个赋值。

可以用`mathFunction`调用被赋值的函数：
```swift
print("Result: \(mathFunction(2, 3))")
// Prints "Result: 5"
```

有着相同类型的不同函数可以被赋值给同一个变量，与非函数类型一个样：
```swift
mathFunction = multiplyTwoInts
print("Result: \(mathFunction(2, 3))")
// Prints "Result: 6"
```

如其他类型，当用函数给常量或变量赋值时，可以把类型识别的工作交给Swift：
```swift
let anotherMathFunction = addTwoInts
// anotherMathFunction is inferred to be of type (Int, Int) -> Int
```

### 函数类型作为参数类型

可以把诸如`(Int, Int) -> Int`的函数类型当作另一个函数的参数。这允许你可以把函数实现的某些方面留给函数的调用者提供。

下面是一个例子，打印上面数学函数的结果：
```swift
func printMathResult(_ mathFunction: (Int, Int) -> Int, _ a: Int, _ b: Int) {
    print("Result: \(mathFunction(a, b)")
}
printMathResult(addTwoInts, 3, 5)
// Prints "Result: 8"
```

这个例子中定义了一个函数`printMathResult(_:_:_:)`，有三个参数。第一个参数`mathFunction`，类型为`(Int, Int) -> Int`。你可以传入任何该类型的函数当作第一个参数的实参。第二个和第三个参数`a`和`b`，都是`Int`类型。它们被用做数学函数的输入值。

当`printMathResult(_:_:_:)`被调用，`addTwoInts(_:_:)`函数和整数值`3`和`5`被传入。该函数用值`3`和`5`调用提供的函数，然后打印结果`8`。

`printMathResult(_:_:_:)`的功能是打印调用适当类型函数的结果。该函数实现了什么并不重要——重要的是该函数属于正确的类型。这允许`printMathResult(_:_:_:)`把一些功能以一种安全的方式交给其调用者。

### 函数类型作为返回类型

可以把函数类型当作另一函数的返回类型。要这么做，在函数的返回箭头(`->`)后面写上完整的函数类型。

下一个例子定义了两个简单函数`stepForward(_:)`和`stepBackward(_:)`。函数`stepForward(_:)`返回一个比输入值大一的值，函数`stepBackward(_:)`返回一个比输入值小一的值。两个函数的类型都是`(Int) -> Int`：
```swift
func stepForward(_ input: Int) -> Int {
    return input + 1
}
func stepBackward(_ input: Int) -> Int {
    return input - 1
}
```

下面的函数`chooseStepFunction(backward:)`，返回类型为`(Int) -> Int`。该函数根据布尔值`backward`返回函数`stepForward(_:)`或`stepBackward(_:)`：
```swift
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    return backward ? stepForward : stepBackward
}
```

你现在可以用`chooseStepFunction(backward:)`来获取一个前进或后退的函数：
```swift
var currentValue = 3
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
// moveNearerToZero now refers to the stepBackward() function
```

上面的例子决定为了使变量`currentValue`逐渐趋于零，是否需要一个加一或减一。`currentValue`初始值为`3`，意味着`currentValue > 0`返回`true`，从而导致`chooseStepFunction(backward:)`返回`stepBackward(_:)`函数。返回函数的引用被储存在常量`moveNearerToZero`中。

现在`moveNearerToZero`引用了正确的函数，它可被用与计数到零：
```swift
print("Counting to zero:")
// Counting to zero:
while currentValue != 0 {
    print("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
print("zero!")
// 3...
// 2...
// 1...
// zero!
```

## 嵌套函数

你在本章节看到的所有函数都是*全局函数*，它们被定义在全局。你也可以把函数定义在另一个函数的函数体里面，这种函数被称为*嵌套函数*。

嵌套函数默认对外部不可见，但是可以被封闭的函数调用。一个封闭函数可以返回一个嵌套函数，以允许该嵌套函数被其他作用域所用。

可以用嵌套函数和返回嵌套函数重写上例中的`chooseStepFunction(backward:)`：

```swift
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    func stepForward(input: Int) -> Int { return input + 1 }
    func stepBackward(input: Int) -> Int { return input - 1 }
    return backward ? stepBackward : stepForward
}
var currentValue = -4
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
// moveNearerToZero now refers to the nested stepForward() function
while currentValue != 0 {
    print("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
print("zero!")
// -4...
// -3...
// -2...
// -1...
// zero!
```

[< 控制流](Control_Flow.md) || [闭包 >](Closures.md)
