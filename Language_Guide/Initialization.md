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

##### **第一阶段**
* 类的一个指定初始化方法或便捷初始化方法被调用
* 存放该类新实例的内存被分配。但是内存还未被初始化
* 该类的指定初始化方法确认其所有储存属性是否有值。存放储存属性的内存被初始化
* 指定初始化方法将操作交给父类初始化方法，父类为自身储存属性执行相同操作
* 这种操作持续到类继承链顶端
* 一旦到达继承链顶端，继承链的最后一个类确保了其所有储存属性都有值，则该实例的内存被认为已经完全初始化，第一阶段结束

##### **第一阶段**
* 从继承链的顶端向下，继承链中的每个初始化方法可以选择进一步自定义实例。从现在起，在初始化方法中可以访问`self`，并且可以修改属性，可以调用实例方法，等等。
* 最后，继承链中的任何便捷初始化方法，都可以自定义实例，且可以使用`self`。

下面是展示假想的父类和子类第一阶段初始化的图表：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/twoPhaseInitialization01_2x.png" alt="两步初始化" width="500"/>
</p>

在这个例子中，初始化始于调用子类的一个便捷初始化方法。这个便捷初始化方法还不能修改任何属性。它交叉委托了一个来自同一个类的指定初始化方法。

根据安全检查一，指定初始化方法确保子类所有的属性都有一个值。然后调用父类的指定初始化方法将初始化延伸到继承链上。

父类指定初始化方法确保父类的所有属性都有一个值。父类没有父类需要初始化，所以不需要进一步委托。

一旦父类所有属性都有初始值，就认为其内存已经完全初始化，第一阶段完成。

下面是展示四二姐的初始化的图表：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/twoPhaseInitialization02_2x.png" alt="两步初始化" width="500"/>
</p>

父类指定初始化方法现在有一个机会进一步自定义实例（即使这不是必须的）。

一旦父类指定初始化方法执行完毕，子类的指定初始化方法可以指定额外的自定义（即使这也不是必须的）。

最后，一旦子类的指定初始化方法方法执行完毕，最初调用的便捷初始化方法可以执行额外的初始化。

### 初始化的继承和重写

不像Objective-C中的子类，Swift的子类默认不继承父类的初始化方法。Swift的这个操作防止发生这种情况：父类中一个简单的初始化方法被一个更具体的子类继承，然后用该初始化方法创建并没有完全初始化或正确初始化的子类实例。

