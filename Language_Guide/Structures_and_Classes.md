# 结构体和类

结构体和类是通用的，灵活的结构，它们是程序代码的构建块。用与定义常量，变量和函数一样的语法，定义属性和方法为结构体和类添加功能。

不像其他编程语言，Swift不需要你为结构体和类创建接口和实现分开的文件。在Swift中，在单个文件中定义结构体或类，类或结构体的对外接口自动对外部代码可用。

> 注意：
传统地，一个类的实例本称为对象。然而，Swift的结构体和类与其他语言相比，在功能上更接近实例，且本节大部分内容描述应用于类或结构体实例的功能。因此，将使用更通用的叫法：*实例*。

## 比较结构体和类

Swift中的结构体和类有许多共同点。都能：
* 定义属性存放值
* 定义方法提供功能
* 定义下标提供用下标语法访问它们的值
* 定义初始化方法设置初始化状态
* 可被扩展，增强超出默认实现的功能
* 实现协议以提供某种类型的标准功能

更多信息，参见[属性](Properties.md)，[方法](Methods.md)，[下标](Subscripts.md)，[初始化](Initialization.md)，[扩展](Extensions.md)和[协议](Protocols.md)。

类支持的额外功能以增加复杂性为代价。作为通用指南，优先使用结构体因为更容易理解，当需要用到类或用类比较合适时，才用类。实践中，意味着用结构体和枚举定义大多数自定义的数据类型。更多详情，参见[结构体与类之间的选择](https://developer.apple.com/documentation/swift/choosing_between_structures_and_classes)。

## 定义语法

结构体和类有着类似的定义语法。用`struct`关键字定义结构体，用`class`关键字定义类。它们都把全部的定义放在一对大括号中：
```swift
struct SomeStructure {
  // structure definition goes here
}
class SomeClass {
  // class definition goes here
}
```

> 注意：
无论何时，定义新结构体或类时，你定义了一个新Swift类型。给类型一个大写开头的驼峰名（如`SomeStructure`和`SomeClass`）以匹配标准Swift类型的大小写（如`String`，`Int`和`Bool`）。用小写字母开头的驼峰命名法命名属性和变量（如`frameRate`和`incrementCount`），以与类型名区分开。

下面是定义结构体和类的例子：
```swift
struct Resolution {
  var width = 0
  var height = 0
}
class VideoMode {
  var resolution = Resolution()
  var interlaced = false
  var frameRate = 0.0
  var name: String?
}
```













[< 枚举](Enumeration.md) || [属性 >](Properties.md)
