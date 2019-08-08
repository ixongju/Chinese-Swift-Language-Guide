# 结构体和类

结构体和类是通用的，灵活的结构，它们是程序代码的构建块。用与定义常量，变量和函数一样的语法，定义属性和方法为结构体和类添加功能。

不像其他编程语言，Swift不需要你为结构体和类创建接口和实现分开的文件。在Swift中，在单个文件中定义结构体或类，类或结构体的对外接口自动对外部代码可用。

> 注意：
传统地，一个类的实例本称为对象。然而，Swift的结构体和类与其他语言相比，在功能上更接近实例，且本节大部分内容描述应用于类或结构体实例的功能。因此，将使用更通用的叫法：*实例*。

## 比较结构体和类

Swift中的结构体和类有许多共同点。都能：
* 定义属性存放值
* 定义方法提供功能
* 定义下标提供用下标语法访问它们的值
* 定义初始化方法设置初始化状态
* 可被扩展，增强超出默认实现的功能
* 实现协议以提供某种类型的标准功能

更多信息，参见[属性](Properties.md)，[方法](Methods.md)，[下标](Subscripts.md)，[初始化](Initialization.md)，[扩展](Extensions.md)和[协议](Protocols.md)。

类有结构体没有的功能：
* 继承允许一个类继承另一个类的成员
* 类型转换允许在运行时检查和解释类实例的类型
* 反初始化器允许类实例释放赋值的资源
* 引用计数允许一个类实例有一个以上的引用

更多信息，参见[继承](Inheritance.md)，[类型转换](Type_Casting.md)，[反初始化](Deinitialization.md)和[自动引用计数](Automatic_Reference_Counting.md)。

