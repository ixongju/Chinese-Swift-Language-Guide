# 类型转换

*类型转换* 是检查实例的类型的方法，或将该类型是为其类层次结构中其他不同的父类或子类的方式。

Swift中用`is`和`as`操作符实现类型转换。这两个操作符提供了简单富有表现力的检查一个值的类型或将一个值转换为不同类型的方法。

也可以用类型转换检查一个类型是否实现了某个协议，如果[检查协议一致性](Protocols.md#检查协议一致性)中所述。

## 为类型转换定义类层次结构

可以用类型转换与类和子类的层级结构来检查特定类实例的类型，并将该实例转换为层次结构中的另一个类型。下面的三个代码片段定义了一个类层次结构，和一个包含这些类实例的数组，为类型转换的例子所用。

第一个片段定义了一个新基础类`MediaItem`。这个类为出现在数字媒体库中的项目提供了基本功能。特别地，它声明了一个`String`类型的属性`name`，和一个`init name`初始化方法。（这里假设素有的媒体项目，包括所有的电影和歌曲，都有一个名称。）
```swift
class MediaItem {
    var name: String
    init(name: String) {
        self.name = name
    }
}
```

下一个片段定义了两个`MediaItem`的子类。第一个子类，`Movie`，封装了电影或视频的额外信息。它在基础类上添加了一个`director`属性，并且添加了对应的初始化方法。第二个子类，`Song`，添加了`artist`属性和初始化方法：
```swift
class Movie: MediaItem {
    var director: String
    init(name: String, director: String) {
        self.director = director
        super.init(name: name)
    }
}

class Song: MediaItem {
    var artist: String
    init(name: String, artist: String) {
        self.artist = artist
        super.init(name: name)
    }
}
```

最后一个片段创建了一个常量数组`library`，该数组包含两个`Movie`实例和三个`Song`实例。通过用数组字面量内容初始化`library`数组，可以推导出其类型。Swift的类型检查器可以推断出`Movie`和`Song`有共同的父类`MediaItem`，所以为数组`library`推导出类型`[MediaItem]`：
```swift
let library = [
    Movie(name: "Casablanca", director: "Michael Curtiz"),
    Song(name: "Blue Suede Shoes", artist: "Elvis Presley"),
    Movie(name: "Citizen Kane", director: "Orson Welles"),
    Song(name: "The One And Only", artist: "Chesney Hawkes"),
    Song(name: "Never Gonna Give You Up", artist: "Rick Astley")
]
// the type of "library" is inferred to be [MediaItem]
```

在底层，存放在`library`中的项目仍然是`Movie`和`Song`的实例。但是，如果你迭代数组的内容，你得到的项目被转换成了`MediaItem`类型，而不是`Movie`或`Song`。为了使用他们的原生类型，需要检查他们的类型，或向下转换为不同的类型，如上面所述。

## 类型检查

用*类型检查操作符*(`is`)检查一个实例是否是某个子类类型。如果实例是该子类类型，则类型检查操作符返回`true`，如果不是，则返回`false`。

下面的例子定义了两个变量，`movieCount`和`songCount`，用来统计`library`数组中`Movie`和`Song`实例的数量：
```swift
var movieCount = 0
var songCount = 0

for item in library {
    if item is Movie {
        movieCount += 1
    } else if item is Song {
        songCount += 1
    }
}

print("Media library contains \(movieCount) movies and \(songCount) songs")
// Prints "Media library contains 2 movies and 3 songs"
```

这个例子遍历了`library`数组中所有的项。每次循环，`for-in`循环将数组中下一个`MediaItem`值设置给常量`item`。

如果当前`MediaItem`是一个`Movie`实例，则`item is Movie`返回`true`，如果不是，则返回`false`。类似地，`item is Song`检查项是否是`Song`实例。`for-in`的最后，`movieCount`和`songCount`的值包含一个数找到多少个每个类型的`MediaItem`实例的数字。

## 向下转换

实际上，在幕后一个特定类类型的常量或变量可能指向一个该类型子类的实例。当你确信这种情况时，你可以试着用*类型转换操作符*(`as?`或`as!`)向下转换为子类类型。

因为向下转换可能失败，所以类型转换操作符有两种格式。条件格式，`as?`，返回一个向下转换类型的可选值。强制格式，`as!`，尝试将向下转换并强制解包转换结果作为一个符合操作。

当你不确定向下转换是否能成功时，用类型转换操作符的条件格式(`as?`)。这个格式的操作符总是返回一个可选值，如果不能向下转换则该值为`nil`。这让你能够检查是否成功转换。

当你能确定向下转换能成功时，用类型转换操作符的强制格式(`as!`)。如果你试图向下转换到一个错误的类类型，该格式会触发一个运行时错误。

下面的例子遍历`library`中的每个`MediaItem`，并且打印为每个项打印合适的描述信息。为了这么做，需要以真实的`Movie`或`Song`而不是`MediaItem`访问每个项。为了能够访问`Movie`或`Song`的`director`或`artist`属性用作描述需要这么做。

在这个例子中，数组中的每个项都有可能是`Movie`，也可能是`Song`。你不可能提前知道要为每个项使用哪个类，所以通过循环，每次都用用类型转换操作符的条件格式(`as?`)来检查向下转换比较合适：
```swift
for item in library {
    if let movie = item as? Movie {
        print("Movie: \(movie.name), dir. \(movie.director)")
    } else if let song = item as? Song {
        print("Song: \(song.name), by \(song.artist)")
    }
}

// Movie: Casablanca, dir. Michael Curtiz
// Song: Blue Suede Shoes, by Elvis Presley
// Movie: Citizen Kane, dir. Orson Welles
// Song: The One And Only, by Chesney Hawkes
// Song: Never Gonna Give You Up, by Rick Astley
```

例子中，一开始将当前项向下转换为`Movie`。因为`item`是一个`MediaItem`实例，有可能是一个`Movie`；同样地，也可能是一个`Song`，或干脆就是基本的`MediaItem`。因为这种不确定性，当试图向下转换为一个子类类型时，`as?`格式的类型转换操作符返回一个可选值。`item as? Movie`的结果是`Movie?`类型，或"可选的`Movie`"。

当把向下转换为`Movie`引用在数组中的`Song`实例上时会失败。为应对这个，上面的例子用可选绑定检查可选`Movie`是否真的包含一个值（也就是，询问向下转换是否成功）。这个可选绑定写作"`if let movie = item as? Movie`"，可以读作：

“尝试把`item`当作`Movie`访问。如果成功了，设置一个临时常量`movie`给储存在返回的可选`Movie`中的值。”

如果向下转换成功了，`movie`的属性会被用来打印`Movie`实例的描述，包括其`director`的名称。同样的做法用于检查`Song`实例，当在找到了`Song`时打印相应的描述（包括其`artist`名称）。

> 注意：
> 实际上，类型转换不修改实例或改变实例的值。底层实例仍然相同；只是简单地被当作被转换的类型去访问。

## Any和AnyObject的类型转换

Swift提供两种特别类型类处理非特定类型：
* `Any`可以表示任何类型的实例，包括函数类型。
* `AnyObject`可以表示任何类类型的实例。

只有当明显需要其提供的行为和能力时，才用`Any`和`AnyObject`。对于你想在代码中使用的类型，最好具体一些。

这里有一个例子，用`Any`处理多种不同的类型，包括函数类型和非类类型。例子中创建了一个数组`things`，可以存放`Any`类型的值：
```swift
var things = [Any]()

things.append(0)
things.append(0.0)
things.append(42)
things.append(3.14159)
things.append("hello")
things.append((3.0, 5.0))
things.append(Movie(name: "Ghostbusters", director: "Ivan Reitman"))
things.append({ (name: String) -> String in "Hello, \(name)" })
```

`things`数组包含两个`Int`值，两个`Double`值，一个`String`值，一个元组类型`(Double, Double)`，电影"Ghostbusters"和一个接受一个`String`值并返回另一个`String`值的闭包表达式。

为了找出已知为`Any`或`AnyObject`的指定类型的常量或变量，可以在`switch`语句分支中使用`is`或`as`模式。下面的例子表里`things`数组中的项，并用`switch`语句查询每个项的类型。一些`switch`语句分支将匹配的值绑定到指定类型的常量，以允许打印其值：
```swift
for thing in things {
    switch thing {
    case 0 as Int:
        print("zero as an Int")
    case 0 as Double:
        print("zero as a Double")
    case let someInt as Int:
        print("an integer value of \(someInt)")
    case let someDouble as Double where someDouble > 0:
        print("a positive double value of \(someDouble)")
    case is Double:
        print("some other double value that I don't want to print")
    case let someString as String:
        print("a string value of \"\(someString)\"")
    case let (x, y) as (Double, Double):
        print("an (x, y) point at \(x), \(y)")
    case let movie as Movie:
        print("a movie called \(movie.name), dir. \(movie.director)")
    case let stringConverter as (String) -> String:
        print(stringConverter("Michael"))
    default:
        print("something else")
    }
}

// zero as an Int
// zero as a Double
// an integer value of 42
// a positive double value of 3.14159
// a string value of "hello"
// an (x, y) point at 3.0, 5.0
// a movie called Ghostbusters, dir. Ivan Reitman
// Hello, Michael
```

> 注意：
> `Any`类型代表任何类型的值，包括可选类型。如果你在需要用`Any`类型的值的地方使用了一个可选值，Swift会给出警告。如果你确实需要把一个可选值用作`Any`值，可以用`as`操作符将可选值显示转换为`Any`，如下所示。
> ```
> let optionalNumber: Int? = 3
> things.append(optionalNumber) // Warning
> things.append(optionalNumber as Any) // No warning
> ```

[< 错误处理](Error_Handling.md) || [嵌套类型 >](Nested_Types.md)
