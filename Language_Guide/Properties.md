# 属性

属性将值与特定类，结构体或枚举关联起来。作为实例的一部分，存储属性存放常量或变量值，计算属性计算（而不是存放）一个值。计算属性有类，结构体和枚举提供。存储属性只由类和结构体提供。

存储属性和计算属性经常与某一类型的实例联系起来。但是，属性也可以与类型本身联系起来。这些类型称为类型属性。

另外，可以定义属性观察者监视属性值的变化，针对变化执行自定义行为。属性观察者可以添加到存储属性中，也可以添加到从父类继承而来的子类属性中。

## 存储属性

简单的说，存储属性是一个被当作某个类或结构体实例的一部分而存储的常量或变量。存储属性可以是*变量存储属性*（用`var`关键字声明）或*常量存储属性*（用`let`关键字声明）。

如[属性默认值](Initialization.md#属性默认值)所述，作为定义的一部分，你可以为存储属性提供默认值。也可以在初始化过程中设置或修改存储属性的原始值。即使是常量存储属性也可以，如[初始化过程中给常量属性赋值](Initialization.md#初始化过程中给常量属性赋值)中所述。

下面的例子定义了结构体`FixedLengthRange`，描述了一个整数范围，该范围长度在创建后不可改变：
```swift
struct FixedLengthRange {
  var firstValue: Int
  let length: Int
}
var rangeOfThreeItems = FixedLengthRange(firstValue: 0, length: 3)
// the range represents integer values 0, 1, and 2
rangeOfThreeItems.firstValue = 6
// the range now represents integer values 6, 7, and 8
```

`FixedLengthRange`的实例有一个可变的存储属性`firstValue`和一个常量存储属性`length`。上例中，`length`在创建新实例的时候初始化，之后不可被改变，因为是常量属性。

### 常量结构体实例的存储属性

如果创建一个结构体实例，然后将该实例赋值给一个常量，你将不能修改该实例的属性，即使该属性被声明为变量属性：
```swift
let rangeOfFourItems = FixedLengthRange(firstValue: 0, length: 4)
// this range represents integer values 0, 1, 2, and 3
rangeOfFourItems.firstValue = 6
// this will report an error, even though firstValue is a variable property
```

因为`rangeOfFourItems`被声明为常量（用了`let`关键字），即便`firstValue`是变量属性，也不可能改变。

这个行为基于结构体是值类型。当一个值类型的实例被标记为常量时，其所有属性也都将是常量。

类则不然，类是引用属性。如果把引用类型实例赋值给常量，你仍然能改变其变量属性。

### 懒存储属性

懒存储属性在被使用之前，不会计算其初始值。在声明前用`lazy`修饰符表明属性是懒储存属性。

> 注意：
> 必须把懒存储属性定义为变量（用`var`关键字），因为在实例初始化完成之前，其初始值可能没有被获取。常量属性在实例初始化之前必须有值，所以常量属性不能被声明为懒属性。

当属性的初始值依赖于外部因素，而实例初始化完成之前，该外部值未知的情况下，懒属性就比较有用了。当属性的初始值需要复杂或耗性能地计算，且在需要用到之前不必执行的情况下，懒属性也比较有用。

下面的例子，用懒存储属性避免了一个复杂类的不必要的初始化。例中，定义了两个类`DataImporter`和`DataManager`，两者都不全显示：
```swift
class DataImporter {
  /*
  DataImporter is a class to import data from an external file.
  The class is assumed to take a nontrivial amout of time to initialize.
  */
  var filename = "data.txt"
  // the DataImporter class would provide data importing functionality here
}

class DataManager {
  lazy var importer = DataImporter()
  var data = [String]()
  // the DataManager class would provide data management functionality here
}

let manager = DataManager()
manager.data.append("Some data")
manager.data.append("Some more data")
// the DataImporter instance for the importer property has not yet been created
```

`DataManager`类有一个存储属性`data`，被初始化为空字符串数组。即使剩下的功能没有全显示，`DataManager`类的目的是管理与提供对这个字符串数组的访问。

`DataManager`类的部分功能是从某个文件导入数据。这个功能被`DataImporter`类提供，`DataImporter`实例需要话费长时间来初始化。这可能是由于`DataImporter`实例需要打开文件，在`DataImporter`实例初始化时，将文件内容读取到内存中。

`DataManager`实例无需从文件导入数据也能管理数据，所以当`DataManager`被创建时，无需创建一个新的`DataImporter`实例。相反，当需要用到的时候，才创建`DataImporter`实例。

因为标记了`lazy`修饰符，`importer`属性的`DataImporter`实例仅在`importer`属性第一次被访问的时候才会创建，例如，请求`filename`属性的时候：
```swift
print(manager.importer.filename)
// the DataImporter instance for the importer property has now been created
// Print "data.txt"
```

> 注意：
> 如果被`lazy`修饰符标记的属性，在没有被初始化的情况下，同时被多个线程访问，无法保证属性只被初始化一次。

### 储存属性和实例变量

如果使用过Objective-C，你或许知道Objective-C提供两种方式存放值和引用作为类实例的一部分。除了属性，还可以用实例变量作为被储存属性的后备储存。

Swift将这些概念结合到单个属性声明中。Swift属性没有对应的实例变量，且不直接访问属性的后备储存。这个操作避免了在不同上下文环境中访问值所造成的困扰，并把属性声明简化为单一，明确的语句。关于属性的所有信息---包括名称，类型和内存管理特性---都在同一个地方作为类型定义的一部分。

## 计算属性

除了储存属性，类，结构体和枚举还可以定义*计算属性*，计算属性不存放值。相反，它们提供getter方法和一个可选setter方法，间接获取或设置其余的属性和值。
```swift
struct Point {
  var x = 0.0, y = 0.0
}
struct Size {
  var width = 0.0, height = 0.0
}
struct Rect {
  var origin = Point()
  var size = Size()
  var center: Point {
    get {
      let centerX = origin.x + (size.width / 2)
      let centerY = origin.y + (size.height / 2)
      return Point(x: centerX, y: centerY)
    }
    set(newCenter) {
      origin.x = newCenter.x - (size.width / 2)
      origin.y = newCenter.y - (size.height / 2)
    }
  }
}
var square = Rect(origin: Point(x: 0.0, y:0.0),
                  size: Size(width: 10.0, height: 10.0))
let initialSquareCenter = square.center
square.center = Point(x: 15.0, y: 15.0)
print("square.origin is now at (\(square.origin.x), \(square.origin.y))")
// Prints "square.origin is now at (10.0, 10.0)"
```

这个例子定义了三个几何图形的结构体：
* `Point`封装一个点的x坐标和y坐标
* `Size`封装宽`width`和高`height`
* `Rect`用原点和尺寸定义一个矩形

结构体`Rect`提供了计算属性`center`。一个矩形的当前中心位置总能由其原点`origin`和尺寸`size`决定，且不需要将中心点明确地储存为一个`Point`值。相反，为了能够像储存属性一样使用使用`center`，`Rect`为计算变量`center`定义了自定义getter和setter方法。

上面的例子创建了一个新`Rect`变量`square`。`square`变量用原点(`0, 0`)，宽和高都为`10`初始化。下图中，用来色方块来表示矩形`square`。

`square`变量的`center`属性可以被点句法访问(`square.center`)，这会导致`center`的getter方法被调用，来获取当前属性值。不是返回一个已存在的值，getter方法实际上计算并返回一个新`Point`值，以此代表矩形的中心。从上面可以看出，getter方法成功地返回了中心点(`5, 5`)。

然后给`center`属性设置新值(`15, 15`)，这将方块向右上方移动，到图中橙色方块的位置。调用`center`的setter方法设置`center`属性，setter方法会修改储存属性`origin`的`x`和`y`值，把方块移动到新位置。

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/computedProperties_2x.png" alt="计算属性" width="300"/>
</p>

### 简写setter方法定义

如果getter方法的方法体仅是一句表达式，getter方法隐式返回该表达式。下面是另一个版本的`Rect`结构体，利用了这种简写方法：
```swift
struct CompactRect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            Point(x: origin.x + (size.width / 2),
                  y: origin.y + (size.height / 2))
        }
        set {
            origin.x = newValue.x - (size.width / 2)
            origin.y = newValue.y - (size.height / 2)
        }
    }
}
```

省略getter方法`return`关键字的规则与省略函数`return`关键字的规则相同，如[隐式返回的函数](Functions.md#隐式返回的函数)中所述。

### 只读计算属性

只有getter方法没有setter方法的计算属性被称为*只读计算属性*。只读计算属性总有返回值，且可以用点号句法访问，但是不能设置不同的值。

> 注意：
> 必须用`var`关键字将计算属性---包括只读计算属性---声明为变量属性，因为它们的值并不固定。`let`关键字只用于常量属性，表明一旦作为实例初始化的一部分被赋值，将不能再被改变。

可以省略`get`关键字及其括号以简化只读计算属性的声明：
```swift
struct Cuboid {
  var width = 0.0, height = 0.0, depth = 0.0
  var volume: Double {
    return width * height * depth
  }
}
let fourByFiveByTwo = Cuboid(width: 4.0, height: 5.0, depth: 2.0)
print("the volume of fourByFiveByTwo is \(fourByFiveByTwo.volume)")
// Prints "the volume of fourByFiveByTwo is 40.0"
```

这个例子定义一个新结构体`Cuboid`，代表一个3D矩形盒子的宽`width`，高`height`和深度`depth`。结构体也有一个只读计算属性`volume`，它计算长方体的体积。可写对于`volume`并无意义，因为对于特定的`volume`值，对应的宽`width`，高`height`和深度`depth`的值是不确定的。尽管如此，一个`Cuboid`提供只读计算属性让使用者能找到计算后的体积，显得很有用。

## 属性观察者

属性观察者观察并响应属性值的变化。每次设置属性值时，即便新值与当前值相同，也会调用属性观察者。

可以给除了懒储存属性之外的任何储存属性添加属性观察者。也可以跟子类重写了的继承而来的属性添加属性观察者。不必给非重写计算属性定义属性观察者，因为可以在其setter中监视并响应值的变化。属性的重写在[重写](Inheritance.md#重写)中有描述。

可以在属性上定义下面之一或全部观察者：
* `willSet`在值被储存之前调用
* `didSet`在值被储存之后立即调用


















[< 结构体和类](Structures_and_Classes.md) || [方法 >](Methods.md)
