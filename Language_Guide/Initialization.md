# 初始化

初始化是准备使用类、结构体或枚举实例的过程。这个过程涉及为该实例的每个储存属性赋值，和准备使用实例之前需要执行的其他设置或初始化。

通过定义初始化器实现此初始化过程，初始化器类似于调用以创建某一类型新实例的方法。不同于Objective-C的初始化器，Swift的初始化器不返回值。主要功能就是确保新实例在第一次被使用前被正确地初始化。

类类型的实例也可以实现反初始化方法，翻初始化方法在该类实例被回收前执行一些自定义清理。更多关于反初始化方法，参见[反初始化](Deinitialization.md)。

## 为储存属性设置初始值

当类和结构体的实例被创建时，***必须*** 为所有的储存属性设置合适的初始值。储存属性不能被放置在不确定状态。

可以在初始化方法中为储存属性设置初始值，或把指定默认属性值作为属性定义的一部分。这些操作在下面的章节中有描述。

> 注意：
当给储存属性赋默认值时，或在初始化方法中设置初始值时，属性值会被直接设置，不会调用属性观察者。

### 初始化方法

调用初始化方法来创建某一类型的实例。最简单的，初始化方法像没有参数的实例方法，用`init`关键字书写：
```swift
init() {
  // perform some initialization here
}
```

下面的例子定义一个新结构体`Fahrenheit`存放华氏温标表示的温度。`Fahrenheit`结构体有一个储存属性`temperature`，是`Double`类型：
```swift
struct Fahrenheit {
    var temperature: Double
    init() {
      temperature = 32.0
    }
}
var f = Fahrenheit()
print("The default temperature is \(f.temperature)° Fahrenheit")
// Prints "The default temperature is 32.0° Fahrenheit"
```

结构体定义了一个初始化方法`init`，无参数，该初始化方法把储存的温度初始化为`32.0`（水的冰点的华氏温度）。

### 默认属性值

如上，你可以在初始化方法中设置储存属性的初始值。另外，也可以把指定*默认属性值*作为属性声明的一部分。通过在定义属性时为其指定初始值，来指定默认属性值。

> 注意：
如果属性总是接受相同的初始值，请提供默认值而不是在初始化方法中初始化。结果是一样，但是，属性初始化的默认值与声明更近。这有助于实现简洁、明了的初始化方法，且能让你从默认值推导属性类型。后面章节会讲到，默认值也能让使用默认初始化方法和初始化方法的继承变得容易。

可以通过在声明`temperature`属性时为其提供默认值，为上面的`Fahrenheit`结构体写一个更简单的版本：
```swift
struct Fahrenheit {
    var temperature = 32.0
}
```

## 自定义初始化

可以用输入参数和可选属性类型自定义初始化过程，或在初始化过程中给常量属性赋值。下面的章节有描述。

### 初始化参数

可以提供*初始化参数*作为初始化定义的一部分，用来定义自定义初始化用到的值的类型和名称。初始化参数与函数和方法有着相同的功能和语法。

下面的例子定义了一个结构体`Celsius`，它存放摄氏度表示的温度。`Celsius`结构体实现了两个自定义初始化方法`init(fromFahrenheit:)`和`init(fromKelvin:)`，它们用不同的温标初始化新的结构体实例：
```swift
struct Celsius {
    var temperatureInCelsius: Double
    init(fromFahrenheit fahrenheit: Double) {
      temperatureInCelsius = (fahrenheit - 32.0) / 1.8
    }
    init(fromKelvin kelvin: Double) {
      temperatureInCelsius = kelvin - 273.15
    }
}
let boilingPointOfWater = Celsius(fromFahrenheit: 212.0)
// boilingPointOfWater.temperatureInCelsius is 100.0
let freezingPointOfWater = Celsius(fromKelvin: 273.15)
// freezingPointOfWater.temperatureInCelsius is 0.0
```

第一个初始化方法有一个初始化参数，该参数有一个实参标签`fromFahrenheit`和一个形参名称`fahrenheit`。第二个初始化方法有一个初始化参数，该参数有一个实参标签`fromKelvin`和一个形参名称`kelvin`。两个初始化方法都把仅有的参数转换为对应的摄氏温度值，然后存放在`temperatureInCelsius`属性中。

### 形参名称和实参标签

