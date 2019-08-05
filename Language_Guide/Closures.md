# 闭包

闭包是可以在代码中传递并使用的独立的功能块。Swift中的闭包与C和Objective-C中的`Block`类似，与其他语言中的拉姆达表达式相似。

闭包可以从其被定义的上下文中，捕获并储存任何变量和常量的引用。这被称为***收起***这些常量和变量。Swift帮你管理所有捕获的内存。

> 注意：
> 如果不懂得捕获这个概念，先别着急。在[值的捕获](#值的捕获)中有解释。

如[函数](Functions.md)中所述，全局和嵌套函数实际上是特殊的闭包。以下三种形式之一，是闭包：
* 全局函数是有名称的闭包，且不捕获任何值
* 嵌套函数是有名称的闭包，且可以从其他函数作用域中捕获值
* 闭包表达式是以轻量语法编写的无名称闭包，可以从其被包围的上下文中捕获值

Swift的闭包表达式具有简洁，清晰的风格，在常用场景中，针对鼓励代码简洁和无杂乱化语法进行优化（吹牛的话不会翻译）。这些优化包括：
* 从上下文中推导参数类型和返回值类型
* 从单个表达式闭包隐式返回
* 速记参数名称
* 尾随闭包语法

## 闭包表达式

如[嵌套函数](Functions.md#嵌套函数)中所述，嵌套函数是一种命名和定义独立功能块，作为更大函数一部分的便利方法。但是，有时候编写较短的没有声明或名称的类函数结构比较有用。当函数或方法需要一个或多个函数作为参数时，这个做法尤为有用。

***闭包表达式*** 是编写内联闭包的简洁，专注的语法。闭包表达式提供了几种以一种简洁但不失清晰度和意图的方法编写闭包的语法优化。下面例子中的闭包表达式通过在遍历中重定义`sorted(by:)`方法，解释这些优化，每个表达式功能相同，但是更简洁。

### 排序方法

Swift标准库提供了一个`sorted(by:)`方法，它基于一个你提供的闭包的输出值，对一个已知类型的数组进行排序。一旦完成排序过程，`sorted(by:)`返回一个与旧数组类型和大小相同，元素排完序的新数组。`sorted(by:)`方法不修改原数组。

下面的闭包表达式用`sorted(by:)`方法按照逆字母顺序对一个字符串数组进行排序。这是原始数组：
```swift
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
```

`sorted(by:)`方法接受两个与数组类型相同的参数，返回一个布尔值，表明在排序比较中，第一个值否会排在第二个值的后面或前面。如果第一个值应该排在第二个值的前面，则排序闭包需要返回`true`，否则返回`false`。

这个例子排序一个字符串数组，排序闭包需要是`(String, String) -> Bool`类型的函数。

一个提供排序闭包的方法是，编写该类型的函数，然后将函数作为参数传递给`sorted(by:)`方法：
```swift
func backward(_ s1: String, _ s2: String) -> Bool {
  return s1 > s2
}
var reversedNames = names.sorted(by: backward)
// reversedNames is equal to ["Ewa", "Daniella", "Chris", "Barry", "Alex"]
```

如果第一个字符串(`s1`)比第二个字符串(`s2`)大，`backward(_:_:)`函数返回`true`，表明在排完序的数组中`s1`应该出现在`s2`的前面。对于字符串中的字符，“大于”的意思是“在字母表中靠后”。这意味着字母`B`比字母`A`要大，字符串`Tom`要比字符串`Tim`大。这里逆序排序，`"Barry"`在`"Alex"`前面，等等。

但是，对比可以写成简单表达式（`a > b`），上面是一种冗长的写法。这个例子中，应该首选用闭包表达式语法，写内联排序闭包。

### 闭包表达式语法

闭包表达式语法有如下通用格式：
```swift
  { (`parame`) -> `return type` in  
      `statements`  
  }
```

闭包表达式语法中的参数可以是`in-out`型参数，但是不能有默认值。也可用可变参数。元组也可被用作参数类型和返回类型。

下面的例子展示了上例中`backward(_:_:)`函数的闭包表达式版本：
```swift
  reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in
    return s1 > s2
  })
```

注意这个内联闭包的参数和返回值类型与`backward(_:_:)`函数相同。在这两个例子中，被写作`(s1: String, s2: String) -> Bool`。但是，对于闭包表达式，参数和返回值类型写在小括号里面，而不是外面。

闭包主体的开头有`in`关键字引入。这个关键字表明闭包的参数和返回值定义已经结束，闭包主体要开始。

因为闭包的主体很短，上面的代码可以写成一行：
```swift
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in return s1 > s2})
```

这说明对`sorted(by:)`方法的整体调用保持不变。一对括号括起了方法的所有参数。但，此时参数是内联闭包的。

### 从上下文推导类型

因为排序闭包被作为参数传递给方法，Swift可以推导参数和返回值的类型。`sorted(by:)`方法被字符串数组调用，所以其参数应该是`(String, String) -> Bool`类型的函数。这意味着不必将`(String, String)`和`Bool`写成闭包表达式定义的一部分。因为所有的类型都可以被推导出来，返回值箭头(`->`)和参数的括号都可以省略：
```swift
reversedNames = names.sorted(by: { s1, s2 in return s1 > s2 })
```
当把闭包作为内联闭包表达式传递给函数或方法时，总能推导出参数和返回值的类型。因此，但把闭包用作函数或方法的参数是，你永远不需要写内联闭包的完整格式。

尽管如此，如果你愿意你可以使类型明确，如果这能让读者或代码避免歧义，应该鼓励这么做。`sorted(by:)`方法的例子中，从排序可以看出闭包的目的很明显，代码的读者认为闭包在处理`String`值也是安全的，因为这有助于对字符串数组进行排序。

### 单行表达式闭包隐式返回值

单行表达式闭包，可以省略声明中的`return`关键字，隐式地返回其单行表达式的结果，就像上面例子的如下版本：
```swift
reversedNames = names.sorted(by: { s1, s2 in s1 > s2} )
```

在这里，方法`sorted(by:)`的函数类型中的实参类型表明，闭包必须返回`Bool`值。因为闭包体仅包含单行返回`Bool`值的表达式（`s1 > s2`），没有任何歧义，可以省略`return`关键字。

### 简写实参名称

Swift自动为内联闭包提供简写的实参名，可以用诸如`$0`，`$1`，`$2`这样的名称指代闭包的实参名。

如果在闭包表达式中使用这些简写实参名，则可以省略定义中的闭包实参列表，且实参名的数量和类型会从函数类型中推导出来。`in`关键字也可以省略，因为闭包表达式仅由闭包体组成：
```swift
reversedNames = names.sorted(by: { $0 > $1 } )
```

这里，`$0`和`$1`指闭包的第一个和第二个字符串实参。

### 运算符方法

实际上有一种书写上述闭包表达式更短的方法。Swift的字符串类型定义了针对字符串的大于运算符(`>`)的实现，该实现有两个`String`类型形参，并返回`Bool`类型的值。这符合`sorted(by:)`方法需要的方法类型。因此，你可以简单地传入大于运算符，然后Swift会推断你想要用针对字符串的实现：
```swift
reversedNames = names.sorted(by: >)
```
更对关于运算符方法，参见[运算符方法](Advanced_Operators.md#运算符方法)。

## 尾随闭包

如果需要将闭包表达式最为函数的最后一个实参，且闭包表达式很长，那么将闭包写成*尾随闭包*比较有用。即便是函数的实参，尾随闭包写在调用方法的括号后面。当使用尾随闭包语法，不用写实参标签作为函数调用的一部分：
```swift
func someFunctionThatTakesAClosure(closure: () -> Void) {
  // function body goes here
}

// Here's how you call this function without using a trailing closure:

someFunctionThatTakesAClosure(closure: {

})

// Here's how you call this function with a trailing closure instead:

someFunctionThatTakesAClosure() {
  trailing closure's body goes here
}
```

上面的字符串排序闭包的闭包表达式语法片段可以作为尾随闭包写到`sorted(by:)`方法括号外面：
```swift
reversedNames = names.sorted() { $0 > $1 }
```

如果闭包表达式是函数或方法仅有的实参，然后以尾随闭包的方式提供该闭包，当调用函数时，你可以不必在函数或方法名后面写上括号(`()`)：
```swift
reversedNames = names.sorted { $0 > $1 }
```

当闭包长到不可能内联到一行代码中时，尾随闭包就显得尤为重要。举个例子，Swift中`Array`类型有一个`map(_:)`方法，该方法接受一个闭包表达式作为唯一的实参。该闭包为数组中的每一个元素调用一次，然后为元素返回一个映射了的值（可能是另外的类型）。映射的性质和返回值的类型由闭包指定。

把提供的闭包提供给数组每个元素后，`map(_:)`方法返回一个包含所有新映射值的新数组，新数组元素顺序与原数组对应元素顺序相同。

下面是如何用`map(_:)`方法配合尾随闭包，把一个`Int`数组转换为一个`String`数组。数组`[16, 58, 510]`被用来创建新数组`["OneSix", "FiveEight", "FiveOneZero"]`：
```swift
let digitNames = [
    0: "Zero", 1: "One", 2: "Two",   3: "Three", 4: "Four",
    5: "Five", 6: "Six", 7: "Seven", 8: "Eight", 9: "Nine"
]
let numbers = [16, 58, 510]
```

上面的代码创建一个字典，存储整数和对应英文版名称的映射。也定义了一个整数数组，将被转换为字符串。

现在可以通过传入尾随闭包形式的闭包表达式给数组的`map(_:)`方法，从数字数组创建字符串数组：
```swift
let strings = numbers.map { (number) -> String in
    var number = number
    var output = ""
    repeat {
        output = digitNames[number % 10]! + output
        number /= 10
    } while number > 0
    return output
}
// strings is inferred to be of type [String]
// its value is ["OneSix", "FiveEight", "FiveOneZero"]
```

`map(_:)`方法为数组中每个元素调用一次闭包表达式。你无需指定闭包输入形参的类型，`number`，因为其类型可以从被映射数组中的值推导出来。

在这个例子中，变量`number`被闭包中形参`number`初始化，所以在闭包体中可以修改该值。（函数和闭包的形参总是常量。）闭包表达式也指定了一个`String`返回值类型，表明储存在映射输出数组中的类型。

每次被调用，闭包表达式构建一个字符串`output`。它用取余运算符计算`number`的最后一位数字，然后用该数字从字典`digitNames`中查找合适的字符串。该闭包可被用于创建任何大于零数字的字符串呈现。
> 注意：
> 字典`digitNames`下标调用后面有一个感叹号(`!`)，因为字典下标返回一个可选值，表明字如果键不存在符串查找可能会失败。上面的例子中，对于`digitNames`字典，能够保证`number % 10`总是有有效的下标键，所以感叹号被用于强制解包储存在下标返回的可选值中的字符串值。

从字典`digitNames`中获取的字符串被添加到`output`的前面，逆序高效地为数字创建字符串版本。（对于`16`，表达式`number % 10`给出`6`，`58`给出`8`，`510`给出`0`。）

然后`number`变量被除以`10`。因为是整数，数字被向下舍入，所以`16`变成`1`，`58`变成`5`，`510`变成`51`。

该过程会一直重复，直到`number`变成0，此时`output`值被闭包返回，然后被`map(_:)`方法添加到输出数组中。

上例中使用尾随闭包语法，将闭包功能整齐地封装在闭包所支持的函数后面，而不需要将整个闭包包含在`map(_:)`方法的外部括号中。

## 值的捕获

一个闭包可以从其被定义的上下文中捕获常量或变量。即使定义了这些常量和变量的原始代码块不存在了，闭包仍可以在闭包体内访问和修改这些变量和常量的值。

在Swift中，可以捕获值的闭包的最简单形式，是写在其他函数中的嵌套函数。一个嵌套函数可以捕获其外部函数的任何实参，也可以捕获定义在外部函数中的任何常量或变量。

下面是一个例子，函数`makeIncrementer`包含一个嵌套函数`incrementer`。嵌套函数`incrementer()`从上下文捕获了两个值，`runningTotal`和`amount`。捕获这些值之后，`incrementer`被`makeIncrementer`当作闭包返回，每次调用都会使`runningTotal`增加。
```swift
func makeIncrementer(forIncrement amount: Int) -> () -> Int {
  var runningTotal = 0
  func incrementer() -> Int {
    runningTotal += amount
    return runningTotal
  }
  return incrementer
}
```

`makeIncrementer`的返回类型是`() -> Int`。这意味着它返回一个函数，而不是一个简单的值。返回的函数没有形参，每次调用该函数会返回一个`Int`值。要学习一个函数如何返回另一个函数，参见[函数类型作为返回类型](Functions.md#函数类型作为返回类型)。

函数`makeIncrementer(forIncrement:)`定义了一个整数变量`runningTotal`，存放将被返回的增量的当前总和。这个变量被初始化为`0`。

`makeIncrementer(forIncrement:)`只有一个`Int`类型形参，实参标签是`forIncrement`，形参名是`amount`。实参值被传入该形参，指明每次被返回的函数被调用时`runningTotal`需要增加多少。`makeIncrementer`函数中定义了一个嵌套函数`incrementer`，嵌套函数实际执行增加。嵌套函数只是把`amount`加给`runningTotal`，然后返回结果。

单独考虑时，嵌套函数`incrementer()`可能并不常见：
```swift
func incrementer() -> Int {
  runningTotal += amount
  return runningTotal
}
```

`incrementer()`函数没有任何形参，但是却在函数体内引用了`runningTotal`和`amount`。通过从周围函数捕获`runningTotal`和`amount`的引用来达到此目的，并在自己的函数体内使用。捕获引用确保了当`makeIncrementer`执行到尾部，`runningTotal`和`amount`不会消失，也确保了`runningTotal`在下一次`incrementer`函数被调用时可用。

> 注意：
> 作为优化，如果值没有被闭包修改或在闭包被创建时值没有被改变，Swift可能用储存值的备份来代替捕获。
> Swift还处理在处理不需要的变量时涉及到的所有内存管理。

下面是`makeIncrementer`应用的例子：
```swift
let incrementByTen = makeIncrementer(forIncrement: 10)
```

这个例子设置一个常量`incrementByTen`给一个增量函数，该函数每次被调用时，会向`runningTotal`变量加`10`。下面多次调用该函数：
```swift
incrementByTen()
// returns a value of 10
incrementByTen()
// returns a value of 20
incrementByTen()
// returns a value of 30
```

如果创建第二个增量器，它会有一个自身的新的，单独的`runningTotal`的储存引用：
```swift
let incrementBySeven = makeIncrementer(forIncrement: 7)
incrementBySeven()
// returns a value of 7
```

再一次调用最初的增量器(`incrementByTen`)，让其`runningTotal`变量增长，不会影响到被`incrementBySeven`捕获的变量：
```swift
incrementByTen()
// returns a value of 40
```

> 注意：
> 如果把闭包赋值给类的一个属性，闭包会通过引用该实例或其成员变量来捕获该实例，这是你在闭包和实例间创建了一个强循环引用。Swift用*捕获列表*打破这个强循环引用。更多信息，参见[闭包的强循环引用](Automatic_Reference_Counting.md#闭包的强循环引用)。

## 闭包是引用类型

在上面的例子中，`incrementBySeven`和`incrementByTen`是常量，但是这些常量引用的闭包仍然能增加它们所捕获的变量`runningTotal`的值。这是因为函数和闭包是引用类型。

无论何时，当你给变量或常量赋值一个函数或一个闭包，你实际上将常量或变量设置成函数或闭包的*引用*。上例中，`incrementByTen`指向的闭包是常量，而不是闭包本身的内容是常量。

这意味着，如果你把闭包赋值给两个不同的常量或变量，则这两个常量或变量都引用同一个闭包。
```swift
let alsoIncrementByTen = incrementByTen
alsoIncrementByTen()
// returns a value of 50

incrementByTen()
// returns a value of 60
```

上面的例子表明，调用`alsoIncrementByTen`与调用`incrementByTen`相同。因为两者都指向同一个闭包，它们都增加并返回了相同的`runningTotal`。

## 逃逸闭包

如果一个闭包被当作实参传入函数，但在函数返回之后才被调用，我们就说该闭包逃逸。当声明一个接受闭包当作参数的函数时，在参数类型前面写上`@escaping`表明允许该闭包逃逸。

闭包可以逃逸的一种方式是，将闭包存放在定义在函数外面的变量中。举个例子，许多启动异步操作的函数接受一个闭包实参作为操作完成后的处理器。函数启动操作后就返回，但闭包并没有被调用，直到异步操作完成——闭包需要逃逸，延后调用。例如：
```swift
var completionHandlers: [() -> Void] = []
func someFunctionWithEscapingClosure(completionHandler: @escaping () -> Void) {
  completionHandlers.append(completionHandler)
}
```

`someFunctionWithEscapingClosure(_:)`函数接受一个闭包作为实参，然后将其添加到一个定义在函数外面的数组里。如果不将参数标记为`@escaping`，将发生编译时错误。

讲一个闭包标记为`@escaping`意味着你必须在闭包中显式引用`self`。例如，下面的代码中，传入到`someFunctionWithEscapingClosure(_:)`的是一个逃逸闭包，意味着需要显式引用`self`。相反，传入到`someFunctionWithNonescapingClosure(_:)`的是非逃逸闭包，意味着其可以隐式地引用`self`。
```swift
func someFunctionWithNonescapingClosure(closure: () -> Void) {
  closure()
}

class SomeClass {
  var x = 10
  func doSomething() {
    someFunctionWithEscapingClosure { self.x = 100 }
    someFunctionWithNonescapingClosure { x = 200 }
  }
}

let instance = SomeClass()
instance.doSomething()
print(instance.x)
// Prints "200"

completionHandlers.first?()
print(instance.x)
// Prints "100"
```

## 自动闭包

自定闭包是为了包裹一个被当作形参传入给函数的表达式而自动创建的闭包。它不接受任何参数，当被调用时，会返回被包裹的表达式的值。这种语法糖，通过编写简单的表达式而不是显式的闭包，使你可以省略包围函数参数的括号。

调用接受自动闭包的函数很常见，但是实现该类型函数并不常见。例如，`assert(condition:message:file:line:)`函数为`condition`和`message`函数接受一个自动闭包；`condition`参数只在调试构建时才参与计算，`message`参数只在当`condition`是`false`时才参与计算。

自动闭包能让你延迟计算，因为只有你调用闭包的时候，代码才执行。延迟计算对于有边际效应或费计算力的代码很有用，因为代码被计算时，你能控制它。下面的代码展示了闭包如何延迟计算的：
```swift
var customersInLine = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
print(customersInLine.count)
// Prints "5"

let customerProvider = { customersInLine.remove(at: 0) }
print(customersInLine.count)
// Prints "5"

print("Now serving \(customerProvider())!")
// Prints "Now serving Chris!"
print(customersInLine.count)
// Prints "4"
```

即便数组`customersInLine`第一个元素被闭包内的代码移除，闭包被实际调用之前，数组元素并没有被移除。如果闭包从不被调用，则闭包内的表达式从不被计算，意味着数组的元素从不会被移除。注意`customerProvider`的类型不是`String`而是`() -> String`——一个无参数返回字符串的函数。

当把闭包当作函数传给函数时，你会得到相同的延迟计算行为：
```swift
// customersInLine is ["Alex", "Ewa", "Barry", "Daniella"]
func serve(customer customerProvider: () -> String) {
  print("Now serving \(customerProvider())!")
}
serve(customer: { customersInLine.remove(at: 0) } )
// Prints "Now serving Alex!"
```

上面的`serve(customer:)`函数接受一个返回顾客名的显式闭包。下面版本的`serve(customer:)`执行相同的操作，但，作为接受显式闭包的替代，它通过标记参数类型属性为`@autoclosure`从而接受一个自动闭包。现在你可以像它接受一个`String`参数一样调用该函数。参数自动变换成一个闭包，因为`customerProvider`参数类型被标记为`@autoclosure`属性。
```swift
// customersInLine is ["Ewa", "Barry", "Daniella"]
func serve(customer customerProvider: @autoclosure () -> String) {
  print("Now serving \(customerProvider())!")
}
serve(customer: customersInLine.remove(at: 0))
// Prints "Now serving Ewa!"
```
> 注意：
过量使用自动闭包会使你的代码变得难懂。上下文和函数名应该明确计算被推迟。

如果你想一个自动闭包能够逃逸，使用`@autoclosure`和`@escaping`属性。`@escaping`属性在上面[逃逸闭包](#逃逸闭包)有描述。
```swift
// customersInLine is ["Barry", "Daniella"]
var customerProviders: [() -> String] = []
func collectCustomerProviders(_ customerProvider: @autoclosure @escaping () -> String) {
    customerProviders.append(customerProvider)
}
collectCustomerProviders(customersInLine.remove(at: 0))
collectCustomerProviders(customersInLine.remove(at: 0))

print("Collected \(customerProviders.count) closures.")
// Prints "Collected 2 closures."
for customerProvider in customerProviders {
    print("Now serving \(customerProvider())!")
}
// Prints "Now serving Barry!"
// Prints "Now serving Daniella!"
```

在上面的代码中，不是直接调用被传入作为`customerProvider`参数的闭包，而是`collectCustomerProviders(_:)`函数将闭包添加到`customerProviders`数组中。数组在函数块之外被声明，意味着数组中的闭包可以在函数返回之后被执行。结果，`customerProvider`参数的值必须被允许逃逸函数块。

[< 函数](Functions.md) || [枚举 >](Enumeration.md)
