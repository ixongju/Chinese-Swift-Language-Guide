# 可选链

*可选链* 是在一个可能为`nil`的可选对象上请求或调用属性，方法和下标的过程。如果可选对象包含值，则属性，方法或下标能成功调用；如果可选对象是`nil`，属性，方法和下标调用会返回`nil`。多个请求会链接在一起，如果链中的任何环节为`nil`，则整个链失败。

> 注意：
> Swift中的可选链与Objective-C中的`nil`消息类似，但是可选链适用于任何类型，且可以检查成功与失败。

## 可选链作为强制解包的替代方法

如果可选值为非`nil`，则通过在要调用属性，方法或下标的可选值后面写上问号(`?`)来指定可选链。这与在可选值后面写上感叹号(`!`)来强制解包包含的值类似。主要不同在于，当可选值为`nil`时，可选链的失败更优雅，而强制解包会触发运行时错误。

为了反应可以在`nil`上调用可选链的事实，即便请求的属性，方法或下标的返回值为非可选值，可选链调用的结果总是可选值。你可以用这个可选返回值检查可选链调用是否成功（返回的可选值包含值），或因为链中有`nil`而失败（返回的可选值是`nil`）。

特别地，可选链调用的结果与期待返回值的类型相同，只是包含在可选值中。通过可选链访问时，一个正常返回`Int`的属性会返回`Int?`。

下面的代码片段，演示了可选链如何与强制解包的不同，及如何让你检查是否成功：
```swift
class Person {
    var residence: Residence?
}
class Residence {
    var numberOfRooms = 1
}
```

`Residence`实例有仅有一个`Int`属性`numberOfRooms`，默认值是`1`。`Person`实例有一个`Residence?`类型的可选属性`residence`。

如果你创建一个`Person`实例，得益于是可选类型，其`residence`属性默认初始化为`nil`。下面的代码，`john`有一个值为`nil`的`residence`属性：
```swift
let john = Person()
```

如果你尝试着用感叹号强制解包的方式访问`residence`的`numberOfRooms`属性，将触发运行时错误，因为`residence`没有可被解包的值：
```swift
let roomCount = john.residence!.numberOfRooms
// this triggers a runtime error
```

当`john.residence`有一个非`nil`值时，上面的代码会成功，并将`roomCount`设置为表示房间数的`Int`类型的值。但是，当`residence`为`nil`时，这段代码总是触发运行时错误。

可选链提供了两外一种访问`numberOfRooms`值的方式。要是用可选链，用问号替换上面的感叹号：
```swift
if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")
}
// Prints "Unable to retrieve the number of rooms."
```

这告诉Swift将链接可选属性`residence`，当`residence`存在的时候获取`numberOfRooms`的值。

因为试图访问`numberOfRooms`有可能失败，可选链尝试返回`Int?`类型的值，或“可选`Int`”。当`residence`是`nil`，如上例，可选`Int`将是`nil`，以表明不可能再去访问`numberOfRooms`。通过可选链访问可选`Int`来解包整数，然后将非可选值赋值给`roomCount`变量。

注意，即使`numberOfRooms`是非可选`nil`也成立。通过可选链访问意味着调用`numberOfRooms`总是返回`Int?`类型值而不是`Int`类型的值。

可以给`john.residence`赋值`Residence`实例，这样它不再是`nil`：
```swift
john.residence = Residence()
```

`john.residence`现在包含一个实际的`Residence`实例，而不是`nil`。如果你试图用与前面相同的可选链访问`numberOfRooms`，会返回包含`numberOfRooms`默认值`1`的`Int?`类型值。
```swift
if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")
}
// Prints "John's residence has 1 room(s)."
```

## 为可选链定义模型类

可以用可选链调用深层次的属性，方法和下标。这使你可以深入探讨关联类型的复杂模型中的子属性，并检查是否能够通过这些子属性访问属性，方法和下标。

