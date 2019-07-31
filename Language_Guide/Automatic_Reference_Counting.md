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

下面的例子与上面的例子相同，只有一个重要的不同。这次，`Apartment`类型的租户属性`tenant`被声明为弱引用：
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
  weak var tenant: Person?
  deinit { print("Apartment \(unit) is being deinitialized") }
}
```

两个变量(`john`和`unit4A`)之间的强引用和两个实例之间的链接跟以前一样。
```swift
var john: Person?
var unit4A: Apartment?

john = Person(name: "John Appleseed")
unit4A = Apartment(unit: "4A")

john!.apartment = unit4A
unit4A!.tenant = john
```

现在实例间的的引用情况如下：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/weakReference01_2x.png" alt="弱引用" width="600"/>
</p>

`Person`实例有一个指向`Apartment`实例的强引用，但`Apartment`实例有一个指向`Person`实例的***弱引用***。这意味着，当你给变量`john`赋值`nil`来终止其持有的强引用时候，`Person`实例不再应有强引用：
```swift
john = nil
// Prints "John Appleseed is being deinitialized"
```

因为`Person`实例不再有任何强引用，它会被回收，`tenant`属性会被设置为`nil`：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/weakReference02_2x.png" alt="弱引用" width="600"/>
</p>

仅存的指向`Apartment`实例的强引用是变量`unit4A`。如果终止该强引用，`Apartment`将没有任何强引用：
```swift
unit4A = nil
// Prints "Apartment 4A is being deinitialized"
```

因为`Apartment`实例不再有任何强引用，它也会被回收：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/weakReference03_2x.png" alt="弱引用" width="600"/>
</p>

> 注意：
> 在垃圾回收系统中，弱指针一般被用作实现简单的缓存机制，因为强引用只有在内存告急触发内存回收时才会被回收。但是，使用ARC，实例一旦失去强引用就会被回收，这时候用弱引用就不那么合适了。

### 不拥有引用

就像弱引用样，***不拥有引用*** 不强持有其所指向的实例。但是，不像弱引用的是，当另一个（被持有）的实例拥有相同会更长的生命周期时，使用***不拥有引用***。在属性或变量的声明前面标记`unowned`关键字以表明该引用是不拥有引用。

不拥有引用总是有一个值。因此，ARC不会将不拥有引用设置为`nil`，这意味着，需要将不拥有引用定义为非可选类型。
> 注意：
> 当你能确保引用总是指向一个不会被回收的实例的时候，使用不拥有引用。
> 如果你试图访问一个实例已经被回收了的不拥有引用，会得到一个运行时错误。

下面的例中，定义两个类，`Customer`和`CreditCard`，为一个银行用户和该用户可能用到的信用卡建模。两个类都将另一个类作为属性储存。这种关系具备创建强循环引用的潜能。

`Customer`和`CreditCard`的关系明显不同于上例中`Apartment`和`Person`的关系。在这个数据模型中，一个用户可能没有也可能有信用卡，但是一张信用卡一定关联于一个用户。一个`CreditCard`实例不会超过其指向的`Customer`的生命周期。要表明这个，`Customer`类有一个可选的`card`属性，同时，`CreditCard`类有一个不拥有的（非可选）`customer`属性。

更进一步，只有当传入一个数值和一个`customer`实例给`CreditCard`的自定义初始化方法，才能创建一个`CreditCard`实例。这确保了，当`CreditCard`实例被创建时，总是有一个与其关联的`customer`实例。

因为信用卡总是有一个用户，所以将其`customer`属性定义为不拥有引用，以避免出现强循环引用：
```swift
class Customer {
  let name: String
  var card: CreditCard?
  init(name: String) {
    self.name = name
  }
  deinit { print("\(name) is being deinitialized") }
}

class CreditCard {
  let number: UInt64
  unowned let customer: Customer
  init(number: UInt64, customer: Customer) {
    self.number = number
    self.customer = customer
  }
  deinit { print("Card #\(number) is being deinitialized") }
}
```

> 注意：
> `CreditCard`类的数组属性用的是`UInt64`类型，而不是`Int`，是为了确保在32位系统和64位系统中，该属性都足够存放16位卡号。

下面的代码片段定义一个可选`Customer`变量`john`，将不用于存放指定用户的引用。由于是可选的，这个变量初始值为`nil`：
```swift
var john: Customer?
```

现在可以创建一个`Customer`实例，用它初始化并赋值一个用作用户实例`card`属性的`CreditCard`实例：
```swift
john = Customer(name: "John Appleseed")
john!.card = CreditCard(number: 1234_5678_9012_3456, customer: john!)
```

链接这两个实例后，引用情况如下：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/unownedReference01_2x.png" alt="不拥有引用" width="600"/>
</p>

`Customer`实例持有`CreditCard`实例的强引用，`CreditCard`实例持有`Customer`实例的不拥有引用。

因为`customer`的不拥有引用，当终止了变量`john`持有的强引用时，`Customer`实例将不再有任何强引用：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/unownedReference02_2x.png" alt="不拥有引用" width="600"/>
</p>

因为`Customer`实例没有了强引用，它将被回收。这之后，`CreditCard`实例也没有强引用，它也被回收：
```swift
 john = nil
 // Prints "John Appleseed is being deinitialized"
 // Prints "Card #1234567890123456 is being deinitialized"
