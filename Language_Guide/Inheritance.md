# 继承

一个类可以从另一个类继承方法，属性和其他成员。当一个类继承另一个类时，继承的类被称为子类，被继承的类称为父类。在Swift中，继承是区分类与其他类型的基本行为。

Swift中的类可以调用和访问父类的方法，属性和下标，也可以重写这些方法，属性和下标，进而重新定义或改变它们的行为。Swift通过检查重写的定义是否有一个相匹配的父类定义来确保正确地重写。

为了在属性值变化是获取通知，类也可以给继承的属性添加属性观察者。不管最初定义成储存属性还是计算属性，都可以给任何属性添加观察者。

## 定义一个基础类

任何不继承其他类的类被称为基础类：

> 注意：
Swfit的类没有继承自一个通用基础类。不指定父类的情况下定义的类自动成为基础类。

下面的例子中，定义了一个基础类`Vehicle`。该基础类定义了储存属性`currentSpeed`，默认值为`0.0`（由此推导出类型为`Double`）。`currentSpeed`属性的值被一个`String`类型只读计算属性`description`用来创建对于车辆的描述。

基础类`Vehicle`也定义了一个方法`makeNoise`。对于基础类`Vehicle`的实例，该方法其实不做任何事情，但是在后面会被子类自定义：
```swift
class Vehicle {
    var currentSpeed = 0.0
    var description: String {
      return "traveling at \(currentSpeed) miles per hour"
    }
    func makeNoise() {
      // do nothing - an arbitrary vehicle doesn't necessarily make a noise
    }
}
```

用初始化语法创建一个新`Vehicle`实例，写为在类型名后面加上括号：
```swift
let someVehicle = Vehicle()
```

已经创建了一个新实例，你可以访问`description`属性打印一个人类可读的关于车辆当前速度的描述：
```swift
print("Vehicle: \(someVehicle.description)")
```

`Vehicle`类定义了任意车辆的通用特性，但本身并没有多大用处。为了让它实用，你需要重定义它以描述更具体的车辆。

## 子类化

子类化在已知类的基础上创建新类的行为。子类继承已知类可被重定义的特性。也可以给子类添加新特性。

要表明一个子类有父类，在父类名前面写上子类名，中间用冒号隔开：
```swift
class SomeClass: SomeSuperClass {
    // subclass definition goes here
}
```

下面的例子，用`Vehicle`作为父类，定义了一个子类`Bicycle`：
```swift
class Bicycle: Vehicle {
    var hasBasket = false
}
```

`Bicycle`类自动获得了父类`Vehicle`的所有特性，例如它拥有`currentSpeed`和`description`属性和`makeNoise`方法。

除了继承而来的特性，`Bicycle`类定义了一个新储存属性`hasBasket`，默认值是`false`（由此推导类型为`Bool`）。

默认地，任何`Bicycle`实例`hasBasket`为`false`。你可以在某一实例被创建后，将`hasBasket`设置为`true`：
```swift
let bicycle = Bicycle()
bicycle.hasBasket = true
```

也可以修改`Bicycle`实例继承而来的`currentSpeed`属性，然后查询实例继承的`description`属性：
```swift
bicycle.currentSpeed = 15.0
print("Bicycle: \(bicycle.description)")
// Bicycle: traveling at 15.0 miles per hour
```

子类也可以被子类化。下一个例子为双人自行车创建一个继承自`Bicycle`的子类：
```swift
class Tandem: Bicycle {
    var currentNumberOfPassengers = 0
}
```

`Tandem`继承了`Bicycle`的所有属性和方法，`Bicycle`继承了`Vehicle`的所有属性和方法。`Tandem`子类添加了一个新储存属性`currentNumberOfPassengers`，默认值为`0`。

如果创建一个`Tandem`实例，你可以实用其任何新的或继承而来的属性，查询继承自`Vehicle`的只读属性`description`：
```swift
let tandem = Tandem()
tandem.hasBasket = true
tandem.currentNumberOfPassengers = 2
tandem.currentSpeed = 22.0
print("Tandem: \(tandem.description)")
// Tandem: traveling at 22.0 miles per hour
```

## 重写

一个子类可以提供实例方法，类型方法，实例属性，类型属性或下标的自身实现，或者继承父类的实现。这被称为*重写*。

要重写继承而来的特性，在重写定义前面写上`override`关键字。这样做能表明你打算提供一个重写实现，而不是无意提供了一个重复实现。意外重写会导致不可预知的行为，任何没有`override`关键字的重写，在编译时都会被判断为错误。

`override`关键字会让Swift编译器检查重写的类的父类有一个相同的实现。这个检查确保重写是正确的。

### 访问父类方法，属性和下标

当为子类提供方法，属性或下标时，有时候把已知父类的实现用作重写的一部分比较有用。例如，你可以重新定义已知实现，或存在已知继承的变量里放一个修改了的值。

