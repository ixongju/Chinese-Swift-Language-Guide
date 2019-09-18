## 版本兼容

本文档描述Swift5.1–––Xcode11包含的Swift的默认版本。你可以用Xcode11构建用Swift5.1，Swift4.2或Swift4编写的目标程序。

当你用Xcode11构建Swift4和Swift4.2代码时，大多数的Swift5.1功能是可用的。也就是说，下面的改变只对Swift5.1及以后版本可用：
* 返回不透明类型的函数需要Swift5.1运行时
* 对于已经返回可选的表达式，`try?`表达式不会引入额外级别的可选性
* 大数字字面量初始化的表达式会被推导为正确的整型类型。例如，`UInt64(0xffff_ffff_ffff_ffff)`被认为是正确的数值，而不是溢出

用Swift5.1编写的目标程序可以依赖一个用Swift4.2或Swift4编写的程序，反之亦然。这意味着，如果你有一个分散为对歌框架的工程，你可以逐个将代码从Swift4迁移到Swift5.1。

[< 关于](About_Swift.md) || [快速开始 >](A_Swift_Tour.md)
