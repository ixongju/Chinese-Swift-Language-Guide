# 扩展

*扩展* 给现有的类，结构体，枚举或协议类型添加新功能。包括为无法访问源码的类型添加扩展（被称为*逆向建模*）。扩展跟Objective-C中的类别(`category`)类似。（与Objective-C的类别不同的是，Swift的扩展没有名称。）

Swift中的扩展可以：
* 添加计算实例属性和计算类属性
* 定义实例方法和类型方法
* 定义下标
* 定义和使用嵌套类型
* 使现有的类型实现一个协议

在Swift中，你甚至可以扩展一个协议，以提供协议规定的实现，或添加实现了协议的类型能使用额外功能。更多详细内容，参见[协议扩展](Protocols.md#协议扩展)。

> 注意：
扩展可以为一个类型添加新功能，但是不能重写现有的功能。

## 扩展语法

用`extension`关键字声明扩展：
```swift
extension SomeType {
  // new functionality to add to SomeType goes here
}
```

扩展可以让一个现有类型实现一个或多个协议。要遵循协议，像为类或结构体编写协议一样为扩展编写协议：
```swift
extension SomeType: SomeProtocol, AnotherProtocol {
  // implementation of protocol requirements goes here
}
```

用这种方式遵循协议在[用扩展添加协议](Protocols.md#用扩展添加协议)中有详述。

扩展可被用于扩展现有的泛型类型，如[扩展一个泛型](Generics.md#扩展一个泛型)中所述。也可以扩展一个泛型类型以有条件地添加功能，如[扩展泛型Where子句](Generics.md#扩展泛型Where子句)中所述。

> 注意：
如果定义一个扩展为现有类型添加新功能，该功能在该类型的所有实例中都可用，即使这些实例在定义扩展之前已经被创建。

## 计算属性

扩展可以为现有的类型添加计算实例属性和计算类型属性。这个例子为Swift内建类型`Double`添加五个计算属性，为使用距离单位提供支持：
```swift
extension Double {
  var km: Double { return self * 1_000.0 }
  var m: Double { return self }
  var cm: Double { return self / 100.0 }
  var mm: Double { return self / 1_000.0 }
  var ft: Double { return self / 3.28084 }
}
let oneInch = 25.4.mm
print("One inch is \(oneInch) meters")
// Prints "One inch is 0.0254 meters"
let threeFeet = 3.ft
print("Three feet is \(threeFeet) meters")
// Prints "Three feet is 0.914399970739201 meters"
```













[< 嵌套类型](Nested_Types.md) || [协议 >](Protocols.md)
