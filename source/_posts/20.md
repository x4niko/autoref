---
title: swift学习笔记10：属性 (Properties)
tags:
  - iOS
  - swift
  - 属性
id: 85
categories:
  - iOS
  - swift学习笔记
date: 2015-11-29 16:07:38
---

存储属性
简单来说，一个存储属性就是存储在特定类或结构体的实例里的一个常量或变量。存储属性可以是变量存储属性（用关键字var定义），也可以是常量存储属性（用关键字let定义）。
下面的例子定义了一个名为FixedLengthRange的结构体，它描述了一个在创建后无法修改值域宽度的区间：
<pre>
struct FixedLengthRange {
   var firstValue: Int
   let length: Int
}
var rangeOfThreeItems = FixedLengthRange(firstValue: 0, length: 3)
// 该区间表示整数0，1，2
rangeOfThreeItems.firstValue = 6
// 该区间现在表示整数6，7，8
</pre>
FixedLengthRange的实例包含一个名为firstValue的变量存储属性和一个名为length的常量存储属性。在上面的例子中，length在创建实例的时候被初始化，因为它是一个常量存储属性，所以之后无法修改它的值。

常量结构体的存储属性
如果创建了一个结构体的实例并将其赋值给一个常量，则无法修改该实例的任何属性，即使定义了变量存储属性：
<pre>
let rangeOfFourItems = FixedLengthRange(firstValue: 0, length: 4)
// 该区间表示整数0，1，2，3
rangeOfFourItems.firstValue = 6
// 会报错。因为rangeOfFourItems被声明成了常量（用let关键字），即使firstValue是一个变量属性，也不能修改它。
</pre>
这是由于结构体属于值类型。当值类型的实例被声明为常量的时候，它的所有属性也就成了常量。属于引用类型的类则不一样。把一个引用类型的实例赋给一个常量后，仍然可以修改该实例的变量属性。

延迟存储属性
延迟存储属性是指当第一次被调用的时候才会计算其初始值的属性，在属性声明前使用lazy来标示一个延迟存储属性。
注意：必须将延迟存储属性声明成变量（使用var关键字），因为属性的初始值可能在实例构造完成之后才会得到。而常量属性在构造过程完成之前必须要有初始值，因此无法声明成延迟属性。

下面的例子使用了延迟存储属性来避免复杂类中不必要的初始化：
<pre>
class DataImporter {
   /*
   DataImporter 是一个负责将外部文件中的数据导入的类。
   这个类的初始化会消耗不少时间。
   */
   var fileName = "data.txt"
   // 这里会提供数据导入功能
}

class DataManager {
   lazy var importer = DataImporter()
   var data = [String]()
   // 这里会提供数据管理功能
}

let manager = DataManager()
manager.data.append("Some data")
manager.data.append("Some more data")
// DataImporter 实例的 importer 属性还没有被创建
</pre>
DataManager管理数据时也可能不从文件中导入数据。所以当DataManager的实例被创建时，没必要创建一个DataImporter的实例，更明智的做法是第一次用到DataImporter的时候才去创建它。
由于使用了lazy，importer属性只有在第一次被访问的时候才被创建。比如访问它的属性fileName时：
<pre>
print(manager.importer.fileName)
// DataImporter 实例的 importer 属性现在被创建了
// 输出 "data.txt”
</pre>
注意：如果一个被标记为lazy的属性在没有初始化时就同时被多个线程访问，则无法保证该属性只会被初始化一次。

计算属性
除存储属性外，类、结构体和枚举可以定义计算属性。计算属性不直接存储值，而是提供一个 getter 和一个可选的 setter，来间接获取和设置其他属性或变量的值。
<pre>
struct Point {
   var x = 0.0, y = 0.0
}

struct Size {
   var width = 0.0, height = 0.0
}

struct Rect {
   var origin = Point()
   var size = Size()
   var center: Point {
      get {
         let centerX = origin.x + (size.width / 2)
         let centerY = origin.y + (size.height / 2)
         return Point(x: centerX, y: centerY)
      }
      set(newCenter) {
         origin.x = newCenter.x - (size.width / 2)
         origin.y = newCenter.y - (size.height / 2)
      }
   }
}

var square = Rect(origin: Point(x: 0.0, y: 0.0),
size: Size(width: 10.0, height: 10.0))
let initialSquareCenter = square.center
square.center = Point(x: 15.0, y: 15.0)
print("square.origin is now at (\(square.origin.x), \(square.origin.y))")
// 输出 "square.origin is now at (10.0, 10.0)”
</pre>

便捷setter声明
如果计算属性的 setter 没有定义表示新值的参数名，则可以使用默认名称newValue。下面是使用了便捷setter声明的Rect结构体代码：
<pre>
struct AlternativeRect {
   var origin = Point()
   var size = Size()
   var center: Point {
      get {
         let centerX = origin.x + (size.width / 2)
         let centerY = origin.y + (size.height / 2)
         return Point(x: centerX, y: centerY)
      }
      set {
         origin.x = newValue.x - (size.width / 2)
         origin.y = newValue.y - (size.height / 2)
      }
   }
}
</pre>

