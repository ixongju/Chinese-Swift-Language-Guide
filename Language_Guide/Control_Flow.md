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




[< 集合类型](Collection_Types.md) || [函数 >](Functions.md)
