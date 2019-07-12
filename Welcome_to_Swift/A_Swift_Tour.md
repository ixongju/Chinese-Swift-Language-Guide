## 快速开始

按照传统，第一个程序是打印一个“Hello, world!”。在Swift中，只需要一行代码：
```swift
print("Hello, world!")
// Prints "Hello, world!"
```
如果你用过`C`或`Objective-C`，这个语法对你应该很熟悉。在Swift中，这一行代码是一个完整的程序。你不需要引入额外的输入/输出或处理字符串的库。在全局范围编写的代码会作为程序的入口，所以你不需要编写`main()`函数。也不需要在每个声明的末尾写上分号（;）。

本导览通过展示完成多种不同编程任务，来为你提供开始用Swift编写代码的足够信息。如果有不懂的地方，请不要担心，本指南后面会给出解释。

### 简单的值
用`let`创建常量，用`var`创建变量。在编译时不需要知道常量的值，但必须为常量指定一次值。这意味着你可以用常量定义指定一次多处使用的值。
```swift
var myVariable = 42
myVariable = 50
let myConstant = 42
```
一个常量或变量必须拥有与你所指定的值一致的类型。


[< 版本兼容](Version_Compatibility.md) || [基础 >](../Language_Guide/Basic_Operators.md)