只读计算属性
只有 getter 没有 setter 的计算属性就是只读计算属性。只读计算属性总是返回一个值，可以通过点运算符访问，但不能设置新的值。必须使用var关键字定义计算属性，包括只读计算属性，因为它们的值不是固定的。let关键字只用来声明常量属性，表示初始化后再也无法修改的值。
只读计算属性的声明可以去掉get关键字和花括号：
<pre>
struct Cuboid {
   var width = 0.0, height = 0.0, depth = 0.0
   var volume: Double {
      return width * height * depth
   }
}
let fourByFiveByTwo = Cuboid(width: 4.0, height: 5.0, depth: 2.0)
print("the volume of fourByFiveByTwo is \(fourByFiveByTwo.volume)")
// 输出 "the volume of fourByFiveByTwo is 40.0"
</pre>

属性观察器
属性观察器监控和响应属性值的变化，每次属性被设置值的时候都会调用属性观察器，甚至新值和当前值相同的时候也不例外。
可以为除了延迟存储属性之外的其他存储属性添加属性观察器，也可以通过重写属性的方式为继承的属性（包括存储属性和计算属性）添加属性观察器。
注意：不需要为非重写的计算属性添加属性观察器，因为可以通过它的 setter 直接监控和响应值的变化。
可以为属性添加如下的一个或全部观察器：
1、willSet在新的值被设置之前调用
2、didSet在新的值被设置之后立即调用
willSet观察器会将新的属性值作为常量参数传入，在willSet的实现代码中可以为这个参数指定一个名称，如果不指定则参数仍然可用，这时使用默认名称newValue表示。类似地，didSet观察器会将旧的属性值作为参数传入，可以为该参数命名或者使用默认参数名oldValue。
注意：父类的属性在子类的构造器中被赋值时，它在父类中的willSet和didSet观察器会被调用。

这里是一个willSet和didSet的实际例子，其中定义了一个名为StepCounter的类，用来统计一个人步行时的总步数。这个类可以跟计步器或其他日常锻炼的统计装置的输入数据配合使用。
<pre>
class StepCounter {
   var totalSteps: Int = 0 {
      willSet(newTotalSteps) {
         print("About to set totalSteps to \(newTotalSteps)")
      }
      didSet {
         if totalSteps > oldValue {
            print("Added \(totalSteps - oldValue) steps")
         }
      }
   }
}

let stepCounter = StepCounter()
stepCounter.totalSteps = 200
// About to set totalSteps to 200
// Added 200 steps
stepCounter.totalSteps = 360
// About to set totalSteps to 360
// Added 160 steps
stepCounter.totalSteps = 896
// About to set totalSteps to 896
// Added 536 steps
</pre>
如果在一个属性的didSet观察器里为它赋值，这个值会替换之前设置的值。

全局变量和局部变量
计算属性和属性观察器所描述的功能也可以用于全局变量和局部变量。全局变量是在函数、方法、闭包或任何类型之外定义的变量。局部变量是在函数、方法或闭包内部定义的变量。
全局的常量或变量都是延迟计算的，跟延迟存储属性相似，不同的地方在于，全局的常量或变量不需要标记lazy修饰符。
局部范围的常量或变量从不延迟计算。

类型属性
实例属性属于一个特定类型的实例，每创建一个实例，实例都拥有属于自己的一套属性值，实例之间的属性相互独立。
也可以为类型本身定义属性，无论创建了多少个该类型的实例，这些属性都只有唯一一份。这种属性就是类型属性。
类型属性用于定义某个类型所有实例共享的数据，比如所有实例都能用的一个常量（就像 C 语言中的静态常量），或者所有实例都能访问的一个变量（就像 C 语言中的静态变量）。
存储型类型属性可以是变量或常量，计算型类型属性跟实例的计算型属性一样只能定义成变量属性。
注意：跟实例的存储型属性不同，必须给存储型类型属性指定默认值，因为类型本身没有构造器，也就无法在初始化过程中使用构造器给类型属性赋值。
存储型类型属性是延迟初始化的，它们只有在第一次被访问的时候才会被初始化。即使它们被多个线程同时访问，系统也保证只会对其进行一次初始化，并且不需要对其使用lazy修饰符。

类型属性语法
在 Swift 中，类型属性是作为类型定义的一部分写在类型最外层的花括号内，因此它的作用范围也就在类型支持的范围内。
使用关键字static来定义类型属性。在为类定义计算型类型属性时，可以改用关键字class来支持子类对父类的实现进行重写。下面的例子演示了存储型和计算型类型属性的语法：
<pre>
struct SomeStructure {
   static var storedTypeProperty = "Some value."
   static var computedTypeProperty: Int {
      return 1
   }
}

enum SomeEnumeration {
   static var storedTypeProperty = "Some value."
   static var computedTypeProperty: Int {
      return 6
   }
}

class SomeClass {
   static var storedTypeProperty = "Some value."
   static var computedTypeProperty: Int {
      return 27
   }

   class var overrideableComputedTypeProperty: Int {
      return 107
   }
}

print(SomeStructure.storedTypeProperty)
// 输出 "Some value."
SomeStructure.storedTypeProperty = "Another value."
print(SomeStructure.storedTypeProperty)
// 输出 "Another value.”
print(SomeEnumeration.computedTypeProperty)
// 输出 "6"
print(SomeClass.computedTypeProperty)
// 输出 "27"
</pre>