---
title: swift学习笔记11：方法（Methods）
tags:
  - iOS
  - swift
  - 方法
id: 87
categories:
  - iOS
  - swift学习笔记
date: 2015-12-01 22:12:06
---

在Swift中，不仅能选择是否要定义一个类/结构体/枚举，还能灵活地在创建的类型（类/结构体/枚举）上定义方法。

实例方法 (Instance Methods)
实例方法是属于某个特定类、结构体或者枚举类型实例的方法。
定义一个简单的Counter类，对一个动作发生的次数进行计数：
<pre>
class Counter {
   var count = 0
   func increment() {
      ++count
   }
   func incrementBy(amount: Int) {
      count += amount
   }
   func reset() {
      count = 0
   }
}

// 用点语法（dot syntax）调用实例方法
let counter = Counter()
// 初始计数值是0
counter.increment()
// 计数值现在是1
counter.incrementBy(5)
// 计数值现在是6
counter.reset()
// 计数值现在是0
</pre>

方法的局部参数名称和外部参数名称 (Local and External Parameter Names for Methods)
函数参数可以同时有一个局部名称（在函数体内部使用）和一个外部名称（在调用函数时使用）。
定义了一个更复杂的incrementBy(_:)方法：
<pre>
class Counter {
   var count: Int = 0
   func incrementBy(amount: Int, numberOfTimes: Int) {
      count += amount * numberOfTimes
   }
}
</pre>
incrementBy(_:numverOfTimes:)方法有两个参数： amount和numberOfTimes。默认情况下，Swift只把amount当作一个局部名称，但是把numberOfTimes即看作局部名称又看作外部名称。下面调用这个方法：
<pre>
let counter = Counter()
counter.incrementBy(5, numberOfTimes: 3)
// counter 的值现在是 15
</pre>

修改方法的外部参数名称(Modifying External Parameter Name Behavior for Methods)
有时为方法的第一个参数提供一个外部参数名称是非常有用的，尽管这不是默认的行为。你自己可以为第一个参数添加一个显式的外部名称。如果不想为方法的第二个及后续的参数提供一个外部名称，可以通过使用下划线（_）作为该参数的显式外部名称，这样做将覆盖默认行为。

self 属性(The self Property)
类型的每一个实例都有一个隐含属性叫做self，self完全等同于该实例本身。可以在一个实例的实例方法中使用这个隐含的self属性来引用当前实例。
上面例子中的increment方法还可以这样写：
<pre>
func increment() {
   self.count++
}
</pre>
使用这条规则的主要场景是实例方法的某个参数名称与实例的某个属性名称相同的时候。在这种情况下，参数名称享有优先权，并且在引用属性时必须使用一种更严格的方式。这时你可以使用self属性来区分参数名称和属性名称。
下面的例子中，self消除方法参数x和实例属性x之间的歧义：
<pre>
struct Point {
   var x = 0.0, y = 0.0
   func isToTheRightOfX(x: Double) -> Bool {
      return self.x > x
   }
}
let somePoint = Point(x: 4.0, y: 5.0)
if somePoint.isToTheRightOfX(1.0) {
   print("This point is to the right of the line where x == 1.0")
}
// 打印输出: This point is to the right of the line where x == 1.0
</pre>

在实例方法中修改值类型(Modifying Value Types from Within Instance Methods)
结构体和枚举是值类型。一般情况下，值类型的属性不能在它的实例方法中被修改。
但是，如果确实需要在某个特定的方法中修改结构体或者枚举的属性，可以选择变异(mutating)这个方法，然后方法就可以从方法内部改变它的属性；并且它做的任何改变在方法结束时还会保留在原始结构中。方法还可以给它隐含的self属性赋值一个全新的实例，这个新实例在方法结束后将替换原来的实例。
要使用变异方法，将关键字mutating 放到方法的func关键字之前就可以了：
<pre>
struct Point {
   var x = 0.0, y = 0.0
   mutating func moveByX(deltaX: Double, y deltaY: Double) {
      x += deltaX
      y += deltaY
   }
}
var somePoint = Point(x: 1.0, y: 1.0)
somePoint.moveByX(2.0, y: 3.0)
print("The point is now at (\(somePoint.x), \(somePoint.y))")
// 打印输出: "The point is now at (3.0, 4.0)"
</pre>
注意，不能在结构体类型的常量上调用可变方法，因为其属性不能被改变，即使属性是变量属性：
<pre>
let fixedPoint = Point(x: 3.0, y: 3.0)
fixedPoint.moveByX(2.0, y: 3.0)
// 这里将会报告一个错误
</pre>

在可变方法中给 self 赋值(Assigning to self Within a Mutating Method)
可变方法能够赋给隐含属性self一个全新的实例。上面Point的例子可以用下面的方式改写：
<pre>
struct Point {
   var x = 0.0, y = 0.0
   mutating func moveByX(deltaX: Double, y deltaY: Double) {
      self = Point(x: x + deltaX, y: y + deltaY)
   }
}
</pre>
新版的可变方法moveByX(_:y:)创建了一个新的结构（它的 x 和 y 的值都被设定为目标值）。调用这个版本的方法和调用上个版本的最终结果是一样的。

枚举的可变方法可以把self设置为同一枚举类型中不同的成员：
<pre>
enum TriStateSwitch {
   case Off, Low, High
   mutating func next() {
      switch self {
         case Off:
            self = Low
         case Low:
            self = High
         case High:
            self = Off
      }
   }
}
var ovenLight = TriStateSwitch.Low
ovenLight.next()
// ovenLight 现在等于 .High
ovenLight.next()
// ovenLight 现在等于 .Off
</pre>

类型方法 (Type Methods)
实例方法是被类型的某个实例调用的方法。也可以定义类型本身调用的方法，这种方法就叫做类型方法。声明结构体和枚举的类型方法，在方法的func关键字之前加上关键字static。类可能会用关键字class来允许子类重写父类的方法实现。
在SomeClass类上调用类型方法的例子：
<pre>
class SomeClass {
   static func someTypeMethod() {
      // type method implementation goes here
   }
}
SomeClass.someTypeMethod()
</pre>
在类型方法的方法体中，self指向这个类型本身，而不是类型的某个实例。这意味着你可以用self来消除类型属性和类型方法参数之间的歧义（类似于我们在前面处理实例属性和实例方法参数时做的那样）。