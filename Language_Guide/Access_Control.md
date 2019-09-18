# 访问控制

*访问控制* 限制其他代码或模块访问你代码的某部分。这个功能能让你隐藏代码的实现细节，且能指定访问并使用代码的优先接口，

可以为单个类型（类，结构体和枚举）指定访问级别，也能为这些类型的属性，方法，初始化方法和下标指定访问级别。协议可以被限制为某一上下文，全局常量，变量和函数也能。

除了提供多个级别的访问控制，Swift为特定场景提供默认的访问控制，以减少显式指定访问控制级别的需要。确实，如果你编写仅有单个目标程序的app，你或许不需要显式指定访问控制级别。

> 注意：
> 简便起见，代码中（属性，类型，函数等）可以指定访问控制各个方面，在下面章节中称为“实体”。

## 模块和源文件

Swift的访问控制模型基于模块和源文件的概念。

一个*模块*是一个发布的代码单元–––一个被构建或继承作为单个单元的框架或应用，可以被另一个模块用Swift中的`import`关键字导入。

在Swift中，Xcode中的每个构建目标（如一个app包或框架）都被看作是一个独立的模块。如果你把app代码的几个方面集合成一个独立的框架–––也许是为了封装然后跨多个应用重用该代码–––则当其被导入和应用到一个app中，或被用于另一个框架中时，你在该框架中定义的任何东西将是一个独立模块的一部分。

一个*源文件*是一个模块中的单个Swift源代码文件（简单地说，模块或框架中的单个文件）。即使将单独的类型定义在单独的源文件中比较常见，一个单独的源文件也可以包含多个类型，函数等的定义。

## 访问级别

Swift为代码实体提供五个不同的*访问级别*。这些访问级别相对于定义实体的源文件，也相对于包含源文件的模块。
* *开放访问* 和 *公开访问* 允许同一模块中的任何源文件访问实体，也允许任何其他导入此模块的模块中的源文件访问实体。当指定框架的公开接口时，一般用开放或公开访问。开放访问和公开访问的不同在下面有描述。
* *内部访问* 允许同一模块中任何源文件（中的代码）访问实体，但是不允许任何外部模块访问。当定义app或框架的内部结构体时，一般用内部访问。
* *文件私有访问* 将实体的使用限制在自身源文件中。当实现细节只在单个文件中使用时，用文件私有访问隐藏特定功能的实现细节。
* *私有访问* 将实体的使用限制在封闭声明和同一文件声明的扩展中。当实现细节只在单个声明中使用时，用文件私有访问隐藏特定功能的实现细节。

开放访问是最高（限制最少）级别的访问级别，而私有访问是最低（限制最多）级别的访问级别。

