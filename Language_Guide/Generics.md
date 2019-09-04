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


## 扩展一个泛型














[< 协议](Protocols.md) || [不透明类型 >](Opaque_Types.md)
