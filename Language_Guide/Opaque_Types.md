# 不透明类型

一个有不透明返回类型的函数或方法，会隐藏返回值的类型信息。不是提供具体类型作为函数返回类型，而是用其支持的协议类描述返回值。在模块和调用模块代码的边界间隐藏类型信息比较有用，因为返回值的底层类型可以保持私有。不同于类型是协议类型的返回值，不透明类型保持类型身份–––编译器有权访问类型信息，模块客户端没有。

## 不透明类型解决的问题

举个例子，假如你要编写一个模块用来绘制[ASCII文字画](https://en.wikipedia.org/wiki/ASCII_art)。一个ASCII文字画的基本成员是一个返回代表该图形字符串的`draw()`方法，你可以把这个方法用做`Shape`的协议要求：
```swift
protocol Shape {
    func draw() -> String
}
struct Triangle: Shape {
    var size: Int
    func draw() -> String {
        var result = [String]()
        for length in 1...size {
            result.append(String(repeating: "*", count: length))
        }
        return result.joined(separator: "\n")
    }
}
let smallTriangle = Triangle(size: 3)
print(smallTriangle.draw())
// *
// **
// ***
```

可以用泛型实现诸如垂直翻转图形的操作，如下面代码所示。但是，这个操作有一个限制：翻转的结果暴露了泛型的类型。
```swift
struct FlippedShape<T: Shape>: Shape {
    var shape: T
    func draw() -> String {
        let lines = shape.draw().split(separator: "\n")
        return lines.reversed().joined(separator: "\n")
    }
}
let flippedTriangle = FlippedShape(shape: smallTriangle)
print(flippedTriangle.draw())
// ***
// **
// *
```

如下面代码所示，定义`JoinedShape<T: Shape, U: Shape>`结构体将两个图形垂直相加的的操作，其结果如将一个翻转的矩形与另一个相加的结果`JoinedShape<FlippedShape<Triangle>, Triangle>`类型一样。
```swift
struct JoinedShape<T: Shape, U: Shape>: Shape {
    var top: T
    var bottom: U
    func draw() -> String {
        return top.draw() + "\n" + bottom.draw()
    }
}
let joinedTriangles = JoinedShape(top: smallTriangle, bottom: flippedTriangle)
print(joinedTriangles.draw())
// *
// **
// ***
// ***
// **
// *
```

暴露创建图形的详细信息，允许本不属于ASCII文字画模块公开接口的类型暴露出来，因为需要声明完整的返回类型。模块内部的代码可以有很多种方法构建相同的图形，模块外使用图形的代码，不需要知道转换列表的实现细节。包装类型如`JoinedShape`和`FlippedShape`与模块的用户无关，它们应该不可见。模块的公开接口由诸如链接和翻转图形的操作组成，这些操作返回另一个`Shape`值。

## 返回一个不透明类型

可以把不透明类型想像成与泛型相反的类型。





























[< 泛型](Generics.md) || [自动引用计数 >](Automatic_Reference_Counting.md)