> 注意：
> 在某些环境下，父类初始化方法会被继承，但是只在安全和合适的时候才会这么做。更多信息，参见下面的[自动继承初始化方法](#自动继承初始化方法)。

如果你希望自定义子类呈现一个或多个与父类相同的初始化方法，你可以给子类提供这些初始化方法的自定义实现。

当你编写匹配父类的*指定初始化方法*的子类初始化方法时，你正在有效地提供该指定初始化方法的重写版本。因此，你必须在子类初始化方法的定义前写上`override`修饰符。如果你打算重写自动提供的默认初始化方法，也得这么做，如[默认初始化方法](#默认初始化方法)中所述。

与被重写的属性，方法或下标一样，写上`override`修饰符提示Swift检查父类有一个匹配的指定初始化方法需要重写，并且验证被重写初始化方法的参数是否按照预期指定。

> 注意：
> 当重写父类的指定初始化方法时，即使初始化方法的子类实现是便捷初始化方法，也要写上`override`修饰符。

相反，如果编写匹配父类便捷初始化方法的子类初始化方法，该父类的便捷初始化方法不能被子类直接调用，如果上面[类类型的初始化委托](#类类型的初始化委托)中规则所描述。因此，（严格地说）子类并没有重写父类初始化方法。结果，当（在子类）提供匹配父类便捷初始化方法的实现时，不需要写上`override`修饰符。

下面的例子定义了一个基础类`Vehicle`。这个基础类声明了一个储存属性`numberOfWheels`，默认值是`Int`值`0`。`numberOfWheels`属性被一个计算属性`description`用来创建车辆属性的`String`描述：
```swift
class Vehicle {
  var numberOfWheels = 0
  var description: String {
    return "\(numberOfWheels) wheel(s)"
  }
}
```

`Vehicle`类为唯一的储存属性提供了一个默认值，且没有提供任何自定义初始化方法。结果，它自动拥有的默认初始化方法，如[默认初始化方法](#默认初始化方法)中所述。对于类，默认初始化方法（当可用时）就是指定初始化方法，并且可被用于创建一个`numberOfWheels`为`0`的新`Vehicle`实例:
```swift
let vehicle = Vehicle()
print("Vehicle: \(vehicle.description)")
// Vehicle: 0 wheel(s)
```

下一个例子定义了`Vehicle`的子类`Bicycle`：
```swift
class Bicycle: Vehicle {
  override init() {
    super.init()
    numberOfWheels = 2
  }
}
```

`Bicycle`子类定义了一个自定义指定初始化方法`init()`。这个指定初始化方法匹配`Bicycle`的父类的一个指定初始化方法，所以`Bicycle`版本的这个初始化方法被`override`修饰符标记。

`Bicycle`类的初始化方法`init()`一开始调用`super.init()`，该方法调用`Bicycle`父类`Vehicle`的默认初始化方法。这确保在`Bicycle`有机会修改继承属性`numberOfWheels`之前已经被`Vehicle`初始化。调用`super.init()`之后，`numberOfWheels`的原始值被替换为新值`2`。

如果创建一个`Bicycle`实例，你可以调用其计算属性`description`看看`numberOfWheels`属性如何被改变：
```swift
let bicycle = Bicycle()
print("Bicycle: \(bicycle.description)")
// Bicycle: 2 wheel(s)
```

如果子类初始化方法在初始化过程的第二步没有执行自定义操作，且父类有一个无参数的指定初始化方法，则在为子类所有储存属性赋值后，你可以省略调用`super.init()`。

这个例子定义了另外一个`Vehicle`的子类`Hoverboard`。在其初始化方法中，`Hoverboard`类只设置其`color`属性。初始化方法依赖隐式调用父类初始化方法来完成初始化过程，而不是显示调用`super.init()`。
```swift
class Hoverboard: Vehicle {
  var color: String
  init(color: String) {
    self.color = color
    // super.init() implicitly called here
  }
  override var description: String {
    return "\(super.description) in a beautiful \(color)"
  }
}
```

`Hoverboard`的实例用使用`Vehicle`初始化方法提供的默认轮子数：
```swift
let hoverboard = Hoverboard(color: "silver")
print("Hoverboard: \(hoverboard.description)")
// Hoverboard: 0 wheel(s) in a beautiful silver
```

> 注意：
> 子类可以在初始化期间修改继承而来的变量属性，但是不能修改继承而来的常量属性。

### 自动继承初始化方法

如上面提到的，子类并不默认地继承父类的初始化方法。但是，如果满足某些条件，父类的初始化方法会自动被继承。实践中，这意味着，在许多常见场景，你不需要重写初始化方法，而且只要安全，可以用最小的代价继承父类的初始化方法。

假设你为子类中新引入的属性提供默认值，则适用以下两个规则：

##### **规则一**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果子类没有定义任何指定初始化方法，则它自动继承父类的所有指定初始化方法。

##### **规则二**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果子类提供了父类*所有*指定初始化方法–––无论是通过规则一继承，还是作为自身定义的一部分提供自定义实现–––这它自动继承父类所有的便捷初始化方法。

即使子类进一步添加了便捷初始化方法，这些规则仍然适用。

> 注意：
> 一个子类可以实现一个父类指定初始化方法，作为子类的便捷初始化方法。满足规则二。

### 指定初始化方法和便捷初始化方法实践

下面的例子展示了实践中的指定初始化方法，便捷初始化方法和自动继承初始化方法。这个例子定义了三个类`Food`，`RecipeIngredient`和`ShoppingListItem`的结构图，然后演示了它们的初始化方法如何交互。

结构图中的基础类是`Food`，它是食物名称的简单封装。`Food`类引入了唯一的`String`属性`name`，然后提供了两个创建`Food`实例的初始化方法：
```swift
class Food {
  var name: String
  init(name: String) {
    self.name = name
  }
  convenience init() {
    self.init(name: "[Unnamed]")
  }
}
```

下图展示了`Food`类的初始化方法链：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/initializersExample01_2x.png" alt="指定初始化方法和便捷初始化方法实践" width="500"/>
</p>

类没有默认成员初始化方法，所以`Food`类提供了一个接受单个参数`name`的指定初始化方法。这个初始化方法可以用指定的名称创建一个`Food`实例：
```swift
let namedMeat = Food(name: "Bacon")
// namedMeat's name is "Bacon"
```

`Food`类的初始化方法`init(name: String)`是指定初始化方法，因为它确保一个新`Food`实例的所有储存属性被完全初始化。`Food`类没有父类，所以`init(name: String)`初始化方法不需要调用`super.init()`来完成初始化。

`Food`类也提供了一个无参数便捷初始化方法`init()`。`init()`初始化方法通过用`name`值`[Unnamed]`交叉委托`Food`类的`init(name: String)`初始化方法，为新实例提供了一个默认占位符名称:
```swift
let mysteryMeat = Food()
// mysteryMeat's name is "[Unnamed]"
```

类结构图中的第二个类是`Food`的子类`RecipeIngredient`。`RecipeIngredient`类为烹饪食谱中的一种成分建模。它引入了一个`Int`属性`quantity`（除了从`Food`继承而来的`name`属性），然后定义了两个创建`RecipeIngredient`实例的初始化方法：
```swift
class RecipeIngredient: Food {
  var quantity: Int
  init(name: String, quantity: Int) {
    self.quantity = quantity
    super.init(name: name)
  }
  override convenience init(name: String) {
    self.init(name: name, quantity: 1)
  }
}
```

下图展示了`RecipeIngredient`类的初始化方法链：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/initializersExample02_2x.png" alt="指定初始化方法和便捷初始化方法实践" width="500"/>
</p>

`RecipeIngredient`类有一个唯一的指定初始化方法`init(name: String, quantity: Int)`，该初始化方法可被用于填充一个新`RecipeIngredient`实例的所有属性。这个初始化方法始于将传入的`quantity`实参赋值给`quantity`属性，该属性是唯一有`RecipeIngredient`引入的新属性。这么做之后，初始化方法向上委托给`Food`类的初始化方法`init(name: String)`。这个过程满足[两步初始化](#两步初始化)中的安全检查一。

`RecipeIngredient`也定义类一个便捷初始化方法`init(name: String)`，该初始化方法只用名称创建一个`RecipeIngredient`实例。这个初始化方法假设任何没有明确指明数量的`RecipeIngredient`实例，其数量均为`1`。这个便捷初始化方法使创建`RecipeIngredient`实例更快更便利，且避免了当要创建多个数量为`1`的`RecipeIngredient`实例时的代码重复。这个便捷初始化方法简单地检查委托类的指定初始化方法，传入`quantity`的值`1`。

`RecipeIngredient`提供的便捷初始化方法`init(name: String)`接受与`Food`指定初始化方法相同的参数。因为这个初始化方法重写了父类的指定初始化方法，它必须被`override`修饰符标记（如[初始化的继承和重写](#初始化的继承和重写)中所述）。

即使`RecipeIngredient`提供`init(name: String)`作为一个便捷初始化方法，`RecipeIngredient`还是提供了所有父类指定初始化方法的实现。因此，`RecipeIngredient`也自动继承父类所有的便捷初始化方法。

在这个例子中，`RecipeIngredient`的父类是`Food`，它有一个唯一的便捷初始化方法`init()`。因此这个初始化方法被`RecipeIngredient`继承。`init()`函数的继承版本与`Food`的版本刚好相同，除了它是委托给`RecipeIngredient`版本的`init(name: String)`而不是`Food`的版本。

所有这三个初始化方法都可被用于创建一个`RecipeIngredient`实例：
```swift
let oneMysteryItem = RecipeIngredient()
let oneBacon = RecipeIngredient(name: "Bacon")
let sixEggs = RecipeIngredient(name: "Eggs", quantity: 6)
```

类结构图中第三个也是最后一个类`ShoppingListItem`是`RecipeIngredient`的子类。`ShoppingListItem`类为购物单中的食谱成分建模。

购物单中的每个项一开始为"未买单"。为表示这个，`ShoppingListItem`引入了一个布尔属性`purchased`，默认值是`false`。`ShoppingListItem`也引入了一个计算属性`description`，该属性提供`ShoppingListItem`实例的文本描述：
```swift
class ShoppingListItem: RecipeIngredient {
  var purchased = false
  var description: String {
    var output = "\(quantity) x \(name)"
    output += purchased ? " ✔" : " ✘"
    return output
  }
}
```

> 注意：
> `ShoppingListItem`没有定义一个初始化方法为`purchased`提供初始值，因为购物单中的项一开始总是未买单。

因为为所有引入的属性提供了默认值，且本身没有定义任何初始化方法，`ShoppingListItem`自动继承父类所有的指定初始化方法和便捷初始化方法。

下面的图标展示了三个类的全体初始化链：

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/initializersExample03_2x.png" alt="指定初始化方法和便捷初始化方法实践" width="500"/>
</p>

你可以用所有这是三个继承的初始化方法类创建一个新`ShoppingListItem`实例：
```swift
var breakfastList = [
    ShoppingListItem(),
    ShoppingListItem(name: "Bacon"),
    ShoppingListItem(name: "Eggs", quantity: 6),
]
breakfastList[0].name = "Orange juice"
breakfastList[0].purchased = true
for item in breakfastList {
    print(item.description)
}
// 1 x Orange juice ✔
// 1 x Bacon ✘
// 6 x Eggs ✘
```

这里，为包含三个`ShoppingListItem`实例的数组字面量创建一个数组`breakfastList`。数组的类型被推导为`[ShoppingListItem]`。数组被创建后，数组中最开始的`ShoppingListItem`的名称从`[Unnamed]`改变为`Orange juice`，并被标记为已购买。打印数组中每个项的描述信息显示，他们的默认状态都按预期设置。

## 可失败初始化方法

有时候定义初始化可失败的类，结构体或枚举比较有用。这个失败可能被无效的初始化参数，外部必要自然的缺失或其他阻止成功初始化的条件触发。

为了应对可以失败的初始化条件，定义一个或多个可失败初始化方法作为类，结构体或枚举定义的一部分。通过在`init`关键字后面写上问号(`init?`)来编写可失败初始化方法。

> 注意：
> 你不能定义拥有相同参数类型和参数名的可失败初始化方法和非可失败初始化方法。

一个可失败初始化方法创建一个其初始化类型的可选值。在可失败初始化方法里写上`return nil`表明可以在该点触发初始化失败。

> 注意：
> 严格地讲，初始化方法并不返回一个值。相反，它们的功能是确保初始化结束时，`self`被完全和正确地初始化。即便你用`return nil`触发初始化失败，你不需要用`return`关键字表明初始化成功。

例如，为数字类型转换实现可失败初始化方法。为了确保数字类型间的转换能准确的维持数值，用`init(exactly:)`初始化方法。如果类型转换不能维持数值，则初始化失败：
```swift
let wholeNumber: Double = 12345.0
let pi = 3.14159

if let valueMaintained = Int(exactly: wholeNumber) {
  print("\(wholeNumber) conversion to Int maintains value of \(valueMaintained)")
}
// Prints "12345.0 conversion to Int maintains value of 12345"

let valueChanged = Int(exactly: pi)
// valueChanged is of type Int?, not Int

if valueChanged == nil {
  print("\(pi) conversion to Int does not maintain value")
}
// Prints "3.14159 conversion to Int does not maintain value"
```

下面的例子定义了一个结构体`Animal`，有一个`String`常量属性`species`。`Animal`结构体也定义了一个接受单个参数`species`的可失败初始化方法。如果发现了一个空字符串，就会触发初始化失败。否则，为`species`属性赋值，初始化成功：
```swift
struct Animal {
  let species: String
  init?(species: String) {
    if species.isEmpty { return nil }
    self.species = species
  }
}
```

你可以用这个可失败初始化方法，试着初始化一个新`Animal`实例，然后检查初始化是否成功：
```swift
let someCreature = Animal(species: "Giraffe")
// someCreature is of type Animal?, not Animal

if let giraffe = someCreature {
  print("An animal was initialized with a species of \(giraffe.species)")
}
// Prints "An animal was initialized with a species of Giraffe"
```

如果你传入一个空字符串给可失败初始化方法的`species`参数，初始化方法触发初始化失败：
```swift
let anonymousCreature = Animal(species: "")
// anonymousCreature is of type Animal?, not Animal

if anonymousCreature == nil {
    print("The anonymous creature could not be initialized")
}
// Prints "The anonymous creature could not be initialized"
```

> 注意：
> 检查空字符串（例如`""`而不是`"Giraffe"`与检查`nil`表明可选字符串值的缺失是不同的。在上面的例子中，一个空字符串(`""`)是有效的，非可选字符串。但是，动物的`species`属性有一个空字符串是不合适的。为了模拟这个限制，如果发现是空字符串，可失败初始化方法触发一个初始化失败。

### 枚举的可失败初始化方法

可以用可失败初始化方法基于一个或多个参数选择一个可是的枚举分支。如果提供的参数不匹配相应的枚举分支，初始化方法失败。

下面的例子定义了一个枚举`TemperatureUnit`，有三个可能状态（`kelvin`，`celsius`和`fahrenheit`）。用一个可失败初始化方法为代表温度符号的字符(`Character`)值寻找一个合适的枚举分支：
```swift
enum TemperatureUnit {
  case kelvin, celsius, fahrenheit
  init?(symbol: Character) {
    switch symbol {
      case "K":
        self = .kelvin
      case "C":
        self = .celsius
      case "F":
        self = .fahrenheit
      default:
        return nil
    }
  }
}
```

你可以用这个可失败初始化方法为三个可能的状态选择一个合适的枚举分支，如果参数不匹配任何一个状态则引起初始化失败：
```swift
let fahrenheitUnit = TemperatureUnit(symbol: "F")
if fahrenheitUnit != nil {
    print("This is a defined temperature unit, so initialization succeeded.")
}
// Prints "This is a defined temperature unit, so initialization succeeded."

let unknownUnit = TemperatureUnit(symbol: "X")
if unknownUnit == nil {
    print("This is not a defined temperature unit, so initialization failed.")
}
// Prints "This is not a defined temperature unit, so initialization failed."
```

### 有原始值枚举的可失败初始化方法

有原始值的枚举自动获得一个可失败初始化方法`init?(rawValue:)`，该方法接受一个相应原始类型的参数`rawValue`，然后选择一个枚举分支，如果找到了的话，或如果找不到，则触发一个初始化错误。

可以重写上面例子中的`TemperatureUnit`，来查看`Character`类型的原始值，和应用`init?(rawValue:)`初始化方法：
```swift
enum TemperatureUnit: Character {
    case kelvin = "K", celsius = "C", fahrenheit = "F"
}

let fahrenheitUnit = TemperatureUnit(rawValue: "F")
if fahrenheitUnit != nil {
    print("This is a defined temperature unit, so initialization succeeded.")
}
// Prints "This is a defined temperature unit, so initialization succeeded."

let unknownUnit = TemperatureUnit(rawValue: "X")
if unknownUnit == nil {
    print("This is not a defined temperature unit, so initialization failed.")
}
// Prints "This is not a defined temperature unit, so initialization failed."
```

### 初始化失败的传递

类，结构体或枚举的初始化失败可以交叉委托给相同类，结构体或枚举中的另一个可失败初始化方法。类似地，子类的可失败初始化方法可以向上委托给一个父类的可失败初始化方法。

在任一情况下，如果你委托给两一个引起初始化失败的初始化方法，则整个初始化立即失败，不会再执行进一步初始化。

> 注意：
> 一个可失败初始化方法也可以委托一个非可失败初始化方法。如果你需要给现有的初始化过程添加一个潜在的失败状态，否则就会失败，请用这种操作。

下面的例子定义了`Product`的子类`CartItem`。`CartItem`类模拟一个在线购物车中的项。`CartItem`引入了一个储存常量属性`quantity`，并确保这个属性总是有最小为`1`的值：
```swift
class Product {
  let name: String
  init?(name: String) {
    if name.isEmpty { return nil }
    self.name = name
  }
}

class CartItem: Product {
  let quantity: Int
  init?(name: String, quantity: Int) {
    if quantity < 1 { return nil }
    self.quantity = quantity
    super.init(name: name)
  }
}
```

`CartItem`的可失败初始化方法一开始验证接收到的`quantity`值大于或等于`1`。如果`quantity`无效，整个初始化过程立即失败且不会进一步执行初始化代码。同样，`Product`的可失败初始化方法检查`name`值，如果`name`是空字符串，初始化处理立即失败。

如果用非空名称和一个大于或等于`1`的数量创建一个`CartItem`实例，初始化会成功：
```swift
if let twoSocks = CartItem(name: "sock", quantity: 2) {
  print("Item: \(twoSocks.name), quantity: \(twoSocks.quantity)")
}
// Prints "Item: sock, quantity: 2"
```

如果你试图用值为`0`的`quantity`创建新的`CartItem`实例，`CartItem`的初始化方法会引起初始化失败：
```swift
if let zeroShirts = CartItem(name: "shirt", quantity: 0) {
    print("Item: \(zeroShirts.name), quantity: \(zeroShirts.quantity)")
} else {
    print("Unable to initialize zero shirts")
}
// Prints "Unable to initialize zero shirts"
```

类似地，如果用空值`name`创建`CartItem`实例，父类`Product`的初始化方法会引起初始化失败：
```swift
if let oneUnnamed = CartItem(name: "", quantity: 1) {
    print("Item: \(oneUnnamed.name), quantity: \(oneUnnamed.quantity)")
} else {
    print("Unable to initialize one unnamed product")
}
// Prints "Unable to initialize one unnamed product"
```

### 重写一个可失败初始化方法

你可以在子类中像重写其他初始化方法一样，重写一个父类的可失败初始化方法。另外，你可以用子类的非可失败初始化方法重写父类的可失败初始化方法。这能让你定义一个初始化不会失败的子类，即使父类的初始化过程允许失败。

注意如果你用子类的一个非可失败初始化方法重写父类的一个可失败初始化方法，唯一的向上委托给父类初始化方法的做法是强制解包父类可失败初始化方法的结果。

> 注意：
> 你可以用非可失败初始化方法重写可失败初始化方法，但是反之不然。

下面的例子定义了一个类`Document`。这个类模拟一个文档，该文档可以被一个`name`属性初始化，`name`属性可以是非空字符串或`nil`，但是不能是空字符串：
```swift
class Document {
    var name: String?
    // this initializer creates a document with a nil name value
    init () {}
    // this initializer creates a document with a nonempty name value
    init?(name: String) {
        if name.isEmpty { return nil }
        self.name = name
    }
}
```

下一个例子了一个`Document`的子类`AutomaticallyNamedDocument`。`AutomaticallyNamedDocument`子类重写了`Document`中引入的两个指定初始化方法。这些重写在不用`name`初始化实例或给`init(name:)`传入空字符串的情况下，确保`AutomaticallyNamedDocument`实例有一个值为`[Untitled]`的初始`name`：
```swift
class AutomaticallyNamedDocument: Document {
    override init() {
        super.init()
        self.name = "[Untitled]"
    }
    override init(name: String) {
        super.init()
        if name.isEmpty {
            self.name = "[Untitled]"
        } else {
            self.name = name
        }
    }
}
```

`AutomaticallyNamedDocument`用非可失败初始化方法`init(name:)`重写了其父类的可失败初始化方法`init?(name:)`。因为`AutomaticallyNamedDocument`用与父类不同的方式处理了空字符串的情况，其初始化方法不需要失败，所以作为替换提供了一个非可失败版本的初始化方法。

可以把在初始化方法中，用强制解包的方式调用父类可失败初始化方法，作为子类非可失败初始化方法实现的一部分。举个例子，下面的`UntitledDocument`子类总是被命名为`"[Untitled]"`，它在初始化过程中，用父类的可失败初始化方法`init(name:)`：
```swift
class UntitledDocument {
    override init() {
        super.init(name: "[Untitled]")!
    }
}
```

在这个情况下，如果用空字符串作为名称调用父类的初始化方法`init(name:)`，强制解包操纵会导致运行时错误。但是，因为是用字符串常量调用，你可以看到初始化方法不会失败，所以这种情况下不会发生运行时错误。

### `init!`可失败初始化方法

一般通过在`init`关键字后面写上问号(`?`)来定义一个创建相应类型可选实例的可失败初始化方法。另外，你可以定义一个创建相应类型的隐式解包可选实例可初始化方法。要这么做，在`init`关键字后面写上感叹号(`!`)来替换问号(`?`)。

你可以从`init?`委托给`init!`，反之亦然，你可以用`init!`重写`init?`，反之亦然。你也可以从`init`委托给`init!`，即使如果`init!`导致初始化失败这么做会触发断言。

## 必须初始化方法

将`required`修饰符写在类初始化方法的定义前，表明该类的每个子类必须实现该初始化方法：
```swift
class SomeClass {
    required init() {
        // initializer implementation goes here
    }
}
```

你必须也在必须初始化方法的子类实现前面写上`required`修饰符，用以表明初始化方法的要求进一步应用到了继承链中的子类上。当重写必须初始化方法时，不比写`override`修饰符：
```swift
class SomeSubClass: SomeClass {
    required init() {
        // subclass implementation of the required initializer goes here
    }
}
```

> 注意：
> 如果你满足了一个必须初始化方法的要求，你不必提供一个必须初始化方法的显式实现。

## 用闭包或函数设置默认属性值

如果一个储存属性的默认值需要一些自定义或设置，你可以用一个闭包或全局函数为该属性提供自定义的默认值。任何时候当属性类型的实例被初始化时，会调用闭包或函数，然后把返回值赋值给属性当作默认值。

一般这种类型的闭包或函数创建一个与属性类型一致的临时值，定制该值以表示所需的初始状态，然后返回该临时值以被用作属性的默认值。

下面是如何用闭包为属性提供默认值的框架轮廓：
```swift
class SomeClass {
    let someProperty: SomeType = {
        // create a default value for someProperty inside this closure
        // someValue must be of the same type as SomeType
        return someValue
    }()
}
```

注意闭包的闭合大括号后面有一对小括号。这告诉Swift立即执行该闭包。如果省略了小括号，则变成你要把闭包而不是闭包的返回值赋值给该属性。

> 注意：
> 如果用闭包来初始化属性，记住当闭包被执行时，实例的其余部分尚未被初始化。这意味着你不能在闭包中访问任何其他属性，你是这些属性有默认值。你也不能使用隐藏属性`self`，或调用任何实例方法。

下面的例子定义了一个结构体`Chessboard`，它模拟国际象棋的棋盘。国际象棋在8x8，黑白相间的棋盘上玩。

<p align="center">
<img src="https://docs.swift.org/swift-book/_images/chessBoard_2x.png" alt="用闭包或函数设置默认属性值" width="200"/>
</p>

为了表示这个棋盘，结构体`Chessboard`有一个唯一的属性`boardColors`，该属性是一个包含64个`Bool`值的属性。数组中的`true`代表一个黑方块，`false`代表一个白方块。数组中的第一个项代表棋盘的左上方块，最后一个项代表右下的方块。

使用闭包来初始化数组`boardColors`以设置其颜色值：
```swift
struct Chessboard {
    let boardColors: [Bool] = {
        var temporaryBoard = [Bool]()
        var isBlack = false
        for i in 1...8 {
            for j in 1...8 {
                temporaryBoard.append(isBlack)
                isBlack = !.isBlack
            }
            isBlack = !isBlack
        }
        return temporaryBoard
    }()
    func squareIsBlackAt(row: Int, column: Int) -> Bool {
        return boardColors[](row * 8) + column]
    }
}
```

任何时候创建`Chessboard`实例，闭包都会被调用，计算并返回`boardColors`的默认值。上面例子中的闭包计算并将每个方块设置适当的颜色设置到临时数组`temporaryBoard`中，然后，一旦设置完成，把这个临时数组作为闭包的返回值返回。返回的数组存放在`boardColors`中，可以被`squareIsBlackAt(row:column:)`工具函数查询：
```swift
let board = Chessboard()
print(board.squareIsBlackAt(row: 0, column: 1))
// Prints "true"
print(board.squareIsBlackAt(row: 7, column: 7))
// Prints "false"
```

[< 继承](Inheritance.md) || [反初始化 >](Deinitialization.md)
