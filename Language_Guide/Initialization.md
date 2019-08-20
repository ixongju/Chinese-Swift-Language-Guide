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
let cheeseQuestion = SurveyQuestion(text: "Do you like cheese?")
cheeseQuestion.ask()
// Prints "Do you like cheese?"
cheeseQuestion.response = "Yes, I do like cheese."
```

在询问之前问卷的回不是不可能知道的，所以`response`属性被声明为可选字符串类型`String?`。该属性自动被指定默认值`nil`，意思是当新`SurveyQuestion`实例被初始化时，“还没有字符串”。


## 两步初始化







[< 继承](Inheritance.md) || [反初始化 >](Deinitialization.md)
