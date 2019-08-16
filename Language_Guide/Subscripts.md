# 下标

类，结构体和枚举可以定义下标，下标用于快捷访问集合，列表或序列的元素。用下标通过索引设置或获取值，而不需要单独的方法。例如，用`someArray[index]`访问数组实例中的元素，用`Dictionary[key]`访问字典实例中的元素。

你可以为一个类型定义多个下标，基于传入的索引类型从重载的下标中选择合适的下标。下标不限于单一维度，你可以定义有多个输入参数的下标去适应自定义类型的需要。

## 下标语法

下标可以让你通过在实例名后放括号内写上一个或多个值，来查询某一类型的实例。它们的语法与实例方法和计算属性语法类似。用`subscript`关键字定义下标，并如实例方法一样，指定一个或多个参数和一个返回值。与实例方法不同的是，下标可以是读写或只读。这个行为用与计算属性相同的getter方法和setter方法来实现：
```swift
subscript(index: Int) -> Int {
  get {
    // Return an appropriate subscript value here.
  }
  set(newValue) {
    // Perform a suitable setting action here.
  }
}
```

`newValue`的类型与下标返回值的类型相同。如计算属性样，你可以选择不指定setter方法的`newValue`参数。如果不指定的话，一个默认参数名`newValue`将被提供给setter方法。

如只读计算属性，可以通过移除`get`关键字和其括号，简化声明一个只读下标：
```swift
subscript(index: Int) -> Int {
  // Return an appropriate subscript value here.
}
```

下面是一个只读下标实现的例子，例中定义了一个`TimesTable`结构体代表一个整数乘n表：
```swift
struct TimesTable {
    let multiplier: Int
    subscript(index: Int) -> Int {
      return multiplier * index
    }
}
let threeTimesTable = TimesTable(multiplier: 3)
print("six times three is \(threeTimesTable[6])")
// Prints "six times three is 18"
```

在这个例子中，常见一个`TimesTable`实例代表一个乘3表。通过传入3给结构体的初始化方法，作为实例参数`multiplier`来创建乘3表。

如上所示的`threeTimesTable[6]`，可以通过调用下标来查询`threeTimesTable`实例。这从乘3表请求第六个条目，会返回`18`或`3 * 6`。

> 注意：
一个乘n表是基于固定的数学规律。它不适合为`threeTimesTable[someIndex]`设置新值，所以`TimesTable`的下标被定义为只读下标。

## 下标应用

下标的具体意义依赖于其上下文。下标一般被用于快捷地访问集合，列表或序列的成员元素。你可以为特定的类或结构体功能实现最合适的下标。

例如，Swift的字典`Dictionary`类型实现了一个设置和获取字典实例中的值的下标。你可以在下标括号中提供字典键类型的键，指定一个字典值类型的值给下标：
```swift
var numberOfLegs = ["spider": 8, "ant": 6, "cat": 4]
numberOfLegs["bird"] = 2
```

上例中，定义了一个变量`numberOfLegs`，用包含三个键值对的字典字面量初始化。`numberOfLegs`字典的类型被推导为`[String: Int]`。创建字典后，用下标赋值想字典添加一个字符串键`bird`和一个整数值`2`。

更多关于字典下标，参见[访问和修改字典](Collection_Types.md#访问和修改字典)。

> 注意：
Swift的字典类型实现了接受并返回一个可选类型的键值下标。对于上面的`numberOfLegs`，键值下标接受并返回一个`Int?`类型的值，或一个“可选整数”。字典类型用一个可选下标类型模拟并非每一个键都有对应的值，并提供通过给键赋值`nil`的方法删除该键对应的值。

## 下标选项

下标可以接受任意数量的输入参数，这些参数可以是任何类型。下标也可以返回任何类型。下标可以用可变参数，且可以提供默认参数，但不能使用输入输出参数。

一个类或结构体可以根据需要提供任意多的下标实现。适当的下标会在下标被使用时，根据值的类型或下标括号中值的类型推导出来。















[< 方法](Methods.md) || [继承 >](Inheritance.md)
