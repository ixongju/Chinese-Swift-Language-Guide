# 集合类型

Swift提供三种基本集合类型，被称为数组，无序集合(Set)和字典，来储存值的集合。数组是有序的值集合。Set是无序且不重合的值的集合。字典是无序的键值对。

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/CollectionTypes_intro_2x.png" alt="集合类型" width="600"/>
</p>

Swift中的数组，无序集合和字典总是知道它们所储存的值的类型。这意味着你无法无意插入错误类型的值到集合中。也意味着你能对从集合中拿到的值的类型充满自信。
> 注意：
Swift的数组，无序集合和字典类型实现来范型。更多关于范型，请看[范型](Generics.md)。

## 集合的可变性

如果你创建数组，无序集合或字典，然后赋值给变量，则创建的结合将是 *可变的(mutable)*。这意味着在集合被创建之后，你可以通过添加，移除或改变集合中的元素来修改集合。如果你赋值给常量，则该集合是 *不可变的(immutable)*，内容和大小都不能被你改变。

> 注意：
在集合不需要改变的情况下，最好创建不可变的集合。这样做可以使你更容易敲定代码，并使Swift编译器能帮你优化你所创建的集合的性能。

### 数组

数组用一个有序序列来储存相同类型的值。同一个值可以在一个数组中的不同地方出现若干次：
> 注意：
Swift的数组类型桥接于Foundation框架的`NSArray`类。
关于更多与Foundation和Cocoa框架一起使用`Array`的信息，参见[桥接Array与NSArray](https://developer.apple.com/documentation/swift/array#2846730)

#### 数组类型速记语法

Swift数组类型完整写法是`Array<Element>`，其中`Element`是数组可以储存的值的类型。你也可以用速记语法`[Element]`写数组的类型。虽然这两种写法在功能上相同，当提到数组的类型时，速记语法是首选，并被广泛使用在本指南中。

#### 创建空数组

你可以用初始化语法创建某一类型的空数组：
```swift
var someInts = [Int]()
print("someInts is of type [Int] with \(someInts.count) items.")
// Prints "someInts is of type [Int] with 0 items."
```

注意，变量`someInts`的类型可以从初始化起的类型推导出为`[Int]`。

另外，如果上下文已经提供了类型信息，例如函数的实参或已经有类型的常量或变量，你可以直接用空数组字面量创建一个空数组，写作`[]`(一对空的中括号)：
```swift
someInts.append(3)
// someInts now contains 1 value of type Int
someInts = []
// someInts is now an empty array, but is still of type [Int]
```

#### 用默认值创建数组

Swift提供创建将所有元素设定为同一个值的固定大小的数组的初始化方法。向此初始化方法传入适当类型的值（称为`repeating`），和该值在数组中重复的次数（称为`count`）：
```swift
var threeDoubles = Array(repeating: 0.0, count: 3)
// threeDoubles is of type [Double], and equals [0.0, 0.0, 0.0]
```

#### 通过两个数组相加创建新数组

可以通过加号运算符将两个含有兼容类型值的数组相加的方式创建新数组。新数组的类型通过被相加的两个数组类型推导而来：
```swift
var anotherThreeDoubles = Array(repeating: 2.5, count: 3)
// anotherThreeDoubles is of type [Double], and equals [2.5, 2.5, 2.5]

var sixDoubles = threeDoubles + anotherThreeDoubles
// sixDoubles is inferred as [Double], and equals [0.0, 0.0, 0.0, 2.5, 2.5, 2.5]
```

#### 用数组字面量创建数组

也可以用数组字面量来初始化数组，这是创建含有一个或多个值数组的快捷方法。数组字面量被写作一个值列表，用逗号分隔，用中括号括起来：[`value1`, `value2`, `value3`]

下面的例子中，创建一个`shoppingList`数组用来存放`String`值：
```swift
var shoppingList: [String] = ["Eggs", "Milk"]
// shoppingList has been initialized with two initial items
```

变量`shoppingList`被声明为“一个字符串数组”，写作`[String]`。因为这个数组被指定来元素类型`String`，所以它指被允许用来存放字符串。在这里，数组`shoppingList`被两个字符串初始化，这两个字符串被写成数组字面量。
> 注意：
> `shoppingList`被声明为变量（使用了`var`关键字）而不是常量（`let`关键字），是因为接下来的例子中，会向其中添加更多的元素。

在这种情况下，数组字面量仅仅包含两个字符串。这满足了`shoppingList`变量所声明的类型（一个只能存放字符串的数组），所以数组字面量的赋值被允许用来初始化`shoppingList`。

这要感谢Swift的类型推导，如果你用含有相同类型元素的数组字面量初始化数组，你并不需要写上数组的类型。（译者：不要这么骄傲，很多语言都有类型推导的功能哦）`shoppingList`的初始化可以写得更简洁：
```swift
var shopping1 = ["Eggs", "Milk"]
```

因为字面量中所有值是相同类型，Swift可以推导出`[String]`是`shoppingList`的正确类型。

#### 访问和修改数组

可以通过数组的方法和属性或下标语法来访问和修改数组。

要获取数组中元素的个数，用只读属性`count`：
```swift
print("The shopping list contains \(shoppingList.count) items.")
// Prints "The shopping list contains 2 items."
```

用布尔属性`isEmpty`作为检查`count`属性是否为`0`等快捷方法：
```swift
if shoppingList.isEmpty {
    print("The shopping list is empty.")
} else {
    print("The shopping list is not empty.")
}
// Prints "The shopping list is not empty."
```

可以通过调用`append(_:)`方法向数组末尾添加新元素：
```swift
shoppingList.append("Flour")
// shoppingList now contains 3 items, and someone is making pancakes
```

另外，用加法赋值符号(`+=`)向数组末尾添加含有一个或多个兼容元素的数组：
```swift
shoppingList += ["Baking Powder"]
// shoppingList now contains 4 items
shoppingList += ["Chocolate Spread", "Cheese", "Butter"]
// shoppingList now contains 7 items
```

用*下标语法*从数组中获取一个值，在数组名后面写上用方括号括起的你想获取的值的索引：
```swift
var firstItem = shoppingList[0]
// firstItem is equal to "Eggs"
```
> 注意：
> 数组中的第一个值索引为`0`，而不是`1`。Swift中的数组索引从零开始。

可以用下标语法修改指定索引对应的已经存在的值：
```swift
shoppingList[0] = "Six eggs"
// the first item in the list is now equal to "Six eggs" rather than "Eggs"
```

当使用下标语法时，你所以定的索引必须有效。例如，`shoppingList[shoppingList.count] = "Salt"`试图向数组的末尾添加元素，将导致运行时错误。

也可以用下标语法一次性改变一段范围的值，即使用来替换范围与被替换的范围长度不同。下面的例子将`Chocolate Spread`，`Cheese`和`Butter`替换为`Bananas`和`Apples`：
```swift
shoppingList[4...6] = ["Bananas", "Apples"]
// shoppingList now contains 6 items
```

要将一个元素插入到指定索引位置，调用`insert(_:at:)`方法：
```swift
shoppingList.insert("Maple Syrup", at: 0)
// shoppingList now contains 7 items
// "Maple Syrup" is now the first item in the list
```

这里调用`insert(_:at:)`方法将一个新元素"Maple Syrup" 插入到购物清单数组的最前面，也就是索引为`0`的位置。

类似地，用`remove(at:)`方法移除一个元素。这个方法移除指定索引的元素然后返回该元素（如果不需要该返回值，可以忽略）：
```swift
let mapleSyrup = shoppingList.remove(at: 0)
// the item that was at index 0 has just been removed
// shoppingList now contains 6 items, and no Maple Syrup
// the mapleSyrup constant is now equal to the removed "Maple Syrup" string
```

> 注意：
> 如果你试图通过超出数组已有边界的索引来访问或修改数组的元素，将触发运行时错误。可以通过比较索引与`count`属性来检查该索引是够合法。因为数组的索引从零开始，所以最大的索引值是`count - 1`，但，当`count`是`0`时（数组为空），没有合法的索引。

移除元素时，数组中的间隔或被删除，如下，`Six eggs`再一次变成了索引为`0`的值：
```swift
firstItem = shoppingList[0]
// firstItem is now equal to "Six eggs"
```

如果要从数组中移除最后一个元素，用`removeLast()`方法而不是`remove(at:)`方法，以避免需要访问数组的`count`属性。像`remove(at:)`方法样，`removeLast()`方法也返回被移除的元素：
```swift
let apples = shoppingList.removeLast()
// the last item in the array has just been removed
// shoppingList now contains 5 items, and no apples
// the apples constant is now equal to the removed "Apples" string
```

#### 遍历数组

可以用`for-in`循环遍历数组的所有值：
```swift
for item in shoppingList {
  print(item)
}
// Six eggs
// Milk
// Flour
// Baking Powder
// Bananas
```

 如果需要整数索引和值，用`enumerated()`方法遍历数组。对于数组中的每个元素，`enumerated()`方法返回一个整数与元素符合而成的元组。整数从零开始，每个元素加1；如果列举整个数组，这些整数将能匹配元素的索引。可以将元组拆解成临时常量或变量，作为遍历的一部分：
```swift
for (index, value) in shoppingList.enumerated() {
  print("Item \(index + 1): \(value)")
}
// Item 1: Six eggs
// Item 2: Milk
// Item 3: Flour
// Item 4: Baking Powder
// Item 5: Bananas
```

关于`for-in`循环，参见[For-In循环](Control_Flow.md#For-In循环)。

## 无序集合












[< 字符串和字符](Strings_and_Characters.md) || [控制流 >](Control_Flow.md)