如函数和方法参数样，初始化参数可以有在初始化方法体中使用的形参名称，和调用初始化方法时使用的实参标签。

但是，初始化方法在其括号之前没有像函数和方法一样的标识函数名称。因此，初始化参数的参数名和类型在决定调用哪一个初始化方法时扮演一个非常重要的角色。所以，如果你没有提供实参标签，Swift会自动为每个参数提供实参标签。

下面的例子定义了一个结构体`Color`，该结构体有三个常量属性`red`，`green`和`blue`。这些属性存放介于`0.0`和`1.0`之间的值，代表颜色中的红，绿和蓝。

`Color`提供了一个有与红，绿和蓝三原色对应名称的参数的初始化方法。`Color`也提供了第二个初始化方法，该方法只有一个`white`参数，用该初始化方法为三原色提供相同的值。
```swift
struct Color {
    let red, green, blue: Double
    init(red: Double, green: Double, blue: Double) {
        self.red = red
        self.green = green
        self.blue = blue
    }
    init(white: Double) {
        red = white
        green = white
        blue = white
    }
}
```

通过提供命名的值给初始化参数，两个初始化方法都可以用来创建`Color`实例：
```swift
let magenta = Color(red: 1.0, green: 0.0, blue: 1.0)
let halfGray = Color(white: 0.5)
```

注意，不用实参标签是不可能调用这些初始化方法的。如果定义了就必须在初始化方法中使用实参标签，省略它们会导致编译时错误：
```swift
let veryGreen = Color(0.0, 1.0, 0.0)
// this reports a compile-time error - argument labels are required
```

### 无实参标签的初始化参数

如果不希望为初始化参数使用实参标签，作为显式实参标签的替换，写上下划线(`_`)来重写默认行为。