通过`super`前缀访问父类版本的方法，属性或下标：
* 一个被重写了的方法`someMethod()`可以在重写实现中用`super.someMethod()`调用父类版本的`someMethod()`。
* 一个被重写了的属性`someProperty`可以在重写的getter和setter方法中用`super.someProperty`访问父类版本的`someProperty`。
* 一个为`someIndex`被重写了的下标可以在重写下标实现中用`super[someIndex]`访问父类版本的下标。

### 重写方法

可以通过重写继承的实例方法或类型方法，为子类定制或替换方法实现。

下面的例子，定义了一个新的`Vehicle`的子类`Train`，该类重写了继承自`Vehicle`类的方法`makeNoise()`：
```swift
class Train: Vehicle {
    override func makeNoise() {
        print("Choo Choo")
    }
}
```

如果创建一个`Train`实例，并调用`makeNoise()`方法，你可以看到子类`Train`版本的方法被调用：
```swift
let train = Train()
train.makeNoise()
// Prints "Choo Choo"
```

### 重写属性

可以重写继承的实例或类型属性，来为属性提供自定义getter方法和setter方法，或为属性添加观察者以允许被重写的属性观察属性值的变化。

#### 重写属性的Getter和Setter方法

不管继承而来的属性在源码中是储存属性还是计算属性，你都可以提供自定义的getter方法（或setter方法，如果合适的话）来重写该属性。储存或计算的本质并不被子类所知——子类只知道继承而来的属性有一个名称和类型。你必须说明你要重写的属性的名称和类型，以确保编译器会检查你要重写的属性与父类某一属性有着相同的名称和类型。

> 注意：
如果你提供一个setter方法作为重写属性的一部分，你必须提供一个getter。如果你不打算在重写getter中修改属性的值，可以简单地从getter方法返回`super.someProperty`，`someProperty`是你要重写的属性。

下面的例子定义了一个新类`Car`，它是`Vehicle`的子类。`Car`有一个新储存属性`gear`，默认值是整数`1`。`Car`重写了继承自`Vehicle`的`description`属性，提供包含当前档位的自定义描述：
```swift
class Car: Vehicle {
    var gear = 1
    override var description: String {
        return super.description + " in gear \(gear)"
    }
}
```

`description`的重写以调用`super.description`开始，这返回`Vehicle`类的`description`属性。然后，`Car`的`description`属性在其后增加额外的文本，提供当前档位信息。

如果创建一个`Car`类的实例，设置`gear`和`currentSpeed`属性，你可以看到`description`属性返回`Car`中定义的定制之后的描述：
```swift
let car = Car()
car.currentSpeed = 25.0
car.gear = 3
print("Car: \(car.description)")
// Car: traveling at 25.0 miles per hour in gear 3
```

#### 重写属性观察者

可以用属性重写为继承的属性添加属性观察者。这让你在继承属性值改变时收到通知，而不论原始实现如何。更多关于属性观察者的信息，参见[属性观察者](Properties.md#属性观察者)。

> 注意：
不能为继承的常量储存属性或只读计算属性添加属性观察者。不能设置这些属性的值，所以为其重写提供`willSet`和`didSet`实现是不合适的。
注意你不能为同一属性同时提供重写的setter和重写的属性观察者。如果你想监视属性值的改变，且已经为该属性提供了自定义的setter方法，你可以直接在该setter方法中监视值的改变。

下面的例子定义了一个名为`AutomaticCar`的新类，她是`Car`的子类。`AutomaticCar`类代表自动挡汽车，它基于当前速度自动选择档位：
```swift
class AutomaticCar: Car {
    override var currentSpeed: Double {
        didSet {
            gear = Int(currentSpeed / 10.0) + 1
        }
    }
}
```

当你设置`AutomaticCar`实例的`currentSpeed`属性时，属性的`didSet`观察者为实例的`gear`属性设置相应的档位。特别地，属性观察者用`currentSpeed`除以`10`，向下舍入并加`1`的值，作为档位。`35.0`的速度对应的档位是`4`：
```swift
let automatic = AutomaticCar()
automatic.currentSpeed = 35.0
print("AutomaticCar: \(automatic.description)")
// AutomaticCar: traveling at 35.0 miles per hour in gear 4
```

## 防止重写

可以通过把方法，属性或下标标记为`final`来防止被重写。把`final`修饰符写在方法，属性或下标关键字前面（如`final var`，`final func`，`final class func`和`final subscript`）。

在子类中任何重写最终方法，属性或下标的企图都会被报编译时错误。在扩展中向类添加的方法，属性或下标也可以在扩展定义中标记为`final`。

你可以在类的定义中，将`final`修饰符写在`class`关键字前面，从而将整个类标记为`final`（`final class`）。任何试图子类化最终类的行为将被报导致编译时错误。

[< 下标](Subscripts.md) || [初始化 >](Initialization.md)
