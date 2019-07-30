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

上面的例子中，ARC可以追踪`Person`实例的引用数，并且在不需要实例的时候将其回收。

但是，有可能写出一个类的实例无论如何强引用都不为零的代码。当两个实例相互持有强引用的时候，就会出现这种情况，这样两个实例都让对方存活。这被称为***强循环引用***。

通过把类之间的引用关系定义为`weak`或`unowned`，而不是`strong`，来解决强循环引用问题。这个过程在[解决类实例之间的强循环引用问题](#解决类实例之间的强循环引用问题)中描述。然而，在你学会如何解决强循环引用之前，最好先弄懂强循环引用是如何导致的。

下面是一个意外创建了强循环引用的例子。例子中定义了两个类`Person`和`Apartment`，来为公寓和其居民建模：
```swift
class Person {
  let name: String
  init(name: String) { self.name = name }
  var apartment: Apartment?
  deinit { print("\(name) is being deinitialized") }
}

class Apartment {
  let unit: String
  init(unit: String) { self.unit = unit }
  var tenant: Person?
  deinit { print("Apartment \(unit) is being deinitialized") }
}
```

每个`Person`实例有一个字符串属性`name`和一个默认为`nil`的`apartment`属性。`apartment`属性是可选值，因为一个人可能没有公寓。

类似地，每个`Apartment`实例有一个字符串属性`unit`和一个默认为`nil`的`tenant`属性。`tenant`属性是可选值，因为公寓不一定有人住。

两个类都定义了反初始化方法，在实例被回收时会打印信息。这能让你看到这两个类的实例是否如你所愿被回收。

下面的代码片段定义了两个可选类型变量`john`和`unit4A`，然后设定为指定的`Apartment`和`Person`实例。由于是可选值，这两个变量初始值都是`nil`：
```swift
var john: Person?
var unit4A: Apartment?
```

现在创建`Person`和`Apartment`实例，将它们赋值给`john`和`unit4A`：
```swift
john = Person(name: "John Appleseed")
unit4A = Apartment(unit: "4A")
```

下面是创建并赋值这两个实例之后，强引用的情况。`john`变量有一个`Person`实例的强引用，`unit4A`变量有一个`Apartment`实例的强引用：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/referenceCycle01_2x.png" alt="强循环引用" width="600"/>
</p>

现在可以将两个实例关联起来，让人拥有公寓，公寓拥有租户。注意，用感叹号(`!`)解包并访问变量`john`和`unit4A`中存放的实例，来设置实例的属性：
```swift
john!.apartment = unit4A
unit4A!.tenant = john
```

现在的强引用情况如下图：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/referenceCycle02_2x.png" alt="强循环引用" width="600"/>
</p>

不幸地是，链接两个实例导致了强循环引用。`Person`实例有了`Apartment`实例的强引用，`Apartment`实例有了`Person`实例的强引用。因此，当终止变量`john`和`apartment`持有的强引用时，强引用数并不减至零，ARC无法回收这两个实例。
```swift
john = nil
unit4A = nil
```

注意到当给两个变量赋值`nil`时，反初始化方法并没有被调用。强循环引用阻止了`Person`和`Apartment`实例被回收，这回导致App内存移除。

下面是给变量`john`和`unit4A`赋值`nil`后强引用情况：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/referenceCycle03_2x.png" alt="强循环引用" width="600"/>
</p>

`Person`实例和`Apartment`实例之间的强引用仍然存在，且无法被终止。

## 解决类实例之间的强循环引用问题

使用类类型属性时，Swift提供了两种解决强循环引用的方式：***弱引用*** 和***不拥有引用***。

弱引用和不拥有引用能使引用循环中的一个实例不用强引用的方式引用其他实例。实例相互引用，但不构成强循环引用。

当另一个实例有较短的生命周期时，用弱引用——也就是说，另一个实例可以被提前回收。在上例中，公寓在某个时间点可能没有租户，所以这种情况，适合用***弱引用***来打破强循环引用。相反，当另一个实例拥有相同或更长的生命周期时，使用***不拥有引用***。

### 弱引用

弱引用对所引用的实例不保持强拥有，它不阻止ARC回收被引用的实例。这个行为能防止该引用称为强循环引用的一部分。在属性或变量前面标记`weak`关键字以表明该引用是弱引用。

因为弱引用不强持有缩引用的实例，即使该弱引用仍然存在，该实例也可以被回收。因此，当被引用的实例被回收时，ARC会将该弱引用设置为`nil`。并且，因为弱引用需要允许值在运行时被改成`nil`，所以弱引用经常声明为变量，而不是常量或可选类型。

你可以检查弱引用的值是否存在，就像检查任何可选类型的值一样，你并不会得到一个不存在的无效的实例的引用。
> 注意：
当ARC将弱引用设置为`nil`时，属性观察者不会被调用。


















[< 透明类型](Opaque_Types.md) || [内存安全 >](Memory_Safety.md)
