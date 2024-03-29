[TOC]

## C++ 的意义

C++ 是一门多范式的通用编程语言

多范式，是因为 C++ 支持面向过程编程，也支持面向对象编程，也支持泛型编程，新版本还可以说是支持了函数式编程。同时，上面这些不同的范式，都可以在同一项目中组合使用，这就大大增加了开发的灵活性。因此，C++ 适用的领域非常广泛，小到嵌入式，大到分布式服务器，到处可以见到 C++ 的身影

C++ 的传统领域有被侵蚀的风险，那是因为和它相竞争的语言远远不止一个

- 如果专注性能和最小内存占用的话，C 仍然是首选——嵌入式领域用 C 非常多，而 Linux 也是用纯 C 写的。
- 如果专注抽象表达和可读性的话，那 Python 之类的脚本语言则要方便得多。
- 图形界面（GUI）编程传统上是 C++ 的地盘，但近年来 C# 和 JavaScript 占领了很大一部分市场。
- 游戏算是 C++ 的经典强项了，但有了 C++ 写的游戏引擎，游戏用 C# 写也没啥问题了——你可能不一定知道，Unity 游戏引擎上的首选开发语言是 C#，而王者荣耀是用什么游戏引擎呢？答案正是 Unity——所以王者荣耀可以认为是用 C# 开发的。
- 还有，Go 和 Rust 也加入了战团，对 C++ 形成了一定的竞争……

 C++ 的核心竞争力

- 抽象能力：意味着较高的开发效率，同时，更重要的是，不会因抽象而降低性能。
- 性能：这不用多说了，就是快并且占用资源少。
- 功耗：这是近年来我们越来越关注的问题，跟性能直接相关，性能好了功耗自然就低

即使主流移动平台的开发语言不是 C++——而是 Java 和 Objective-C 或 Swift——但任何性能要求高的应用，都几乎必然会用到 C++ 开发的组件

王者荣耀的客户端是用 Unity + C# 开发的，但我没有说王者荣耀的服务器端——那可还是用 C++ 开发的。另外，有一点我前面还藏着呢！虽然王者荣耀初期是纯用 Unity 开发的，没有用到 C++；但后来，腾讯又用 C++ 把游戏的逻辑部分独立成了一个 GameCore，进一步提高了性能 

目前，跟 C++ 定位差不多、能有直接竞争关系的，也就是既支持高度抽象、又追求高性能的通用编程语言，其实只有 Rust 一种。而 Rust 远没有达到跟 C++ 一样的成熟和普及程度。这也可以从 TIOBE 的排名看出来：C++ 是第 4 位，而 Rust 是第 25 位

## 什么时候该用 C++

C++ 既然性能又好，又支持抽象，为什么没有更流行呢？因为代价更高。C++ 是一种复杂的语言，难以上手和熟练掌握，因此也是一种比较容易出错、被误用的语言。C++ 一直与 C 基本保持了向后兼容性，这种兼容性，也一直是 C++ 的安全性和易用性方面的负担。C++ 比起 C 来，要更安全，更不容易出现缓冲区溢出这类漏洞，但跟没有指针概念的语言比起来，它仍然是一种“不安全”的语言

简言之，当你的软件属于运算密集或者内存密集型，你需要性能、且愿意为性能付出额外代价的时候，应该考虑用 C++，特别在你的代码需要部署在多台服务器或者移动设备的场合

此外，在嵌入式应用的场景，那就根本不是值不值、而是行不行的问题。如果程序完成一个功能不能在指定的若干毫秒、甚至微秒内完成，那产品根本是失败、不可用的。在这种场合，能和 C++ 竞争的只有 C，但 C 是一种开发效率更低、更需要堆人力的语言了。在嵌入式开发使用 C++ 的最大障碍可能不是技术，而是人力资源——搞嵌入式开发的程序员可能大多都习惯使用纯 C 了