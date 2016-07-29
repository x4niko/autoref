---
title: swift学习笔记9：类和结构体 (Classes and Structures)
tags:
  - iOS
  - swift
  - 类
  - 结构体
id: 82
categories:
  - iOS
  - swift学习笔记
date: 2015-11-28 10:49:54
---

类和结构体对比
共同处：
1、定义属性用于存储值
2、定义方法用于提供功能
3、定义附属脚本用于访问值
4、定义构造器用于生成初始化值
5、通过扩展以增加默认实现的功能
6、实现协议以提供某种标准功能

与结构体相比，类还有如下的附加功能：
1、继承允许一个类继承另一个类的特征
2、类型转换允许在运行时检查和解释一个类实例的类型
3、解构器允许一个类实例释放任何其所被分配的资源
4、引用计数允许对一个类的多次引用
注意：结构体总是通过被复制的方式在代码中传递，不使用引用计数。

定义语法
类和结构体有着类似的定义方式。我们通过关键字class和struct来分别表示类和结构体：
<pre>
class SomeClass {
   // class definition goes here
}
struct SomeStructure {
   // structure definition goes here
}
</pre>
注意：在每次定义一个新类或者结构体的时候，实际上是定义了一个新的Swift类型。因此使用UpperCamelCase这种方式来命名（如SomeClass和SomeStructure等），以便符合标准Swift类型的大写命名风格（如String，Int和Bool）。相反的，使用lowerCamelCase这种方式为属性和方法命名（如framerate和incrementCount），以便和类型名区分。
以下是定义结构体和定义类的示例：
<pre>
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
</pre>

类和结构体实例
生成结构体和类实例的语法非常相似：
<pre>
let someResolution = Resolution()
let someVideoMode = VideoMode()
</pre>

属性访问
通过使用点语法（dot syntax），访问实例的属性：
<pre>
print("The width of someResolution is \(someResolution.width)")
// 输出 "The width of someResolution is 0"
</pre>
也可以访问子属性，如VideoMode中Resolution属性的width属性：
<pre>
print("The width of someVideoMode is \(someVideoMode.resolution.width)")
// 输出 "The width of someVideoMode is 0"
</pre>
也可以使用点语法为变量属性赋值：
<pre>
// 允许直接设置结构体属性的子属性
someVideoMode.resolution.width = 1280
print("The width of someVideoMode is now \(someVideoMode.resolution.width)")
// 输出 "The width of someVideoMode is now 1280"
</pre>

结构体类型的成员逐一构造器（Memberwise Initializers for Structure Types）
所有结构体都有一个自动生成的成员逐一构造器，用于初始化新结构体实例中成员的属性。新实例中各个属性的初始值可以通过属性的名称传递到成员逐一构造器之中：
<pre>
let vga = Resolution(width:640, height: 480)
</pre>
与结构体不同，类实例没有默认的成员逐一构造器。

结构体和枚举是值类型
值类型被赋予给一个变量、常量或者被传递给一个函数的时候，其值会被拷贝。
实际上，在Swift中，所有的基本类型：整数（Integer）、浮点数（floating-point）、布尔值（Boolean）、字符串（string)、数组（array）和字典（dictionary），都是值类型，并且在底层都是以结构体的形式所实现。

在 Swift 中，所有的结构体和枚举类型都是值类型。这意味着它们的实例，以及实例中所包含的任何值类型属性，在代码中传递的时候都会被复制。
<pre>
let hd = Resolution(width: 1920, height: 1080)
var cinema = hd     // cinema的值其实是hd的一个拷贝副本，而不是hd本身
</pre>

枚举也遵循相同的行为准则：
<pre>
enum CompassPoint {
   case North, South, East, West
}
var currentDirection = CompassPoint.West
let rememberedDirection = currentDirection
currentDirection = .East
if rememberedDirection == .West {
   print("The remembered direction is still .West")
}
// 输出 "The remembered direction is still .West"
</pre>
上例中rememberedDirection被赋予了currentDirection的值，实际上它被赋予的是值的一个拷贝。赋值过程结束后再修改currentDirection的值并不影响rememberedDirection所储存的原始值的拷贝。

类是引用类型
与值类型不同，引用类型在被赋予到一个变量、常量或者被传递到一个函数时，其值不会被拷贝。
下面这个示例，使用了之前定义的VideoMode类：
<pre>
let tenEighty = VideoMode()
tenEighty.resolution = hd
tenEighty.interlaced = true
tenEighty.name = "1080i"
tenEighty.frameRate = 25.0

// tenEighty被赋予名为alsoTenEighty的新常量，同时对alsoTenEighty的帧率进行修改
let alsoTenEighty = tenEighty
alsoTenEighty.frameRate = 30.0

print("The frameRate property of tenEighty is now \(tenEighty.frameRate)")
// 输出 "The frameRate property of theEighty is now 30.0"
// 因为类是引用类型，所以tenEight和alsoTenEight实际上引用的是相同的VideoMode实例
</pre>
需要注意的是tenEighty和alsoTenEighty被声明为常量而不是变量。然而你依然可以改变tenEighty.frameRate和alsoTenEighty.frameRate，因为tenEighty和alsoTenEighty这两个常量的值并未改变。它们并不“存储”这个VideoMode实例，而仅仅是对VideoMode实例的引用。所以，改变的是被引用的VideoMode的frameRate属性，而不是引用VideoMode的常量的值。

恒等运算符
因为类是引用类型，有可能有多个常量和变量在幕后同时引用同一个类实例。
运用等价于（===）和不等价于（!==）这两个运算符检测两个常量或者变量是否引用同一个实例：
<pre>
if tenEighty === alsoTenEighty {
   print("tenEighty and alsoTenEighty refer to the same Resolution instance.")
}
//输出 "tenEighty and alsoTenEighty refer to the same Resolution instance."
</pre>
注意“等价于”（用三个等号表示，===）与“等于”（用两个等号表示，==）的不同：
1、“等价于”表示两个类类型（class type）的常量或者变量引用同一个类实例。
2、“等于”表示两个实例的值“相等”或“相同”。

类和结构体的选择
结构体实例总是通过值传递，类实例总是通过引用传递，这意味两者适用不同的任务。
按照通用的准则，当符合一条或多条以下条件时，考虑构建结构体：
1、该数据结构的主要目的是用来封装少量相关简单数据值。
2、有理由预计该数据结构的实例在被赋值或传递时，封装的数据将会被拷贝而不是被引用。
3、该数据结构中储存的值类型属性，也应该被拷贝，而不是被引用。
4、该数据结构不需要去继承另一个既有类型的属性或者行为。

字符串(String)、数组(Array)、和字典(Dictionary)类型的赋值与复制行为
Swift中，许多基本类型，诸如String，Array和Dictionary类型均以结构体的形式实现。这意味着被赋值给新的常量或变量，或者被传入函数或方法中时，它们的值会被拷贝。