下面的代码片段定义了在后继例子中会用到的四个模型类，包括多层级可选链。通过添加`Room`和`Address`类和相关的属性，方法和下标，扩展了上例中的`Person`和`Residence`模型。

`Person`类定义跟上面一样：
```swift
class Person {
    var residence: Residence?
}
```

`Residence`类比以前复杂。这次，`Residence`类定义了一个变量属性`rooms`，用`[Room]`类型空数组初始化：
```swift
class Residence {
    var rooms = [Room]()
    var numberOfRooms: Int {
        return rooms.count
    }
    subscript(i: Int) -> Room {
        get {
            return rooms[i]
        }
        set {
            rooms[i] = newValue
        }
    }
    func printNumberOfRooms() {
        print("The number of rooms is \(numberOfRooms)")
    }
    var address: Address?
}
```

因为这个版本的`Residence`储存了`Room`实例的数组，`numberOfRooms`属性被实现为计算属性，而不是储存属性。计算属性`numberOfRooms`简单地返回`rooms`数组的`count`属性。

作为访问`rooms`数组的简单方式，这个版本的`Residence`提供了一个可读可写的下标，提供对`rooms`数组中指定索引位置的房间的访问。

这个版本的`Residence`提供了一个方法`printNumberOfRooms`，该方法简单地打印房间的数量。

最后，`Residence`定义了`Address?`类型的可选属性`address`。`Address`类型在下面有定义。

`rooms`数组的`Room`类是有一个`name`属性的简单类，并且有一个为该属性赋值的初始化方法：
```swift
class Room {
    let name: String
    init(name: String) { self.name = name }
}
```

本模型中最后一个类是`Address`。这个类有三个`String?`类型可选属性。前面两个属性，`buildingName`和`buildingNumber`，是用另一种方式将某一建筑标识为地址的一部分。第三个属性，`street`，用于表示地址的街道名：
```swift
class Address {
    var buildingName: String?
    var buildingNumber: String?
    var street: String?
    func buildingIdentifier() -> String? {
        if let buildingNumber = buildingNumber, let street = street {
            return "\(buildingNumber) \(street)"
        } else if buildingName != nil {
            return buildingName
        } else {
            return nil
        }
    }
}
```

`Address`类提供了一个返回`String?`类型的方法`buildingIdentifier()`。这个方法检查地址的属性，如果有值则返回`buildingName`，如果`buildingNumber`和`street`都有值，则返回街道和建筑编号信息，否则返回`nil`。

## 用可选链访问属性

