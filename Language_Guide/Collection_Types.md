# 集合类型

Swift提供三种基本集合类型，被称为数组，无序集合(Set)和字典，来储存值的集合。数组是有序的值集合。Set是无序且不重合的值的集合。字典是无序的键值对。

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/CollectionTypes_intro_2x.png" alt="集合类型" width="600"/>
</p>

Swift中的数组，无序集合和字典总是知道它们所储存的值的类型。这意味着你无法无意插入错误类型的值到集合中。也意味着你能对从集合中拿到的值的类型充满自信。
> 注意：
Swift的数组，无序集合和字典类型实现来范型。更多关于范型，请看[范型](Generics.md)。

## 集合的可变性

如果你创建数组，无序集合或字典，然后赋值给变量，则创建的结合将是 *可变的(mutable)*。这意味着在集合被创建之后，你可以通过添加，移除或改变集合中的元素来修改集合。如果你赋值给常量，则该集合是 *不可变的(immutable)*，内容和大小都不能被你改变。

> 注意：
在集合不需要改变的情况下，最好创建不可变的集合。这样做可以使你更容易敲定代码，并使Swift编译器能帮你优化你所创建的集合的性能。

### 数组

数组用一个有序序列来储存相同类型的值。同一个值可以在一个数组中的不同地方出现若干次：
> 注意：
Swift的数组类型桥接于Foundation框架的`NSArray`类。
关于更多与Foundation和Cocoa框架一起使用`Array`的信息，参见[桥接Array与NSArray](https://developer.apple.com/documentation/swift/array#2846730)

#### 数组类型速记语法

Swift数组类型完整写法是`Array<Element>`，其中`Element`是数组可以储存的值的类型。你也可以用速记语法`[Element]`写数组的类型。虽然这两种写法在功能上相同，当提到数组的类型时，速记语法是首选，并被广泛使用在本指南中。

#### 创建空数组

你可以用初始化语法创建某一类型的空数组：
```swift
var someInts = [Int]()
print("someInts is of type [Int] with \(someInts.count) items.")
// Prints "someInts is of type [Int] with 0 items."
```

注意，变量`someInts`的类型可以从初始化起的类型推导出为`[Int]`。

另外，如果上下文已经提供了类型信息，例如函数的实参或已经有类型的常量或变量，你可以直接用空数组字面量创建一个空数组，写作`[]`(一对空的中括号)：
```swift
someInts.append(3)
// someInts now contains 1 value of type Int
someInts = []
// someInts is now an empty array, but is still of type [Int]
```

#### 用默认值创建数组











[< 字符串和字符](Strings_and_Characters.md) || [控制流 >](Control_Flow.md)
