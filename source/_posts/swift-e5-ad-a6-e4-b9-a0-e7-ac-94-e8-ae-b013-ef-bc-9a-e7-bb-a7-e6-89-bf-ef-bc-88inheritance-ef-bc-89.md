---
title: swift学习笔记13：继承（Inheritance）
tags:
  - iOS
  - swift
  - 继承
id: 91
categories:
  - iOS
  - swift学习笔记
date: 2015-12-05 11:53:24
---

一个类可以继承（inherit）另一个类的方法（methods），属性（properties）和其它特性。当一个类继承其它类时，继承类叫子类（subclass），被继承类叫超类（或父类，superclass）。在Swift中，继承是区分「类」与其它类型的一个基本特征。

定义一个基类（Base class）
不继承于其它类的类，称之为基类（base calss）。Swift中的类并不是从一个通用的基类继承而来。如果不为定义的类指定一个超类的话，这个类就自动成为基类。
<pre>
class Vehicle {
    var currentSpeed = 0.0
    var description: String {
        return "traveling at \(currentSpeed) miles per hour"
    }
    func makeNoise() {
        // 什么也不做
    }
}

let someVehicle = Vehicle()
print("Vehicle: \(someVehicle.description)")
// Vehicle: traveling at 0.0 miles per hour
</pre>

子类生成（Subclassing）
子类生成（Subclassing）指的是在一个已有类的基础上创建一个新的类。子类继承超类的特性，并且可以进一步完善，还可以为子类添加新的特性。
为了指明某个类的超类，将超类名写在子类名的后面，用冒号分隔：
<pre>
// 定义一个叫Bicycle的子类，继承成父类Vehicle，新的Bicycle类自动获得Vehicle类的所有特性
class Bicycle: Vehicle {
    var hasBasket = false
}

// 为特定的Bicycle实例设置hasBasket属性为ture
let bicycle = Bicycle()
bicycle.hasBasket = true

// 修改Bicycle实例所继承的currentSpeed属性，和查询实例所继承的description属性
bicycle.currentSpeed = 15.0
print("Bicycle: \(bicycle.description)")
// Bicycle: traveling at 15.0 miles per hour
</pre>

子类还可以继续被其它类继承，下面的示例为Bicycle创建了一个名为Tandem（双人自行车）的子类：
<pre>
class Tandem: Bicycle {
    var currentNumberOfPassengers = 0
}

// 创建一个Tandem的实例，可以使用它所有的新属性和继承的属性，还能查询从Vehicle继承来的只读属性description
let tandem = Tandem()
tandem.hasBasket = true
tandem.currentNumberOfPassengers = 2
tandem.currentSpeed = 22.0
print("Tandem: \(tandem.description)")
// Tandem: traveling at 22.0 miles per hour
</pre>

重写（Overriding）
子类可以为继承来的实例方法（instance method），类方法（class method），实例属性（instance property），或下标脚本（subscript）提供自己定制的实现（implementation）。这种行为叫重写（overriding）。
如果要重写某个特性，需要在重写定义的前面加上override关键字，任何缺少override关键字的重写都会在编译时被诊断为错误。
override关键字会提醒 Swift 编译器去检查该类的超类（或其中一个父类）是否有匹配重写版本的声明。这个检查可以确保重写定义是正确的。

定义Vehicle的一个新的子类Train，它重写了从Vehicle类继承来的makeNoise()方法：
<pre>
class Train: Vehicle {
    override func makeNoise() {
        print("Choo Choo")
    }
}

let train = Train()
train.makeNoise()
// 打印 "Choo Choo"
</pre>

重写属性的 Getters 和 Setters
可以提供定制的 getter（或 setter）来重写任意继承来的属性，无论继承来的属性是存储型的还是计算型的属性。子类并不知道继承来的属性是存储型的还是计算型的，它只知道继承来的属性会有一个名字和类型。在重写一个属性时，必需将它的名字和类型都写出来。这样才能使编译器去检查重写的属性是与超类中同名同类型的属性相匹配的。
可以将一个继承来的只读属性重写为一个读写属性，只需要在重写版本的属性里提供 getter 和 setter。但是不可以将一个继承来的读写属性重写为一个只读属性。
如果在重写属性中提供了setter，那么也一定要提供 getter。如果不想在重写版本中的 getter 里修改继承来的属性值，可以直接通过super.someProperty来返回继承来的值，其中someProperty是要重写的属性的名字。
<pre>
class Car: Vehicle {
    var gear = 1
    override var description: String {
        return super.description + " in gear \(gear)"
    }
}

let car = Car()
car.currentSpeed = 25.0
car.gear = 3
print("Car: \(car.description)")
// Car: traveling at 25.0 miles per hour in gear 3
</pre>

重写属性观察器（Property Observer）
不可以为继承来的常量存储型属性或继承来的只读计算型属性添加属性观察器。这些属性的值是不可以被设置的，所以，为它们提供willSet或didSet实现是不恰当。
此外还要注意，不可以同时提供重写的setter和重写的属性观察器。如果想观察属性值的变化，并且已经为那个属性提供了定制的 setter，那么在setter中就可以观察到任何值变化了。
定义一个新类叫AutomaticCar，它是Car的子类。AutomaticCar表示自动挡汽车，它可以根据当前的速度自动选择合适的挡位:
<pre>
class AutomaticCar: Car {
    override var currentSpeed: Double {
        didSet {
            gear = Int(currentSpeed / 10.0) + 1
        }
    }
}

let automatic = AutomaticCar()
automatic.currentSpeed = 35.0
print("AutomaticCar: \(automatic.description)")
// AutomaticCar: traveling at 35.0 miles per hour in gear 4
</pre>

防止重写
可以通过把方法，属性或下标脚本标记为final来防止它们被重写，只需要在声明关键字前加上final修饰符即可（例如：final var，final func，final class func，以及final subscript）。
如果重写了final方法，属性或下标脚本，在编译时会报错。在类扩展中的方法，属性或下标脚本也可以在扩展的定义里标记为 final。
可以通过在关键字class前添加final修饰符（final class）来将整个类标记为 final 的。这样的类是不可被继承的，试图继承这样的类会导致编译报错。