如[可选链作为强制解包的替代方法](#可选链作为强制解包的替代方法)中所展示的，可以用可选链访问可选值的属性，和检查属性访问是否成功。

用上面定义的类创建一个`Person`实例，尝试像以前一样访问它的`numberOfRooms`属性：
```swift
let john = Person()
if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")
}
// Prints "Unable to retrieve the number of rooms."
```

因为`john.residence`是`nil`，跟前面一样可选链调用失败。

你可以试着用可选链设置属性值：
```swift
let someAddress = Address()
someAddress.buildingName = "29"
someAddress.street = "Acacia Road"
john.residence?.address = someAddress
```

这个例子中，尝试给`john.residence`的`address`属性赋值会失败，因为`john.residence`是`nil`。

赋值操作是可选链的一部分，这意味着等号(`=`)运算符右边的代码不会被计算。在前面的例子中，不容易看到`someAddress`没有被计算，因为访问常量没有副作用。下面的列表做同样的赋值操作，但是是用一个函数创建`address`。函数在返回之前会打印“Function was called”信息，这可以让你看到等号(`=`)右边的部分是否被计算。
```swift
func createAddress() -> Address {
    print("Function was called.")

    let someAddress = Address()
    someAddress.buildingNumber = "29"
    someAddress.street = "Acacia Road"

    return someAddress
}
john.residence?.address = createAddress()
```
你可以看出`createAddress()`函数没有被调用，因为没有打印信息。

## 用可选链调用方法

可以用可选链调用可选值的方法，并且可以检查调用是否成功。即使该方法没有返回值也能这么做。

`Residence`类的`printNumberOfRooms()`方法打印当前`numberOfRooms`的值。如下所示：
```swift
func printNumberOfRooms() {
    print("The number of rooms is \(numberOfRooms)")
}
```

这个方法没有指定返回类型。但是，没有返回类型的函数和方法有一个隐式返回类型`Void`，如[无返回值函数](Functions.md#无返回值函数)中所述。这意味着它们返回了`()`，或一个空元组。

如果在一个可选值上用可选链调用这个方法，则方法的返回类型是`Void?`，而不是`Void`，因为通过可选链调用时返回值总是可选类型。这使得你可以用`if`语句检查是否能调用`printNumberOfRooms()`方法，尽管该方法定义中没有返回值。通过将`printNumberOfRooms()`调用的返回值与`nil`对比判断方法调用有没有成功：
```swift
if john.residence?.printNumberOfRooms() != nil {
    print("It was possible to print the number of rooms.")
} else {
    print("It was not possible to print the number of rooms.")
}
// Prints "It was not possible to print the number of rooms."
```

试图用可选链数值属性值也是如此。[用可选链访问属性](#用可选链访问属性)中的例子试图为`john.residence`设置`address`，即使`residence`属性为`nil`。任何试图通过可选链为属性设置值都会返回一个`Void?`类型的值，这使得你可以将该值与`nil`对比判断是否成功为属性设置了值：
```swift
if (john.residence?.address = someAddress) != nil {
    print("It was possible to set the address.")
} else {
    print("It was not possible to set the address.")
}
// Prints "It was not possible to set the address."
```

## 用可选链访问下标

可以用可选链获取或设置可选值的下标，并检查下标调用是否成功。

> 注意：
> 当通过可选链访问可选值的下标，将问好放在下标括号之前，而不是之后。可选链问好总是在可选表达式之后。

下面的例子，试图通过`Residence`类中定义的下标，获取`john.residence`的属性`rooms`中第一个房间的名称。因为`john.residence`现在是`nil`，下标调用失败：
```swift
if let firstRoomName = john.residence?[0].name {
    print("The first room name is \(firstRoomName).")
} else {
    print("Unable to retrieve the first room name.")
}
// Prints "Unable to retrieve the first room name."
```

下标调用中的可选链问号被放在了`john.residence`后面，下标方括号之前，因为`john.residence`才是可选链试图访问的可选值。

类似地，可以用可选链通过下标设置值：
```swift
john.residence?[0] = Room(name: "Bathroom")
```

这个通过下标设置值的尝试也会失败，因为`residence`当前值是`nil`。

如果创建一个`rooms`数值有多个`Room`实例的`Residence`实例并赋值给`john.residence`，就可以用可选链通过`Residence`下标访问`rooms`数组中实际的项了：
```swift
let johnsHouse = Residence()
johnsHouse.rooms.append(Room(name: "Living Room"))
johnsHouse.rooms.append(Room(name: "Kitchen"))
john.residence = johnsHouse

if let firstRoomName = john.residence?[0].name {
    print("The first room name is \(firstRoomName).")
} else {
    print("Unable to retrieve the first room name.")
}
// Prints "The first room name is Living Room."
```

### 访问可选类型的下标

如果下标返回一个可选类型的值---例如Swift中`Dictionary`类型的键下标---在下标括号后面写上问号来链接其可选返回值：
```swift
var testScores = ["Dave": [86, 82, 84], "Bev": [79, 94, 81]]
testScores["Dave"]?[0] = 91
testScores["Bev"]?[0] += 1
testScores["Brian"]?[0] = 72
// the "Dave" array is now [91, 82, 84] and the "Bev" array is now [80, 94, 81]
```

上例中定义了一个字典`testScores`，该字典包含两对映射`String`键到一个`Int`数组的键值对。示例用可选链设置`"Dave"`对应的数组中第一个项为`91`；将`"Bev"`对应的数组中第一个项增加`1`；试图为`"Brain"`对应的数组的第一个项赋值。前面两个成功，因为`testScores`字典包含`"Dave"`和`"Bev"`两个键。第三个失败了，因为`testScores`字典不包含`"Brian"`键。

## 链接多级可选链

可以链接多个可选链从而深入访问模型内部的子属性，方法和下标。即便如此，多级可选链并不增加返回值的可选性。

也就是说：
* 如果你试图获取的类型不是可选的，因为是用可选链，它会变成可选的
* 如果你试图获取的类型是可选的，不会因为是可选链，就变成多级可选（可选值中包含的值仍然是可选值）

因此：
* 如果你试图通过可选链获取一个`Int`类型的值，会返回一个`Int?`类型的值，而不管用了多少级可选链
* 类似地，如果你试图通过可选链获取一个`Int?`类型的值，会返回一个`Int?`类型的值，而不管用了多少级可选链

下面的例子试图访问`john`的属性`residence`的属性`address`的属性`street`。这里使用了两级可选链，来链接`residence`和`address`属性，两者都是可选类型：
```swift
if let johnsStreet = john.residence?.address?.street {
    print("John's street name is \(johnsStreet).")
} else {
    print("Unable to retrieve the address.")
}
// Prints "Unable to retrieve the address."
```

`john.residence`现在包含一个有效的`Residence`实例。但是，`john.residence.address`当前为`nil`。因此，调用`john.residence?.address?.street`会失败。

注意到在上面的例子中，你试图获取`street`属性的值。该属性的类型是`String?`。因此，即使除了底层的属性是可选类型还使用了二级可选链，`john.residence?.address?.street`的返回值也是`String?`。

如果为`john.residence.address`设置一个真实的`Address`实例，并为`street`属性设置一个真实的值，就可以通过多级可选链访问`street`属性的值：
```swift
let johnsAddress = Address()
johnsAddress.buildingName = "The Larches"
johnsAddress.street = "Laurel Street"
john.residence?.address = johnsAddress

if let johnsStreet = john.residence?.address?.street {
    print("John's street name is \(johnsStreet).")
} else {
    print("Unable to retrieve the address.")
}
// Prints "John's street name is Laurel Street."
```

在这个例子中，尝试访问`john.residence`的`address`属性会成功，因为`john.residence`当前包含了一个有效的`Residence`实例。

## 在返回可选类型值的方法上使用可选链

前面的例子展示了如何通过可选链获取可选类型属性的值。也可以用可选链调用返回可选类型值的方法，且如果需要，可以在该返回值上再使用可选链。

下面的例子通过可选链调用`Address`类的`buildingIdentifier()`方法。这个方法返回一个`String?`类型的值。如上所述，用可选链调用这个方法后，最终返回类型仍然是`String?`：
```swift
if let buildingIdentifier = john.residence?.address?.buildingIdentifier() {
    print("John's building identifier is \(buildingIdentifier).")
}
// Prints "John's building identifier is The Larches."
```

如果想在方法的返回值上进一步使用可选链，可在方法的括号后面写上可选链问号：
```swift
if let beginsWithThe = john.residence?.address?.buildingIdentifier()?.hasPrefix("The") {
    if beginsWithThe {
        print("John's building identifier begins with \"The\".")
    } else {
        print("John's building identifier does not begin with \"The\".")
    }
}
// Prints "John's building identifier begins with "The"."
```

> 注意：
> 在上面的例子中，在括号后面写上可选链问号，是因为链接的是可选值是方法的返回值，而不是`buildingIdentifier()`方法本身。

[< 反初始化](Deinitialization.md) || [错误处理 >](Error_Handling.md)