```

上面最后的代码片段显示，变量`john`被设置成`nil`后，`Customer`实例和`CreditCard`实例的反初始化方法都打印出了反初始化信息。

> 注意：
> 上看的例子展示了如何使用不拥有引用。Swift也提供了*unsafe unowned*引用，以应对需要禁用运行时安全检查的情况---例如，出于性能考虑，对于不安全操作，由你自己来负责代码的安全检查。
> 用`unowned(unsafe)`关键表明非安全不拥有引用。如果在实例被回收之后试图访问非安全不拥有引用，程序会试着访问该实例过去的内存位置，这个操作是不安全的。

## 不拥有引用和隐式解包可选属性

上面关于弱引用和不拥有引用的例子，介绍了两个有必要打破强循环引用更常见的场景。

`Person`和`Apartment`的例子展示了允许两个属性为`nil`，可能引起强循环引用的情形。这个可以用弱引用很好地解决。

`Customer`和`CreditCard`的例子则展示了允许一个属性为`nil`，另一个属性不能为`nil`，可能引起强循环引用的情形。可以用不拥有引用很好地解决。

但是，有第三中情形，两个属性都必须有值，且初始化之后任何一个属性不能为`nil`。这种情况下，用隐式解包可选属性将不拥有属性的类与另一个类链接起来。

这确保一旦初始化完成，两个属性都可以直接被访问（不用可选解包），且避免了循环引用。本节介绍如何缔结这种关系。

下面的例子中，定义了两个类，`Country`和`City`，每个类保存另一个类的实例作为属性。在这个数据模型中，每个国家必须有一个首都城市，每个城市都应该属于某一个国家。为了实现这个，`Country`类有一个`capitalCity`属性，`City`类有一个`country`属性：
```swift
class Country {
  let name: String
  var capitalCity: City!
  init(name: String, capitalName: String) {
    self.name = name
    self.capitalCity = City(name: capitalName, country: self)
  }
}

class City {
  let name: String
  unowned let country: Country
  init(name: String, country: Country) {
    self.name = name
    self.country = country
  }
}
```

为了构建这两个类的相互依赖关系，`City`的初始化方法需要一个`Country`实例，并将该实例存入`country`属性中。

`City`的初始化方法在`Country`初始化方法中被调用。但是，`Country`实例被完全初始化之前，在`Country`的初始化方法中不能将`self`传给`City`的初始化方法，如[两步初始化](Initialization.md#两步初始化)中所述。

为了应付这种需求，将`Country`类的`capitalCity`属性声明为*隐式解包可选属性*，在类型后面标上感叹号(!)。这意味着`capitalCity`属性有默认值`nil`，就像其他可选值样，但不需要解包储存的值得。如[隐式解包可选值](The_Basic.md#隐式解包可选值)中所述。

因为`capitalCity`有默认值`nil`，当`Country`实例在初始化方法中设置`name`属性时，`Country`实例被认为已经完全初始化。这意味着一旦`name`属性被设置，`Country`初始化方法可以开始引用并传递隐式`self`属性。因此，在`Country`的初始化方法中设置`capitalCity`属性时，可以将`self`用作`City`初始化方法的参数。

所有这些表明，你可以在单个语句中创建`Country`和`City`的实例，而不会导致强循环引用，且不用感叹号解包，`capitalCity`属性可以被直接访问：
```swift
var country = Country(name: "Canada", capitalName: "Ottawa")
print("\(country.name)'s capital city is called \(country.capitalCity.name)")
// Prints "Canada's capital city is called Ottawa"
```

上面的例子中，使用隐式解包可选值意味着，所有的两步初始化的要求得到满足。一旦初始化完成，`capitalCity`属性可以像非可选值样被使用和访问，同时也避免了强循环引用。

## 闭包的强循环引用













[< 透明类型](Opaque_Types.md) || [内存安全 >](Memory_Safety.md)