类支持的额外功能以增加复杂性为代价。作为通用指南，优先使用结构体因为更容易理解，当需要用到类或用类比较合适时，才用类。实践中，意味着用结构体和枚举定义大多数自定义的数据类型。更多详情，参见[结构体与类之间的选择](https://developer.apple.com/documentation/swift/choosing_between_structures_and_classes)。

### 定义语法

结构体和类有着类似的定义语法。用`struct`关键字定义结构体，用`class`关键字定义类。它们都把全部的定义放在一对大括号中：
```swift
struct SomeStructure {
  // structure definition goes here
}
class SomeClass {
  // class definition goes here
}
```

> 注意：
无论何时，定义新结构体或类时，你定义了一个新Swift类型。给类型一个大写开头的驼峰名（如`SomeStructure`和`SomeClass`）以匹配标准Swift类型的大小写（如`String`，`Int`和`Bool`）。用小写字母开头的驼峰命名法命名属性和变量（如`frameRate`和`incrementCount`），以与类型名区分开。

下面是定义结构体和类的例子：
```swift
struct Resolution {
  var width = 0
  var height = 0
}
class VideoMode {
  var resolution = Resolution()
  var interlaced = false
  var frameRate = 0.0
  var name: String?
}
```
上面的例子定义了一个新结构体`Resolution`，用来描述基于像素的分辨率。结构体有两个储存属性`width`和`height`。储存属性是打包并储存作结构体或类的一部分的变量或常量。从用整数`0`初始化可以推断这两个属性类型为`Int`。

上例中也定义了一个新类`VideoMode`，用来描述展示视频的一个特定类型。这个类有四个变量储存属性。第一个，`resolution`，被一个新`Resolution`结构体实例初始化，推断其为`Resolution`类型属性。另外三个属性，新`VideoMode`实例被初始化时，`interlaced`属性被设置成`false`（意为“非隔行扫描视频”），播放帧率设置成`0.0`，和一个可选字符串值`name`。`name`属性自动被赋予一个默认值`nil`，或"没有`name`"，因为是可选类型。

### 结构体和类实例

`Resolution`结构体和`VideoMode`类的定义只描述了`Resolution`或`VideoMode`的外观。他们自身并不描述某一个分辨率或视频模式。要这么做，就需要创建结构体或类的实例。

创建结构体和类实例的方法类似：
```swift
let someResolution = Resolution()
let someVideoMode = VideoMode()
```

结构体和类都用初始化语法创建新实例。初始化语法最简单的格式是，用类或结构体类型名加上空括号，例如`Resolution()`或`VideoMode()`。这创建类或结构体的实例，所有属性都被默认值初始化。类和结构体的初始化在[初始化](Initialization.md)中有更多描述。

### 访问属性

可以用*点语法*访问实例的属性。在点语法中，直接把属性名写在实例名后面，用点号(`.`)隔开，不需要空格：
```swift
print("The width of someResolution is \(someResolution.width)")
// Prints "The width of someResolution is 0"
```

这个例子中，`someResolution.width`引用了`someResolution`的`width`属性，并返回其默认初始化值`0`。

也可以深入到子属性，例如`VideoMode`实例的`resolution`属性的`width`属性：
```swift
print("The width of someVideoMode is \(someVideoMode.resolution.width)")
// Prints "The width of someVideoMode is 0"
```

也可以用点语法赋值给变量属性：
```swift
someVideoMode.resolution.width = 1280
print("The width of someVideoMode is now \(someVideoMode.resolution.width)")
// Prints "The width of someVideoMode is now 1280"
```

### 结构体类型的成员初始化器

所有的结构体都有一个自动生成的*成员初始化器*，可以用该成员初始化器初始化结构体实例的成员属性。通过名称把新实例属性的初始值传给成员初始化器：
```swift
let vga = Resolution(width: 640, height: 480)
```
不像结构体，类实例没有默认的成员初始化器。关于初始化器，在[初始化](Initialization.md)中有描述。

## 结构体和枚举是值类型

值类型的值被赋值给变量或常量，或被传递给函数时，其值会被复制。

实际上，在前面的章节中，你已经广泛使用了值类型。事实上，Swift中所有的基本类型---整形，浮点型，布尔，字符串，数组和字典---都是值类型，底层都是用结构体实现的。

Swift中所有的结构体和枚举都是值类型。这意味着，当在代码中传递你所创建的结构体和枚举实例时，它们以及它们的值类型属性，都将被复制。

> 注意：
> 标准库定义的集合类型，如数组，字典和字符串，优化了复制时消耗的性能。这些集合不是直接复制，而是先在原始实例和备份之间共享内存。如果集合的一个备份被修改，元素在修改之前被复制。你所看到的就像复制立即发生一样。

看看这个例子，用到了上例中的`Resolution`结构体：
```swift
let hd = Resolution(width: 1920, height: 1080)
var cinema = hd
```

这个例子声明一个常量`hd`，设置为`Resolution`实例，该实例被HD视频的宽和高初始化（宽1920像素，高1080像素）。

然后声明一个变量`cinema`，设置当前值为`hd`。因为`Resolution`是结构体，已有实例被复制，并将该复制品复制给`cinema`。现在即使`hd`和`cinema`有相同的宽和高，在底层，它们是两个完全不同的实例。

下一步，`cinema`的`width`属性被修改为略宽的数字影院投影2k标准宽度：
```swift
cinema.width = 2048
```

检查`cinema`的`width`属性，发现它确实变成了`2048`：
```swift
print("cinema is now \(cinema.width) pixels wide")
// Prints "cinema is now 2048 pixels wide"
```

同时，原来的`hd`实例的`width`属性的值仍然是`1920`：
```swift
print("hd is still \(hd.width) pixels wide")
// Prints "hd is still 1920 pixels wide"
```

当`cinema`被设置为`hd`时，`hd`中存放的值被复制到一个新`cinema`实例中。结果，两个完全分开的实例拥有相同的数值。但是，因为它们是独立的实例，设置`cinema`的宽为`2048`并不影响`hd`中存放的宽度，如下图所示：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/sharedStateStruct_2x.png" alt="结构体和枚举是值类型" width="600"/>
</p>

相同的行为也发生在枚举上：
```swift
enum CompassPoint {
    case north, south, east, west
    mutating func turnNorth() {
        self = .north
    }
}
var currentDirection = CompassPoint.west
let rememberedDirection = currentDirection
currentDirection.turnNorth()

print("The current direction is \(currentDirection)")
print("The remembered direction is \(rememberedDirection)")
// Prints "The current direction is north"
// Prints "The remembered direction is west"
```

当`rememberedDirection`被赋值为`currentDirection`时，实际上是赋值了备份。在稍后改变`currentDirection`的值不影响`rememberedDirection`中存放的原始值备份。

## 类是引用类型

与值类型不同的是，*引用类型* 在赋值给变量或常量，或被传递给函数时，其值不会被复制。相反，使用相同实例的引用。

举个例子，使用上面定义的`VideoMode`类：
```swift
let tenEighty = VideoMode()
tenEighty.resolution = hd
tenEighty.interlaced = true
tenEighty.name = "1080!"
tenEighty.frameRate = 25.0
```

这个例子中，声明了一个新常量`tenEighty`，让它引用`VideoMode`类的新实例。视频模式赋值为前面1920*1080分辨率的备份。`interlaced`为`true`，名称为`1080i`，帧率为`25`每秒。

下一步，`tenEighty`被复制给一个新常量，`alsoTenEighty`，帧率被修改了：
```swift
let alsoTenEighty = tenEighty
alsoTenEighty.frameRate = 30.0
```

因为类是引用类型，`tenEighty`和`alsoTenEighty`实际上都引用了同一个`VideoMode`实例。实际上，它们只是同一个实例的两个不同的名字，如下图所示：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/sharedStateClass_2x.png" alt="结构体和枚举是值类型" width="600"/>
</p>

检查`tenEighty`实例的`frameRate`属性发现，其正确反应了底层`VideoMode`实例的新的帧率`30.0`：
```swift
print("The frameRate property of tenEighty is now(tenEighty.frameRate)")
// Prints "The frameRate property of tenEighty is now 30.0"
```

这个例子显示了引用类型如何更难以理解。如果`tenEighty`和`alsoTenEighty`在程序代码的不同部分，很难找出所有视频模式被改变的情况。无论在什么地方用`tenEighty`，你必须考虑到使用了`alsoTenEighty`的代码，反之亦然。相反，值类型比较容易理解，因为与相同的值打交道的代码在同一源文件中紧靠在一起。

注意到`tenEighty`和`alsoTenEighty`被声明为常量，而不是变量。然而，你仍然能够改变`tenEighty.frameRate`和`alsoTenEighty.frameRate`，因为实际上常量`tenEighty`和`alsoTenEighty`本身并不改变。`tenEighty`和`alsoTenEighty`本身并不存放`VideoMode`实例---而是，它们在背后引用了`VideoMode`实例。改变的是底层`VideoMode`的`frameRate`属性，而不是引用`VideoMode`的常量的值。

### 身份运算符

因为类是引用类型，有可能多个常量或变量引用了类的同一个实例。（这对于结构体和枚举是不成立的，因为赋值给常量或变量，或传递给函数是，用的是备份。）

有时候，判断两个常量或变量是否引用同一个类实例比较有用。为了实现这个，Swift提供了两个身份运算符：
* 身份相等（`===`）
* 身份不等（`!==`）

用这些运算符检查两个常量或变量是否指向同一个实例：
```swift
if tenEighty === alsoTenEighty {
  print("tenEighty and alsoTenEighty refer to the same VideoMode instance.")
}
// Prints "tenEighty and alsoTenEighty refer to the same VideoMode instance."
```

注意，身份相等（三个等号，`===`）并不与相等（两个等号，`==`）一样。身份相等意思是两个类类型的常量或变量指向同一个类实例。相等意思是两个实例被认为是相等或值相等，或如类型设计者定义的其他意义上的相等。

当你定义自定义结构体和类时，你有责任决定两个实例相等时需要什么条件。操作符`==`和`!=`的自定义实现在[等价运算符](Advanced_Operators.md#等价运算符)中有描述。

## 指针

如果你用过C，C++或Objective-C，你也许知道这些语言用指针引用内存地址。Swift中引用某些引用类型实例的常量或变量，与C中的指针类似，但不是直接指向内存地址的指针，也不需要你书写星号(`*`)来表明要创建引用。相反，像Swift中其他常量或变量样定义这些引用。如果你需要直接跟指针打交道，标准库提供了指针和缓冲类型。参见[Manual Memory Management](https://developer.apple.com/documentation/swift/swift_standard_library/manual_memory_management)。

[< 枚举](Enumeration.md) || [属性 >](Properties.md)