下面是上例[初始化参数](#初始化参数)中`Celsius`的扩展版，增加了额外的初始化方法，该方法用摄氏温标的`Double`值创建`Celsius`实例：
```swift
struct Celsius {
    var temperatureInCelsius: Double
    init(fromFahrenheit fahrenheit: Double) {
        temperatureInCelsius = (fahrenheit - 32.0) / 1.8
    }
    init(fromKelvin kelvin: Double) {
        temperatureInCelsius = kelvin - 273.15
    }
    init(_ celsius: Double) {
      temperatureInCelsius = celsius
    }
}
let bodyTemperature = Celsius(37.0)
// bodyTemperature.temperatureInCelsius is 37.0
```

初始化方法`Celsius(37.0)`即使没有实参标签其意图也显而易见。因此写成`init(_ celsius: Double)`也合适，并且可以用命名的`Double`值调用。

### 可选的属性类型

如果自定义类型有一个在逻辑上可以为“无值”的储存属性——或许是因为在初始化时不能设置值，或在后面某一时刻允许为无值状态——将该属性声明为*可选类型*。可选类型的属性自动被初始化为`nil`，特地表初始化时属性会是无值状态。

下面的例子定义了一个类`SurveyQuestion`，该类有一个可选字符串属性`response`:
```swift
class SurveyQuestion {
  var text: String
  var response: String?
  init(text: String) {
    self.text = text
  }
  func ask() {
    print(text)
  }
}
let cheeseQuestion = SurveyQuestion(text: "Do you like cheese?")。
cheeseQuestion.ask()
// Prints "Do you like cheese?"
cheeseQuestion.response = "Yes, I do like cheese."
```

在询问之前问卷的回不是不可能知道的，所以`response`属性被声明为可选字符串类型`String?`。该属性自动被指定默认值`nil`，意思是当新`SurveyQuestion`实例被初始化时，“还没有字符串”。

### 初始化期间赋值常量属性

你可以在初始化期间任何时刻给常量属性赋值，只要在初始化结束之前赋值即可。一旦常量属性被赋值，就不能再修改了。

> 注意：
对于类实例，一个常量属性，只能通过引入它的类在初始化期间对其进行修改。它不能被子类修改。

可以修改上例中的`SurveyQuestion`，为了表明一旦`SurveyQuestion`实例被创建问题就不会改变，可以用一个常量属性而不是变量属性来表示`text`。即使`text`属性现在是常量，仍然可以在类的初始化方法中设置：
```swift
class SurveyQuestion {
  let text: String
  var response: String?
  init(text: String) {
    self.text = text
  }
  func ask() {
    print(text)
  }
}
let beetsQuestion = SurveyQuestion(text: "How about beets?")
beetsQuestion.ask()
// Prints "How about beets?"
beetsQuestion.response = "I also like beets. (But not with cheese.)"
```

## 默认初始化方法

Swift为任何为自身属性提供默认值，且没有任何初始化方法的结构体或类提供了一个默认初始化方法。默认初始化器只是创建一个属性都被设置为默认值的新实例。

下面的例子定义个一个类`ShoppingListItem`，封装了购物清单中一个项的名称，数量和购买状态：
```swift
class ShoppingListItem {
  var name: String?
  var quantity = 1
  var purchased = false
}
var item = ShoppingListItem()
```

因为类`ShoppingListItem`的所有属性都有默认值，而且是没有父类的基本类，`ShoppingListItem`自动拥有类一个将所有属性都设置为默认值的默认初始化方法。（`name`是一个可选`String`属性，所以自动接受默认值`nil`，即使代码中没有写`nil`。）上面的例子，用`ShoppingListItem`类的默认初始化方法与初始化方法语法创建了一个新实例，写作`ShoppingListItem()`，然后将新实例赋值给变量`item`。

### 结构体类型的成员初始化方法

结构体类型如果没有自定义初始化方法，则自动拥有一个*成员初始化方法*。与默认初始化方法不同的是，即使结构体有没有初始值的储存属性，结构体仍然拥有成员初始化方法。

成员初始化方法是初始化新结构体实例成员属性的简便方法。新实例属性的初始化值可以按照名称传给成员初始化方法。

下面的例子定义一个结构体`Size`，拥有两个属性`width`和`height`。通过赋默认值`0.0`，两个属性都被推导为`Double`类型。

`Size`结构体自动拥有一个成员初始化方法`init(width:height:)`，你可以用它初始化一个新`Size`实例：
```swift
struct Size {
  var width = 0.0, height = 0.0
}
let twoByTwo = Size(width: 2.0, height: 2.0)
```

当你调用一个成员初始化器，你可以省略任何有初始值的属性的值。在上面的例子中，结构体`Size`的`width`和`height`属性都有默认值。你可以省略其中一个或全部省略，初始化方法会为给省略的部分赋默认值：
```swift
let zeroByTwo = Size(height: 2.0)
print(zeroByTwo.width, zeroByTwo.height)
// Prints "0.0 2.0"

let zeroByZero = Size()
print(zeroByZero.width, zeroByZero.height)
// Prints "0.0 0.0"
```

## 值类型初始化方法委托

初始化方法可以调用其他初始化方法以执行实例的部分初始化。这个过程被称为*初始化方法委托*，可以避免多个初始化方法间出现重复代码。

初始化方法委托如何工作，什么格式的初始化方法被允许，对于值类型和类类型是不同的。值类型（结构体和枚举）不支持继承，所以它们的初始化方法委托过程比较简单，因为它们只能委托自身定义的另一个初始化方法。而类，可以继承另一个类，如[继承](Inheritance.md)中所述。这意味着，类有额外的责任确保所有继承而来的储存属性，在初始化期间都被合适的值赋值。这些职责在下面的[类的继承和初始化](#类的继承和初始化)中有描述。

对于值类型，当编写自定义初始化方法是，用`self.init`指代相同类型的值的其他初始化方法。你可以在初始化方法内调用`self.init`。

注意，如果你为值类型定义了一个自定义初始化方法，你在再无权访问该类型的默认初始化方法了（或成员初始化方法，如果是结构体的话）。这个限制能阻止，一个使用自动初始化方法的用户，意外地绕开了一个更复杂的需要额外必要的设置的初始化方法，的情况发生。（追加说明：绕开了有额外初始化的初始化方法，则该额外初始化部分没有被执行，实例没有被完全初始化斯密达）

> 注意：
如果你希望你的自定义值类型可以用默认初始化方法和成员初始化方法初始化，且可以用你自定义的初始化方法初始化，将你的自定义初始化方法写在一个扩展中，而不是该值类型的原始实现中。更多请参见[扩展](Extensions.md)。

下面的例子定义了一个结构体`Rect`来表示一个几何矩形。例中也定义了两个辅助结构体`Size`和`Point`，这两个结构体都为它们的属性提供了默认值`0.0`：
```swift
struct Size {
    var width = 0.0, height = 0.0
}
struct Point {
    var x = 0.0, y = 0.0
}
```

你可以用三种方式初始化下面的`Rect`结构体——通过用其默认被零初始化的`origin`和`size`属性值初始化，通过提供一个指定的原点和大小初始化或通过提供一个中心点和一个大小。用`Rect`结构体定义中的三个自定义初始化方法代表这些初始化选项：
```swift
struct Rect {
    var origin = Point()
    var size = Size()
    init() {}
    init(origin: Point, size: Size) {
      self.origin = origin
      self.size = size
    }
    init(center: Point, size: Size) {
      let originX = center.x - (size.width / 2)
      let originY = center.y - (size.height / 2)
      self.init(origin: Point(x: originX, y: originY), size: size)
    }
}
```

第一个`Rect`初始化方法`init()`，功能上与结构体没有自定义初始化方法时自动拥有的的默认初始化方法相同。该初始化方法有一个空方法体，用空括号(`()`)代替。调用这个初始化方法会返回一个`origin`和`size`属性都被定义中的默认值`Point(x: 0.0, y: 0.0)`和`Size(width: 0.0, height: 0.0)`初始化的实例：
```swift
let basicRect = Rect()
// basicRect's origin is (0.0, 0.0) and its size is (0.0, 0.0)
```

第二个`Rect`初始化方法`init(center:size:)`，略显复杂。一开始依据`center`和`size`的值计算适当的原点。然后调用（或*委托*）`init(origin:size:)`初始化方法，该初始化方法将新的`origin`和`size`存入适当的属性中：
```swift
let centerRect = Rect(center: Point(x: 4.0, y: 4.0),
                      size: Size(width: 3.0, height: 3.0))
// centerRect's origin is (2.5, 2.5) and its size is (3.0, 3.0)
```

初始化方法`init(center:size:)`本可以为相应的属性指定新的值`origin`和`size`。但是，`init(center:size:)`方法中直接使用提供了该功能的现有初始化方法显得更方便（意图更清晰）。

> 注意：
在不定义`init()`和`init(origin:size:)`的情况下编写另一个初始化方法，参见[扩展](Extensions.md)。

（译者：如果将初始化方法`init(center:size:)`定义在扩展中，并删除`init()`和`init(origin:size:)`两个初始化方法，则由于`Rect`没有自定义初始化方法，会自动拥有`init()`(默认初始化方法)和`init(origin:size:)`(成员初始化方法）这两个初始化方法。定义会更简洁。）

## 类的继承和初始化

类的所有储存属性——包括从父类继承而来的属性——***必须*** 在初始化期间指定初始值。

Swift为类类型定义了两种初始化方法，帮助确保所有的储存属性都能获得初始值。它们被称为指定初始化方法和便捷初始化方法。

### 指定初始化方法和便捷初始化方法

*指定初始化方法* 是类的基本初始化方法。一个指定初始化方法完全地初始化了该类所有属性，并调用相应的父类初始化方法将初始化过程延伸到父类链。

类应该只有很少的指定初始化方法，比较常见的是一个类只有一个。指定初始化方法是初始化时“必须经过的点”，并且通过指定初始化方法，初始化过程继续向上至父类链。

每个类都必须至少有一个指定初始化方法。在某些情况下，通过从父类继承一个或多个指定初始化方法来满足这个要求，如下面[自动继承初始化方法](#自动继承初始化方法)中所述。

*便捷初始化方法* 是次要的，为一个类的初始化提供支持。你可以定义一个便捷初始化方法，来调用同一个类型中的指定初始化方法，作为指定初始化方法中的某些参数被设置为默认值的便捷初始化方法。你也可以定义一个便捷初始化方法，来为特定情况或特殊输入值创建该类的实例。

如果你的类不需要便捷初始化方法，你可以不必提供。任何时候，如果使用常用初始化模式的简写，能节省时间或能使该类的初始化意图更明显，则请（用该简写）创建便捷初始化方法。

### 指定初始化方法和便捷初始化方法的语法

类的指定初始化方法的编写方法与值类型的简单初始化方法相同：
```swift
init(parameters) {
  statements
}
```

便捷初始化方法用相同的格式编写，但是在`init`关键字前面要写上`convenience`修饰符，中间用空格隔开：
```swift
convenience init(parameters) {
  statements
}
```

### 类类型的初始化委托

为了简单化指定初始化方法和便捷初始化方法之间的关系，Swift对初始化方法间的委托调用约法三章：

##### **规则一**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一个指定初始化方法必须调用其直属父类的指定初始化方法。

##### **规则二**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一个便捷初始化方法必须调用*相同*类中另一个初始化方法。

##### **规则三**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一个便捷初始化方法必须最终调用一个指定初始化方法。

简单地记为：
* 指定初始化方法必须总是向上委托
* 便捷初始化方法必须总是交叉委托（译者：最终调用了指定初始化方法）

下面的图表说明了这些规则：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/initializerDelegation01_2x.png" alt="类类型的初始化委托" width="500"/>
</p>

这里，父类有一个指定初始化方法和两个便捷初始化方法。一个便捷初始化方法调用了另一个调用了指定初始化方法的便捷初始化方法。这满足了上面的规则二和规则三。父类本身没有父类，所以规则一不实用。

图中的子类有两个指定初始化方法和一个便捷初始化方法。便捷初始化方法必须调用两个指定初始化方法中的一个，因为便捷初始化方法只能调用同一个类中的两一个初始化方法。这满足上面的规则二和规则三。两个指定初始化方法都调用了父类唯一的指定初始化方法，从而满足了上面的规则一。

> 注意：
这些规则不影响你的类的用户如何创建每个类的实例。上面表中的任何初始化方法，都可被用于完全初始化其所属类的实例，这些规则只会影响你如何实现类的初始化方法。

下面的图标展示了一个更复杂的四个类的类结构。该图说明了在结构图中，指定初始化方法如何扮演类初始化的“必须经过的点”，简化了继承链中类之间的关系：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/initializerDelegation02_2x.png" alt="类类型的初始化委托" width="500"/>
</p>

## 两步初始化

Swift中类的初始化是一个两步过程。第一步中，每个储存属性被类赋上初始值。一旦每个储存属性的初始化状态被决定，第二步就开始了，在准备使用新实例之前，每个类被给予进一步自定义其储存属性的机会。

使用两步初始化使初始化变得安全，同时给继承结构中的每个类足够的灵活性。两步初始化防止属性值在初始化之前被访问，也防止了属性值意外被另一个初始化方法设置成不同的值。

> 注意：
Swift的两步初始化类似于Objective-C中的初始化。主要不同在于，在第一步，Objective-C为每个属性赋空值（如`0`或`nil`）。Swift的初始化流更灵活，它让你设置自定义初始值，且可以应对`0`或`nil`是无效默认值的类型。

Swift编译器执行四个有用的安全检查，以确保两步初始化完成且无错误：

##### **安全检查一**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一个指定初始化方法必须确保，在其向上委托父类初始化方法之前，累的所有属性都被初始化。

如上面提到的，一个对象的内存，只有在其所有储存属性的初始化状态可知的时候，才会被认为已经完全初始化。为了满足这个规则，一个指定初始化方法必须确保在向上传递（初始化）链之前自身所有属性被初始化。

##### **安全检查二**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一个指定初始化方法在为继承而来的属性赋值之前，必须向上委托一个父类初始化方法。如果不这么做，则作为自身初始化的一部分，指定初始化方法赋的值会被父类覆盖（重写）。

##### **安全检查三**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一个便捷初始化方法在给任何属性（包括同一个类定义的属性）赋值之前，必须委托另一个初始化方法。如果不这么做，则便捷初始化方法赋的值会被类自身的指定初始化方法覆盖（重写）。

##### **安全检查四**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;第一步初始化完成之前，初始化方法不能调用任何实例方法，不能读取任何实例属性的值，或引用`self`。

第一步初始化结束之前，类的实例不完全有效。只能访问属性，只有第一步结束，知道类实例有效时，才能访问属性和调用方法。

基于上面四个安全检查，下面展示如何进行两步初始化：

##### **第一步**
* 



### 自动继承初始化方法


### 结构类型的成员初始化方法












[< 继承](Inheritance.md) || [反初始化 >](Deinitialization.md)
