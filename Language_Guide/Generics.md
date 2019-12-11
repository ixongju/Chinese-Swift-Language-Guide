# 泛型

*泛型代码* 使你能够根据定义的要求，编写能够使用任何类型的易扩展，可复用的函数和类型。你可以编写无重复的代码，并且用清晰，抽象地方式表达其意图。

泛型是Swift中最强大的功能之一，Swift标准库中的许多代码是用泛型构建的。事实上，在整个*语言指南*中一直在使用泛型，即便你没有意识到。举个例子，Swift的`Array`和`Dictionary`都是泛型集合。你可以创建存放`Int`值的`Array`，或触发`String`值的`Array`，或干脆存放任何在Swift可以创建的任何类型的值。类似地，你可以创建一个存放任何指定类型值的字典，至于该类型是什么类型，无任何限制。

## 泛型解决的问题

下面是一个标准，非泛型函数`swapTwoInts(_:_:)`，该还是交换两个`Int`的值：
```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

这个函数使用了输入输出参数交换`a`和`b`的值，如[输入输出参数](Functions.md#输入输出参数)中所述。

`swapTwoInts(_:_:)`函数交换`b`的原始值给`a`，`a`的原始值给`b`。可以调用这个函数交换两个`Int`变量的值：
```swift
var someInt = 3
var anotherInt = 107
swapTwoInts(&someInt, &anotherInt)
print("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
// Prints "someInt is now 107, and anotherInt is now 3"
```

`swapTwoInts(_:_:)`函数很有用，但是它只能用`Int`值。如果你想交换两个`String`类型的值，或`Double`类型的值，就需要编写更多函数，例如`swappTwoStrings(_:_:)`和`swappTwoDoubles(_:_:)`，如下所示：
```swift
func swapTwoStrings(_ a: inout String, _ b: inout String) {
    let temporaryA = a
    a = b
    b = temporaryA
}

func swapTwoDoubles(_ a: inout Double, _ b: inout Double) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

也许你已经注意到了，函数`swapTwoInts(_:_:)`，`swapTwoStrings(_:_:)`和`swapTwoDoubles(_:_:)`的函数体相同。唯一的不同是它们接受的值的类型（`Int`，`String`和`Double`）。

编写一个能交换任何类型的两个值的函数，更有用也更灵活。泛型能让你编写这样的函数。（这些函数的泛型版本在下面有定义。）

> 注意：
> 在这三个函数中，`a`和`b`的类型必须相同。如果`a`和`b`类型不同，就不可能交换它们的值。Swift是类型安全的语言，不允许`String`类型的变量和一个`Double`类型的变量互相交换值。尝试这么做会导致编译错误。

## 泛型函数

*泛型函数* 可以使用任何类型。下面是上面函数`swapTwoInts(_:_:)`的泛型版本`swapTwoValues(_:_:)`：
```swift
func swapTwoValues<T>(_ a: inout T, _ b: inout T) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```
函数`swapTwoValues(_:_:)`的函数体与`swapTwoInts(_:_:)`的函数体相同。但是，第一行不同。下面是第一行的对比：
```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int)
func swapTwoValues<T>(_ a: inout T, _ b: inout T)
```

该函数的泛型版本用了一个*占位*类型名(在这里，是`T`)替换实际的类型名(如`Int`，`String`或`Double`)。占位符类型名没有具体表明`T`必须是什么，但是表明了`a`和`b`必须属于相同类型`T`，不管`T`代表什么类型。`T`的实际类型在函数`swapTwoValues(_:_:)`每次被调用时决定。

泛型函数与非泛型函数的其他不同在于，泛型函数名(`swapTwoValues(_:_:)`)后面有包含在尖括号中的占位类型名(`<T>`)。尖括号告诉Swift`T`是函数`swapTwoValues(_:_:)`定义中的一个占位类型名。因为`T`是一个占位符，Swift不会去查找一个名为`T`的类型。

现在只要传入任意类型的两个值，并且这两个值属于同一类型，就可以用调用`swapTwoInts(_:_:)`的方法调用`swapTwoValues(_:_:)`函数。每次调用`swapTwoValues(_:_:)`，`T`的类型就会从传入给函数的值的类型被推导出来。

下面的两个例子中，`T`分别被推导为`Int`和`String`：
```swift
var someInt = 3
var anotherInt = 107
swapTwoValues(&someInt, &anotherInt)
// someInt is now 107, and anotherInt is now 3

var someString = "hello"
var anotherString = "world"
swapTwoValues(&someString, &anotherString)
// someString is now "world", and anotherString is now "hello"
```

> 注意：
> 上面定义的`swapTwoValues(_:_:)`函数，是受到函数`swap`的启发，该函数是Swift标准库的一部分，在你的app中自动可用。如果你需要用到`swapTwoValues(_:_:)`函数，请使用Swift现有的`swap(_:_:)`函数，而不是提供自己的实现。

## 类型参数

上面`swapTwoValues(_:_:)`的例子中，占位类型`T`是*类型参数*的例子。类型参数指定并命名一个占位类型，直接写在函数名后，用尖括号括起来（如`<T>`）。

一旦指定了类型参数，就可以用它类定义函数参数的类型（例如，函数`swapTwoValues(_:_:)`中的参数`a`和`b`），或作为函数的返回类型，或作为函数体中的类型说明。每种情况，当函数被调用时，类型参数会被一个实际类型替换。（上面`swapTwoValues(_:_:)`的例子中，第一次调用函数，`T`被替换为`Int`，第二次调用时，别替换为`String`。）

通过在尖括号中写上多个用逗号隔开的类型参数名，可以提供多个类型参数。

## 命名类型参数

在大多数情况下，类型参数有描述性的名称，如`Dictionary<Key, Value>`中的`Key`和`Value`和`Array<Element>`中的`Element`，这些告诉读者类型参数与泛型类型或函数之间的关系。但是，当没有有意义的关系时，传统上使用单个字母，如`T`，`U`和`V`，如上面函数`swapTwoValues(_:_:)`中的`T`。

> 注意：
> 请用驼峰命名法命名类型参数（如`T`和`MyTypeParameter`）表明它们是类型占位符，而不是值。

## 泛型类型

除了泛型函数，Swift允许你定义自己的*泛型类型*。它们是可以用与`Array`和`Dictionary`类似的方法，使用任何类型的自定义类，结构体和枚举。

本节想你展示如何编写一个泛型集合类型`Stack`(堆)。一个堆是值的有序列表，与数组相似，但是与Swift中的`Array`相比有更严格的操作。一个数组允许在数组的任何位置插入或移除元素。但是，堆只允许从集合的尾部插入新元素（被称为押入一个值到堆中。*push*）。类似地，堆只允许从尾部移除一个元素（被称为从堆中弹出一个值。*pop*）。

> 注意：
> 堆的概念被`UINavigationController`类用与模拟视图管理器的导航结构。调用`UINavigationController`类的`pushViewController(_:animated:)`方法想导航堆中添加（或押入）一个视图管理器，调用其`popViewControllerAnimated(_:)`方法从导航堆中移除（或弹出）一个视图管理器。当你需要严格的“后进，先出”操作来管理集合时，堆是一个有用的集合模型。

下面的图表展示了堆的押入和弹出行为：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/stackPushPop_2x.png" alt="泛型类型" width="500"/>
</p>

1. 当前堆中有三个值
2. 第四个值被押入堆的顶部
3. 现在堆有四个值，最近押入的值在顶部
4. 顶部的项被弹出
5. 弹出一个值之后，堆再一次有三个值

下面是如何编写非泛型的堆，这个情况是`Int`值的堆：
```swift
struct IntStack {
    var items = [Int]()
    mutating func push(_ item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
}
```

结构体用一个`Array`是属性`items`存放堆中的值。`Stack`提供两个方法`push`和`pop`，用于向堆中押入值和从堆中弹出值。这些方法被标记为`mutating`，因为它们需要改变结构体的数组`items`。

但是，上面的`IntStack`只能用`Int`值。定义一个泛型`Stack`类比较有用，泛型`Stack`可以管理任何类型的堆。

下面是相同代码的泛型版：
```swift
struct Stack<Element> {
    var items = [Element]()
    mutating func push(_ item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
}
```

注意泛型版的`Stack`与非泛型版的基本相同，但是使用类型参数`Element`替换了实际的类型`Int`。这个类型参数写在了结构体名后面的尖括号内(`<Element>`)。

`Element`定义了稍后被提供的类型的占位名。在结构体定义中的任何地方，未来的类型可以被推导为`Element`。这种情况下，`Element`在三个地方被用作占位符：
* 用于创建属性`items`，初始化为`Element`类型值的空数组
* 用于指定`push(_:)`方法有一个参数`item`，该参数类型必须是`Element`
* 用于指定被`pop()`方法返回的值其类型是`Element`

因为是泛型类型，`Stack`可以使用Swift中任何有效类型，与`Array`和`Dictionary`的方式类似。

通过把要存放的类型写在尖括号内来创建新`Stack`实例。例如，要创建一个字符串堆，写作`Stack<String>()`：
```swift
var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tres")
stackOfStrings.push("cuatro")
// the stack now contains 4 strings
```

下面是押入了四个值之后的`stackOfStrings`：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/stackPushedFourStrings_2x.png" alt="泛型类型" width="500"/>
</p>

从堆中弹出一个值会移除并返回顶部的值`cuatro`：
```swift
let fromTheTop = stackOfStrings.pop()
// fromTheTop is equal to "cuatro", and the stack now contains 3 strings
```

下面是弹出顶部值后的堆：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/stackPoppedOneString_2x.png" alt="泛型类型" width="500"/>
</p>

## 扩展一个泛型类型

当你扩展一个泛型类型时，不需要为扩展定义提供类型参数列表。相反，在扩展体中，可以使用原始类型定义中的类型参数列表，原始类型参数名被用于推导原始定义中的类型参数。

下面的例子扩展了泛型`Stack`，添加了一个只读计算属性`topItem`，该属性返回堆顶部的项而不会将其弹出：
```swift
extension Stack {
    var topItem: Element? {
        return items.isEmpty ? nil : items[items.count - 1]
    }
}
```

`topItem`属性返回一个`Element`类型的可选值。如果堆是空的，则`topItem`返回`nil`；如果堆非空，`topItem`返回`items`数组的最后一个项。

注意这个扩展并没有定义类型参数列表。相反，在扩展中用`Stack`现有的类型参数名`Element`，来表示计算属性`topItem`的可选类型。

现在`topItem`属性可用在`Stack`任何实例上，以访问和请求顶部的项，而不移除它：
```swift
if let topItem = stackOfStrings.topItem {
    print("The top item on the stack is \(topItem).")
}
// Prints "The top item on the stack is tres."
```

泛型类型的扩展也可以包含为了获得扩展的功能扩展类型的实例必须满足的条件，如[用Where子句扩展泛型](#用Where子句扩展泛型)中所述。

## 类型约束

函数`swapTwoValues(_:_:)`和类型`Stack`可以使用任何类型。但是，有时候给泛型函数和泛型类型可用的类型增加某种*类型约束*比较有用。类型约束指明，类型参数必须继承自指定类或必须实现特定协议或协议组合。

举个例子，Swift中的`Dictionary`类型给字典键的类型添加了限制。如[字典](Collection_Types.md#字典)中所述，字典键的类型必须可哈希(*hashable*)。也就是，它必须提供一个让自身具有独特变现性的方法。`Dictionary`需要键可哈希，所以它能检查对于特定的键是否已经包含对应的值。如果没有这个要求，`Dictionary`将无法确定是否要为特定的键插入或移除一个值，也不能为给定的键找到字典中现有的值。

这个要求被`Dictionary`键类型的类型约束执行，该类型约束指定键类型必须实现了`Hashable`协议，`Hashable`协议是Swift标准库中的特殊协议。默认地，所有的Swift基本类型（如`String`，`Int`，`Double`和`Bool`）都可哈希。

你可以在创建自定义泛型时定义自己的类型约束，这些约束提供了泛型编程的大部分能力。诸如`Hashable`这样的抽象概念，根据概念特征而不是具体类型来体现类型。

### 类型约束语法

通过在类型参数名后面写上单个类或协议约束来编写类型约束，(类型参数名与类或协议约束)用冒号隔开，作为类型参数列表的一部分。泛型函数类型约束的基本语法如下所示（泛型类型的语法相同）：
```swift
func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {
    // function body goes here
}
```

上面的函数有两个类型参数。第一个类型参数`T`，有一个要求`T`是`SomeClass`的子类的约束。第二个类型参数`U`，有一个要求`U`实现`SomeProtocol`协议的约束。

### 类型约束实践

下面是一个非泛型函数`findIndex(ofString:in:)`，该函数接受一个需要查找的`String`值，和一个在其中寻找该`String`值的`String`数组。`findIndex(ofString:in:)`函数返回一个可选`Int`值，该可选`Int`值是数组中第一个匹配的字符串的索引，或`nil`，如果没有找到该字符串：
```swift
func findIndex(ofString valueToFind: String, in array: [String]) -> Int? {
    for (index, value) in array.enumerated() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}
```

可以用函数`findIndex(ofString:in:)`在字符串数组中查找某个字符串：
```swift
let strings = ["cat", "dog", "llama", "parakeet", "terrapin"]
if let foundIndex = findIndex(ofString: "llama", in: strings) {
    print("The index of llama is \(foundIndex)")
}
// Prints "The index of llama is 2"
```

然而，查找一个值在数组中索引的原理并不仅仅对字符串有用。可以通过用`T`类型的值替换出现的字符串，把这个函数改写为泛型函数。

下面是`findIndex(ofString:in:)`的一个可能的泛型版本`findIndex(of:in:)`。注意函数的返回类型仍然是`Int?`，因为函数返回一个可选索引数，而不是数组中可选类型值。再注意，这个函数不会编译，原因在例子后面给出：
```swift
func findIndex<T>(of valueToFind: T, in array: [T]) -> Int? {
    for (index, value) in array.enumerated() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}
```

函数并不会如上面所写那样编译。问题在于相等检查`if value == valueToFind`。并不是Swift中的每个类型都能用等于操作符(`==`)进行比较。例如，如果你为复杂的数据结构创建自定义类或结构体，则对于该类或结构体，Swift并不能猜出“相等”的意义。因此，不可能保证代码对于所有可能类型`T`都正常工作，当你尝试编译代码的时候，会报相应的错误。

但是，别慌！Swift标准库定义了一个协议`Equatable`，它要求任何遵守`Equatable`协议的类型实现等于操作符(`==`)和不等操作符(`!=`)，用以比较该类型的两个值。Swift所有的标准类型自动支持`Equatable`协议。

所有实现了`Equatable`的类型可以安全地使用`findIndex(of:in:)`函数，因为它确保支持等于操作符。为了表明这个事实，当定义函数是，向类型参数定义中添加`Equatable`类型约束：
```swift
func findIndex<T: Equatable>(of valueToFind: T, in array: [T]) -> Int? {
    for (index, value) in array.enumerated() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}
```

`findIndex(of:in:)`唯一的类型参数写作`T: Equatable`，意思是“任何遵守`Equatable`协议的类型`T`”。

函数`findIndex(of:in:)`现在能编译成功，并可被用于所有的`Equatable`类型，例如`Double`或`String`：
```swift
let doubleIndex = findIndex(of: 9.3, in: [3.14159, 0.1, 0.25])
// doubleIndex is an optional Int witn no value, because 9.3 isn't in the array
let stringIndex = findIndex(of: "Andrea", in: ["Mike", "Malcolm", "Andrea"])
// stringIndex is an optional Int containing a value of 2
```

## 关联类型

定义协议时，有时候声明一个或多个关联类型作为定义的一部分比较有用。*关联类型* 为协议中用到的类型提供一个占位名称。知道协议被实现，才会指定关联类型的实际类型。关联类型用`associatedtype`关键字指定。

### 关联类型实践

下面是一个协议`Container`，该协议声明了一个关联类型`Item`：
```swift
protocol Container {
    associatedtype Item
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}
```

`Container`协议定义了任何容易必须提供的三个必备能力：
* 必须可以用`append(_:)`方法为容器添加新项
* 必须可以通过返回`Int`值的`count`属性访问容器中项的数量
* 必须可以通过接受`Int`索引的下标获取容器中的每个项

协议没有指明项如何被储存在容器中，或允许什么类型。协议只指定为了成为一个`Container`，类型必须提供的三个功能。只要满足了这三个要求，遵守了协议的类型可以提供额外的功能。

任何遵守`Container`协议的类型都必须能指定其储存的值的类型。特别地，必须确保正确类型的项才能被添加到容器中，并且必须明确下标返回的项的类型。

为了定义这些要求，`Container`协议需要一个方式来推导容器储存元素的类型，同时不需知道特定容器的具体类型。`Container`协议需要指定任何传入到`append(_:)`方法的值必须有与容器元素类型相同的类型，且容器下标返回的值的类型与容器元素的类型相同。

为了达到这个目的，`Container`协议声明了一个关联类型`Item`，写作`associatedtype Item`。协议没有定义`Item`具体是什么–––该信息留给实现协议的类型来提供。尽管如此，`Item`别名提供了一种方法来引用`Container`中项的类型，并定义了一个与`append(_:)`方法和下标一起使用的类型，以确保执行任何`Container`所预期的行为。

下面是上面章节[泛型类型](#泛型类型)中`IntStack`的非泛型版本，遵守了`Container`协议：
```swifts
struct IntStack: Container {
    // original IntStack implementation
    var items = [Int]()
    mutating func push(_ item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
    // conformance to the Container protocol
    typealias Item = Int
    mutating func append(_ item: Int) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Int {
        return items[i]
    }
}
```

`IntStack`类型实现了`Container`协议的三个要求，在各种情况下，包装`IntStack`类型现有的功能去满足这些要求。

此外，`IntStack`指定，对于`Container`的这个实现，对应的`Item`类型用`Int`。定义`typealias Item = Int`将`Container`协议的实现中的抽象类型`Item`变成具体类型`Int`。

得益于Swift的类型推导，实际上你不需要在`IntStack`定义中声明`Item`的具体类型`Int`。因为`IntStack`遵守了`Container`协议的所有要求，Swift可以通过查看`append(_:)`方法的参数和下标的返回类型来推导出合适的`Item`。确实，如果删除上面代码中的`typealias Item = Int`，也能正常工作，因为需要用什么类型已经很清晰了。

你也可以让泛型类型`Stack`实现`Container`协议：
```swift
struct Stack<Element>: Container {
    // original Stack<Element> implementation
    var items = [Element]()
    mutating func push(_ item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
    // conformance to the Container protocol
   mutating func append(_ item: Element) {
       self.push(item)
   }
   var count: Int {
       return items.count
   }
   subscript(i: Int) -> Element {
       return items[i]
   }
}
```

这次，类型参数`Element`被用于`append(_:)`方法的`item`参数类型和下标的返回类型。因此Swift可以推导出`Element`是适用于这个特定容器的`Item`的类型。

### 扩展现有类型以指定关联类型

可以扩展一个现有类型以添加协议一致性，如[用扩展添加协议一致性](Protocols.md#用扩展添加协议一致性)中所述。这包括有关联类型的协议。

Swift的`Array`类型已经提供了一个`append(_:)`方法，一个`count`属性，和一个用`Int`索引获取元素的下标。这三个能力匹配的`Container`协议的要求。这意味着，你可以通过声明`Array`遵守`Container`协议来扩展`Array`实现`Container`协议。用空扩展来实现这个，如[用扩展声明协议采用](Protocols.md#用扩展声明协议采用)中所述：
```swift
extension Array: Container {}
```

`Array`现有的`append(_:)`方法和下标使Swift推导出适用于`Item`的类型，就如上面的泛型类型`Stack`一样。定义扩展之后，你就可以把任何`Array`当作`Container`使用。

### 为关联类型添加约束

给关联类型添加类型约束以要求实现类型满足这些约束。举个例子，下面的代码定义了一个要求容器中的项可以相等的`Container`版本。
```swift
protocol Container {
    associatedtype Item: Equatable
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}
```

为了符合这个版本的协议，容器的`Item`类型必须符合`Equatable`协议。

### 在协议的关联类型约束中使用协议

一个协议可以作为自身要求的一部分出现。举个例子，下面的协议重新定义了`Container`协议，追加了`suffix(_:)`方法要求。`suffix(_:)`方法从容器末尾返回给定数量的元素，并将它们储存在`Suffix`类型的实例中。
```swift
protocol SuffixableContainer: Container {
    associatedtype Suffix: SuffixableContainer where Suffix.Item == Item
    func suffix(_ size: Int) -> Suffix
}
```

在这个协议中，`Suffix`是一个关联类型，如上面例子中`Container`中的`Item`类型。`Suffix`有两个约束：它必须遵循`SuffixableContainer`协议（当前定义的协议），其`Item`类型必须与容器的`Item`类型相同。`Item`的约束是泛型`where`子句，在[泛型Where子句的关联类型](#泛型Where子句的关联类型)中有述。

下面是上面[泛型类型](#泛型类型)中`Stack`类型的一个扩展，该扩展添加`SuffixableContainer`协议的一致性：
```swift
extension Stack: SuffixableContainer {
    func suffix(_ size: Int) -> Stack {
        var result = Stack()
        for index in (count-size)..<count {
            result.append(self[index])
        }
        return result
    }
    // Inferred that Suffix is Stack.
}
var stackOfInts = Stack<Int>()
stackOfInts.append(10)
stackOfInts.append(20)
stackOfInts.append(30)
let suffix = stackOfInts.suffix(2)
// suffix contains 20 and 30
```

在上面的例子中，`Stack`的关联类型`Suffix`也是`Stack`，所以`Stack`的后缀操作返回另一个`Stack`。另外，一个实现了`SuffixableContainer`协议的类型有一个与自身不同的`Suffix`类型———意味着后缀操作返回不同类型。举个例子，下面的扩展为非泛型`IntStack`类型添加`SuffixableContainer`一致性，用`Stack<Int>`作为其后缀类型，而不是`IntStack`：
```swift
extension IntStack: SuffixableContainer {
    func suffix(_ size: Int) -> Stack<Int> {
        var result =  Stack<Int>()
        for index in (count-size)..<count {
            result.append(self[index])
        }
        return result
    }
    // Inferred that Suffix is Stack<Int>
}
```

## 泛型Where子句

类型约束，如[类型约束](#类型约束)中所述，允许你在与函数、下标或类型关联的类型参数上定义要求。

为关联类型定义要求也很有用。通过定义*泛型where子句*来实现这个。泛型where子句允许你要求关联类型必须遵守某一个协议，或某个类型参数和关联类型必须相同。泛型where子句以`where`关键字开始，后面接关联类型约束或类型与关联类型的相等关系。在类型或函数体的开始大括号之前编写泛型where子句。

下面的例子定义了泛型函数`allItemsMatch`，它检查两个`Container`实例是否以相同的顺序包含相同的项。如果项匹配，则函数返回布尔值`true`，如果不匹配，则返回`false`。

被检查的两个容易不一定要是相同类型的容器（即使它们可以是相同的容器），但是它们不需要持有相同类型的项。这个要求通过联合类型约束与一个泛型`where`子句表达：
```swift
func allItemsMatch<C1: Container, C2: Container>
    (_ someContainer: C1, _ anotherContainer: C2) -> Bool
    where C1.Item == C2.Item, C1.Item: Equatable {

    // Check that both containers contain the same number of items.
    if someContainer.count != anotherContainer.count {
        return false
    }

    // Check each pair of items to see if they're equivalent.
    for i in 0..<someContainer.count {
        if someContainer[i] != anotherContainer[i] {
            return false
        }
    }

    // All items match, so return true
    return true
}
```

这个函数接受两个参数`someContainer`和`anotherContainer`。参数`someContainer`的类型是`C1`，另一个参数`anotherContainer`的类型是`C2`。`C1`和`C2`都是调用函数时确定的两个容易类型的类型参数。

给函数的两个类型参数添加了以下要求：
* `C1`必须遵守`Container`协议（写作：`C1: Container`）
* `C2`也必须遵守`Container`协议（写作：`C2: Container`）
* `C1`的`Item`必须与`C2`的`Item`相同（写作：`C1.Item == C2.Item`）
* `C1`的`Item`必须遵守`Equatable`协议（写作：`C1.Item: Equatable`）

第一个和第二个要求定义在函数类型参数列表中，第三个和第四个要求定义在函数的泛型`where`子句中。

这些要求的意思是：
* `someContainer`是类型为`C1`的容器。
* `anotherContainer`是类型为`C2`的容器。
* `someContainer`和`anotherContainer`包含相同类型的项。
* `someContainer`中的项可以用不等操作符(`!=`)检查其是否跟另一个不同。

第三个和第四个要求结合起来意思是，`anotherContainer`中的项也能用不等操作符(`!=`)检查，因为它们与`someContainer`中项的类型相同。

这些要求允许函数`allItemsMatch(_:_:)`比较两个容器，即便这两个容器类型不同。

函数`allItemsMatch(_:_:)`的开始会检查两个容器是否含有相同数量的项。如果项的数量不同，则肯定不匹配，函数返回`false`。

做完这个检查之后，函数用`for-in`循环和半开范围操作符(`..<`)遍历`someContainer`中的所有项。对于每一个项，函数检查`someContainer`中的项是否等于`anotherContainer`中对应的项。如果两者不等，则两个容易不匹配，函数返回`false`。

如果循环结束时没有找到不匹配的，则两个容器匹配，函数返回`true`。

下面是函数`allItemsMatch(_:_:)`的运作方式：
```swift
var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tres")

var arrayOfStrings = ["uno", "dos", "tres"]

if allItemsMatch(stackOfStrings, arrayOfStrings) {
    print("All items match.")
} else {
    print("Not all items match.")
}
// Prints "All items match."
```

上面的例子创建一个`Stack`实例存放`String`值，然后押入三个字符串。例子中也创建了一个数组实例，该实例被一个数组字面量初始化，数组字面量中包含的字符串与`Stack`实例中的相同。即使堆和数组属于不同的类型，他们都遵循了`Container`协议，且都包含相同类型的值。因此可以把这两个容器用作调用`allItemsMatch(_:_:)`函数的参数。在上面的例子中，`allItemsMatch(_:_:)`函数正确地报告出这两个容器中的项都匹配。

## 有泛型where子句的扩展

你也可以在扩展中使用泛型`where`子句。下面的例子扩展了前例中的泛型`Stack`结构体，以向其中添加一个`isTop(_:)`方法。
```swift
extension Stack where Element: Equatable {
    func isTop(_ item: Element) -> Bool {
        guard let topItem = items.last else {
            return false
        }
        return topItem == item
    }
}
```
这个新的`isTop(_:)`方法首先检查`Stack`是否为空，然后把给定的项与`Stack`的顶部项进行比较。如果你不用泛型`where`子句这么做，你将会面临一个问题：`isTop(_:)`方法的实现中用到了相等操作符`==`，但是，`Stack`的定义并没有要求其项必须可相等，所以使用相等操作符`==`会引发编译时错误。用泛型`where`子句可以让你为扩展添加新新要求，使扩展在只有`Stack`中的项可进行相等比较时，才添加`isTop(_:)`方法。

下面是`isTop(_:)`方法的应用：
```swift
if stackOfStrings.isTop("tres") {
    print("Top element is tres.")
} else {
    print("Top element is something else.")
}
// Prints "Top element is tres."
```

如果你尝试在项不能进行相等比较的`Stack`上调用`isTop(_:)`方法，将会得到编译时错误。

```swift
struct NotEquatable {}
var NotEquatableStack = Stack<NotEquatable>()
let NotEquatableValue = NotEquatable()
notEquatableStack.push(notEquatableValue)
notEquatableStack.isTop(notEquatableValue) // Error
```

可以将泛型`where`子句应用于协议的扩展。下面的例子扩展前例中的`Container`协议，以向其中添加`startsWith(_:)`方法。
```swift
extension Container where Item: Equatable {
    func startsWith(_ item: Item) -> Bool {
        return count >= 1 && self[0] == item
    }
}
```

`startsWith(_:)`方法首先确保容器有至少一个项，让后检查容器的第一个项是否匹配给定的项。这个新`startsWith(_:)`方法可被用于实现了`Container`协议的任何类型，包括上面用到的堆和数组，因为这里容器的项可进行相等比较。
```swift
if [9, 9, 9].startsWith(42) {
    print("Starts with 42.")
} else {
    print("Starts with something else.")
}
// Prints "Starts with something else."
```

上面例子中的泛型`where`子句要求项实现一个协议，你可以编写要求项是特定类型的泛型`where`子句。例如：
```swift
extension Container where Item == Double {
    func average() -> Double {
        var sum = 0.0
        for index in 0..<count {
            sum += self[index]
        }
        return sum / Double(count)
    }
}
print([1260.0, 1200.0, 98.6, 37.0].average())
// Prints "648.9"
```

这个例子为`Item`类型为`Double`的容器添加了一个`average()`方法。它遍历容器中的项后相加，然后被容器的长度相除以计算平均值。为了能计算浮点出发，它显式地将长度从`Int`类型转换为`Double`类型。

可以在扩展中的泛型`where`子句中包含多个要求，就像你能在任何地方为扩展编写泛型`where`子句。用都好分隔列表中的每个要求。

## 泛型Where子句的关联类型

可以在关联类型中包含泛型`where`子句。例如，假如你想写一个版本的`Container`，该版本包含一个如标准库中`Sequence`协议的迭代器。你会这么写：
```swift
protocol Container {
    associatedtype Item
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }

    associatedtype Iterator: IteratorProtocol where Iterator.Element == Item
    func makeIterator() -> Iterator
}
```

`Iterator`的泛型`where`子句要求迭代器穿过与容器项类型相同的元素，而不管迭代器的类型。`makeIterator()`函数提供了堆容器迭代器的访问。

对于一个继承自另一个协议的协议，通过在协议声明中添加泛型`where`子句以给继承而来的关联类型添加约束。例如，下面的代码声明了一个`ComparableContainer`协议，该协议要求`Item`实现了`Comparable`协议：
```swift
protocol ComparableContainer: Container where Item: Comparable { }
```

## 泛型下标

下标也可以是泛型，它们可以包含泛型`where`子句。在下标后面的尖括号中编写类型名占位符，在下标体开始大括号前编写泛型`where`子句。例如：
```swift
extension Container {
    subscript<Indices: Sequence>(indices: Indices) -> [Item] where Indices.Iterator.Element == Int {
        var result = [Item]()
        for index in indices {
            result.append(self[index])
        }
        return result
    }
}
```

这个扩展添加了一个下标，该下标接受一个索引序列，然后返回一个包含给定索引项的数组。这个泛型下标约束如下：
+ 尖括号中的泛型参数`Indices`必须是一个实现了标准库中`Sequence`协议的类型。
+ 下标接受单个参数，`indices`，是一个`Indices`实例。
+ 泛型`where`子句要求序列的迭代器必须穿过`Int`类型元素。这确保序列中索引的类型与容器使用的索引类型相同。

这些约束放在一起，意思是：传递给`indices`参数的值是一个整形序列。

[< 协议](Protocols.md) || [不透明类型 >](Opaque_Types.md)