开放访问只应用在类和类成员上，不同与公开访问，开放访问允许外部模块中的代码继承和重写模块中的代码，如[子类化](#子类化)中所述。显示标记类为开放，表明你已经考虑到其他模块中的代码将该类用作父类带来的影响，且你已经为类的代码进行了相应的设计。

### 访问级别的指导原则

Swift中的访问控制遵从一个总体指导原则：*不根据另一个具有较低（更多限制）访问级别的实体定义实体*。

例如：
* 一个公开变量不能被定义为拥有一个内部，文件私有或私有的类型，因为在使用这个公开变量的地方，类型可能不可用。
* 一个函数不能比其参数类型和返回类型拥有更高的访问级别，因为函数可能在对于上下文代码其某一部分不可用的情况下依然可用。

本指导原则对于不同方面的具体意思，在下面详述。

### 默认访问级别

如果不显式指定访问级别，代码中所有实体都有一个默认访问级别*内部访问*。因此，大多数情况下，你不需要显式指定访问级别。

### 单个目标应用的访问级别

当你编写单个目标的应用时，应用的代码自包含在引用中，并不需要使其对应用模块之外可用。默认访问级别内部访问已经满足这个要求。因此，不需要指定自定义访问控制。但是，你也许想将某部分代码标记为文件私有或私有，以对应用模块代码隐藏其实现细节。

### 框架的访问级别

当你开发一个框架时，将该框架的公开接口标记为开放或公开，以让其他模块，例如一个导入了此框架的应用，查看或访问这些接口。这些公开的接口就是该框架的应用程序编程接口（即API）。

> 注意：
> 框架的任何内部实现细节仍然可以用默认的内部访问，或者如果你想对框架其他部分的内部代码隐藏它们，则可以标记为私有或文件私有。如果你希望一个实体成为框架的API，则需要将其标记为开放或公开。

### 单元测试目标的访问级别

当你编写一个带有单元测试目标的应用时，为了测试，应用中的代码需要在单元测试模块中可用。默认地，只有被标记为开放或公开的实体才在其他模块中可用。但是，如果用`@testable`特性标记产品模块的导入声明，启用测试并编译该产品模块，则单元测试目标可以访问任何内部实体。

## 访问控制语法

通过在在实体声明的开始写上`open`，`public`，`internal`，`fileprivate`或`private`修饰符，来定义实体的访问级别。
```swift
public class SomePublicClass {}
internal class SomeInternalClass {}
fileprivate class SomeFilePrivateClass {}
private class SomePrivateClass {}

public var somePublicVariable = 0
internal let someInternalConstant = 0
fileprivate func someFilePrivateFunction() {}
private func somePrivateFunction() {}
```

除非另外指定，默认访问级别是内部(`internal`)，如[默认访问级别](#默认访问级别)中所述。这意味着`SomeInternalClass`和`someInternalConstant`可以不用写显式访问级别修饰符，而照样拥有内部(`internal`)访问级别：
```swift
class SomeInternalClass {}    // implicitly internal
let someInternalConstant = 0  // implicitly internal
```

## 自定义类型

如果你想为自定义类型指定显式访问级别，请在定义类型的时候指定。然后，可以在访问控制允许的任何地方使用该类型。举个例子，如果定义了一个文件私有类，则该类只能在其所在源文件中，被用作属性的类型，或函数的参数或返回类型。

一个类型的访问控制级别也影响该类型*成员*（其属性，方法，初始化方法和下标）的默认访问控制级别。如果把一个类型的访问级别定义为私有或文件私有，则其成员的默认访问级别也是私有或文件私有。如果把一个类型的访问级别定义为内部或公开（或不显式指定访问级别，用默认的内部访问级别），则其成员的默认访问级别将是内部。

> 注意：
> 一个公开类型默认拥有内部成员，而不是公开成员。如果你希望一个类型成员是公开，你必须显式标记其为公开。这种操作确保公开一个类型的API是可以选择发布的，同时避免错误地将类型内部工作呈现为公开API。

```swift
public class SomePublicClass {                  // explicitly public class
    public var somePublicProperty = 0            // explicitly public class member
    var someInternalProperty = 0                 // implicitly internal class member
    fileprivate func someFilePrivateMethod() {}  // explicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
}

class SomeInternalClass {                       // implicitly internal class
    var someInternalProperty = 0                 // implicitly internal class member
    fileprivate func someFilePrivateMethod() {}  // explicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
}

fileprivate class SomeFilePrivateClass {        // explicitly file-private class
    func someFilePrivateMethod() {}              // implicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
}

private class SomePrivateClass {                // explicitly private class
    func somePrivateMethod() {}                  // implicitly private class member
}
```

### 元组类型

元组类型的访问级别是元组中用到的所有访问级别中中限制最多的访问级别。举个例子，如果把两个不同类型组合成元组，一个是内部访问，另一个是私有访问，则组成的元组类型的访问级别是私有。

> 注意：
> 元组类型没有类，结构体，枚举和函数那样独立的定义。一个元组类型的访问级别自动由组成该元组的类型决定，不能被显式指定。

### 函数类型

函数类型的访问级别按函数参数类型和返回类型中限制最多的计算。如果计算得到的访问级别不匹配上下文的默认级别，则必须在函数定义中显式指定访问级别。

下面的例子定义了一个全局函数`someFunction()`，没有为函数提供特定的访问级别修饰符。你也许希望这个函数由默认访问级别`internal`，但是并不是这样。实际上，如下编写的`someFunction()`并不会通过编译：
```swift
func someFunction() -> (SomeInternalClass, SomePrivateClass) {
    // function implementation goes here
}
```

函数的返回类型是一个由[自定义类型](#自定义类型)中定义的两个自定义类组成的元组类型。这些类中的一个被定义为内部，另一个被定义为私有。因此，符合元组的整体访问级别是私有（元组组成类型的最低访问级别）。

因为函数的返回类型是私有，所以你必须用`private`修饰符标记函数的整个访问级别，以让函数声明有效：
```swift
private func someFunction() -> (SomeInternalClass, SomePrivateClass) {
    // function implementation goes here
}
```

用`public`或`internal`修饰符标记`someFunction()`的定义或用默认的内部访问是无效的，因为函数的公开或内部使用者，可能没有访问函数返回类型中私有类的合适权限。

### 枚举类型

枚举的单个分支自动获得与其所属枚举相同访问级别。你不能为枚举的单个分支指定其他访问级别。

在下面的例子中，枚举`CompassPoint`有一个公开的显式访问级别。枚举分支`north`，`south`，`east`和`west`也因此有公开的访问级别：
```swift
public enum CompassPoint {
    case north
    case south
    case east
    case west
}
```

#### 原始值和关联值

枚举定义中原始值或关联值的类型，必须用一个最低高于枚举访问级别的访问级别。例如，你不能把一个私有类型用作一个内部访问级别枚举的原始值类型。

### 嵌套类型

如果包含的类型是公开的，则嵌套类型的访问级别与其包含的类型的访问级别相同。公开类型中定义的嵌套类型自动拥有内部访问级别。如果你希望公开类型中的嵌套类型公开可用，你必须显式声明嵌套类型为公开。

## 子类化

你可以子类化任何在当前访问上下文中可访问的类，和任何定义在相同模块中的子类。你也可以子类化任何定义在不同模块中的开放类。一个子类不能有比其父类更高的访问级别–––举个例子，你不能编写内部父类的公开子类。

另外，对于定义在相同模块中的类，你可以重写任何在特定访问上下文中可见的类成员（方法，属性，初始化方法或下标）。对于定义在另一个模块中的类，你可以重写任何开放类成员。

重写可以使一个继承的类成员比其父类版本更加具有访问性。在下面的例子中，类`A`是一个拥有文件私有方法`someMethod()`的公开类。类`B`是`A`的子类，降低了访问级别为`internal`。尽管如此，类`B`提供了方法`someMethod()`的`internal`访问级别的重写，这比`someMethod()`原始实现的访问级别要高：
```swift
public class A {
    fileprivate func someMethod() {}
}

internal class B: A {
    override internal func someMethod() {}
}
```

即使一个子类成员调用一个拥有比子类成员的访问权限低的父类成员也是有效的，只要对父类成员的调用发生在允许的访问级别上下文中（也就是，在与父类相同源文件中的文件私有成员调用，或在与父类相同模块中内部成员调用）：
```swift
public class A {
    fileprivate func someMethod() {}
}

internal class B: A {
    override internal func someMethod() {
        super.someMethod()
    }
}
```

因为父类`A`和子类`B`被定义在同一个源文件中，在`B`的`someMethod()`实现中调用`super.someMethod()`是有效的。

## 常量，变量，属性和下标

一个常量，变量或属性不能比其类型更公开。例如，用一个私有类型定义公开属性是无效的。类似地，一个下标必能比其索引类型或返回类型更加公开。

如果一个常量，变量，属性或下标使用了一个私有类型，则该常量，变量，属性或下标必须也被标记为`private`：
```swift
private var privateInstance = SomePrivateClass()
```

### Getters和Setters

常量，变量，属性和下标的Getter方法和Setter方法自动拥有与该常量，变量，属性和下标相同的访问级别。

你可以给setter方法设置比其对应的getter方法更低的访问级别，以限制该变量，属性或下标的读写权限范围。在`var`或`subscript`声明符前使用`fileprivate(set)`，`private(set)`或`internal(set)`来指定更低的访问级别。

> 注意：
> 这个规则适用于储存属性，也适用于计算属性。即使你没有为储存属性书写显式的getter和setter方法，Swift仍然会合成隐式getter和setter方法，以提供对储存属性底层储存的访问。用`fileprivate(set)`，`private(set)`和`internal(set)`改变合成的setter的访问级别，其方式与计算属性显式定义setter的方式完全相同，

下面的例子定义了一个结构体`TrackedString`，该结构体统计一个字符串属性被修改的次数：
```swift
struct TrackedString {
    private(set) var numberOfEdits = 0
    var value: String = "" {
        didSet {
            numberOfEdits += 1
        }
    }
}
```

## 初始化方法

## 协议

## 扩展

## 泛型

泛型类型或泛型函数的访问级别，是泛型类型或泛型函数本身访问级别和，任何其类型参数的类型约束访问级别中的最低访问级别。

## 类型别名

为了访问控制，你定义的任何类型别名都会被是为不同的类型。一个类型别名可以有低于或等于其命名的类型的访问级别。举个例子，一个私有类型别名可以作为一个私有，文件私有，内部，公开或开放的类型的别名，但是一个公开类型不能作为一个内部，文件私有或私有类型的别名。

> 注意：
> 这个规则也适用于为了满足协议一致性而使用的关联类型的别名。

[< 内存安全](Memory_Safety.md) || [高级操作符 >](Advanced_Operators.md)
