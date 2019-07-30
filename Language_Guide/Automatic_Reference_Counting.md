# 自动引用计数
Swift用_自动引用计数_(ARC)来跟踪和管理app的内存使用情况。在大多数情况下，这意味着在Swift中内存管理自动有效，而你不需要考虑内存管理。当类的实例不再需要的时候，ARC会自动释放其占用的内存。

但是，在少数情况下，为了管理内存，ARC需要更多关于代码不同部分之间的关系。这个章节将讨论这些情况，并向你展示如何用ARC管理app的内存。在Swift中用ARC与[Transitioning to ARC Release Notes](https://developer.apple.com/library/archive/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html)中描述的在Objective-C中的使用方法相似。

引用计数只针对类的实例。结构体和枚举是值类型，不是引用类型，不使用引用来存储和传递。

## ARC如何工作

每当创建类的实例，ARC会分发一段内存来存放该实例。这个段内存存放了实例的类型，和实例所有储存属性和其关联值的信息。

另外，当一个实例不再使用，ARC会释放该实例所占内存，以留作他用。这确保无用的实例不会占用内存。

但是，如果ARC试图回收被引用的实例，就再也不可能访问该实例的属性，会调用该实例的方法。实际上，如果你试图访问该实例，你的app多半会崩溃。

为了确保仍然有用的实例不会消失，ARC会跟踪每个类实例的属性，常量和变量的引用数。如果至少有一个引用指向了实例，ARC就不会回收该实例。

要实现这个，每当你为类实例指定属性，常量或变量时，该属性，常量或变量_强引用_该实例。该引用被称为“强”引用，因为它坚定地持有了该实例，只要该引用存在，就不会允许实例被回收。

## ARC原理

下面是自动引用计数如何工作的例子。例子以类`Person`开始，该类定义了一个存储常量属性`name`:
```swift
class Person {
  let name: String
  init(name: String) {
    self.name = name
    print("\(name) is being initialized")
  }
  deinit {
    print("\(name) is being deinitialized")
  }
}
```

`Person`类有一个设置属性`name`的初始化方法，并且在初始化是打印信息。`Person`类也有一个反初始化方法，当类被回收时，也会打印信息。

下面的代码中，定义了三个`Person?`类型的变量，在后面的代码中，它们被用于向新`Person`实例设置多个引用。因为这些变量是可选类型（`Person?`，而不是`Person`），它们会自动初始化为`nil`，并且不立即引用一个`Person`实例。
```swift
var reference1: Person?
var reference2: Person?
var reference3: Person?
```

现在创建一个新的`Person`实例，将它赋值给上面三个变量之一：
```swift
reference1 = Person(name: "John Appleseed")
// Prints "John Appleseed is being initialized"
```

注意到当调用`Persion`类初始化方法时，信息"`John Appleseed is being initialized`"会被打印。这表明初始化发生了。

因为新的`Person`实例被赋值给了`reference1`变量，因此`reference1`对该实例有强引用。因为至少有一个强引用，ARC确保该实例一直在内存中，且不会被回收。

如果将同一个`Person`实例赋值给两个以上的变量，则ARC认定该实例有两个以上的强引用：
```swift
reference2 = reference1
reference3 = reference1
```

现在有三个强引用指向了一个`Person`实例。

如果通过赋值`nil`给这两个变量，终止2个强引用（包括原始引用），则只剩下一个强引用，`Person`尚未被回收。
```swift
reference1 = nil
reference2 = nil
```

第三个，也就是最后一个强引用被终止前，ARC不会回收该实例，最后一个引用被终止的时候，说明你不再需要该实例：
```swift
reference3 = nil
// Prints "John Appleseed is being deinitialized"
```

## 类实例间的强循环引用

上面的例子中，







[< 透明类型](Opaque_Types.md) || [内存安全 >](Memory_Safety.md)
