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



















[< 反初始化](Deinitialization.md) || [错误处理 >](Error_Handling.md)
