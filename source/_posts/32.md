---
title: swift学习笔记22：协议（Protocols）
tags:
  - iOS
  - swift
  - 协议
id: 111
categories:
  - iOS
  - swift学习笔记
date: 2015-12-23 18:18:35
---

协议语法
协议的定义方式与类、结构体和枚举的定义非常相似：
<pre>
protocol SomeProtocol {
    // 这里是协议的定义部分
}
</pre>

要让自定义类型采纳某个协议，在定义类型时，需要在类型名称后加上协议名称，中间以冒号（:）分隔。采纳多个协议时，各协议之间用逗号（,）分隔：
<pre>
struct SomeStructure: FirstProtocol, AnotherProtocol {
    // 这里是结构体的定义部分
}
</pre>

拥有父类的类在采纳协议时，应该将父类名放在协议名之前，以逗号分隔：
<pre>
class SomeClass: SomeSuperClass, FirstProtocol, AnotherProtocol {
    // 这里是类的定义部分
}
</pre>

属性要求
协议可以要求采纳协议的类型提供特定名称和类型的实例属性或类型属性。协议不指定属性是存储型属性还是计算型属性，它只指定属性的名称和类型。此外，协议还指定属性是只读的还是可读可写的。
如果协议要求属性是可读可写的，那么该属性不能是常量属性或只读的计算型属性。如果协议只要求属性是只读的，那么该属性不仅可以是只读的，如果代码需要的话，还可以是可写的。
协议通常用 var 关键字来声明变量属性，在类型声明后加上 { set get } 来表示属性是可读可写的，只读属性则用 { get } 来表示：
<pre>
protocol SomeProtocol {
    var mustBeSettable: Int { get set }
    var doesNotNeedToBeSettable: Int { get }
}
</pre>

在协议中定义类型属性时，总是使用 static 关键字作为前缀。当类类型采纳协议时，除了 static 关键字，还可以使用 class 关键字来声明类型属性：
<pre>
protocol AnotherProtocol {
    static var someTypeProperty: Int { get set }
}
</pre>

如下所示，这是一个只含有一个实例属性要求的协议：
<pre>
protocol FullyNamed {
    var fullName: String { get }
}
</pre>
FullyNamed 协议除了要求采纳协议的类型提供 fullName 属性外，并没有其他特别的要求。这个协议表示，任何采纳 FullyNamed 的类型，都必须有一个只读的 String 类型的实例属性 fullName。
下面是一个采纳 FullyNamed 协议的简单结构体：
<pre>
struct Person: FullyNamed {
    var fullName: String
}
let john = Person(fullName: "John Appleseed")
// john.fullName 为 "John Appleseed"
</pre>
这个例子中定义了一个叫做 Person 的结构体，用来表示一个具有名字的人。从第一行代码可以看出，它采纳了 FullyNamed 协议。
Person 结构体的每一个实例都有一个 String 类型的存储型属性 fullName。这正好满足了 FullyNamed 协议的要求，也就意味着 Person 结构体正确地符合了协议。（如果协议要求未被完全满足，在编译时会报错。）

下面是一个更为复杂的类，它采纳并符合了 FullyNamed 协议：
<pre>
class Starship: FullyNamed {
    var prefix: String?
    var name: String
    init(name: String, prefix: String? = nil) {
        self.name = name
        self.prefix = prefix
    }
    var fullName: String {
        return (prefix != nil ? prefix! + " " : "") + name
    }
}
var ncc1701 = Starship(name: "Enterprise", prefix: "USS")
// ncc1701.fullName 是 "USS Enterprise"
</pre>
Starship 类把 fullName 属性实现为只读的计算型属性。每一个 Starship 类的实例都有一个名为 name 的非可选属性和一个名为 prefix 的可选属性。 当 prefix 存在时，计算型属性 fullName 会将 prefix 插入到 name 之前，从而为星际飞船构建一个全名。

方法要求
协议可以要求采纳协议的类型实现某些指定的实例方法或类方法。这些方法作为协议的一部分，像普通方法一样放在协议的定义中，但是不需要大括号和方法体。可以在协议中定义具有可变参数的方法，和普通方法的定义方式相同。但是，不支持为协议中的方法的参数提供默认值。
正如属性要求中所述，在协议中定义类方法的时候，总是使用 static 关键字作为前缀。当类类型采纳协议时，除了 static 关键字，还可以使用 class 关键字作为前缀：
<pre>
protocol SomeProtocol {
    static func someTypeMethod()
}
</pre>
下面的例子定义了一个只含有一个实例方法的协议：
<pre>
protocol RandomNumberGenerator {
    func random() -> Double
}
</pre>
RandomNumberGenerator 协议要求采纳协议的类型必须拥有一个名为 random， 返回值类型为 Double 的实例方法。尽管这里并未指明，但是我们假设返回值在 [0.0,1.0) 区间内。
RandomNumberGenerator 协议并不关心每一个随机数是怎样生成的，它只要求必须提供一个随机数生成器。

如下所示，下边是一个采纳了 RandomNumberGenerator 协议的类。该类实现了一个叫做 线性同余生成器（linear congruential generator） 的伪随机数算法。
<pre>
class LinearCongruentialGenerator: RandomNumberGenerator {
    var lastRandom = 42.0
    let m = 139968.0
    let a = 3877.0
    let c = 29573.0
    func random() -> Double {
        lastRandom = ((lastRandom * a + c) % m)
        return lastRandom / m
    }
}

let generator = LinearCongruentialGenerator()
print("Here's a random number: \(generator.random())")
// 打印 “Here's a random number: 0.37464991998171”
print("And another one: \(generator.random())")
// 打印 “And another one: 0.729023776863283”
</pre>

Mutating 方法要求
有时需要在方法中改变方法所属的实例。例如，在值类型（即结构体和枚举）的实例方法中，将 mutating 关键字作为方法的前缀，写在 func 关键字之前，表示可以在该方法中修改它所属的实例以及实例的任意属性的值。
如果在协议中定义了一个实例方法，该方法会改变采纳该协议的类型的实例，那么在定义协议时需要在方法前加 mutating 关键字。这使得结构体和枚举能够采纳此协议并满足此方法要求。
注意：实现协议中的 mutating 方法时，若是类类型，则不用写 mutating 关键字。而对于结构体和枚举，则必须写 mutating 关键字。
如下所示，Togglable 协议只要求实现一个名为 toggle 的实例方法。根据名称的暗示，toggle() 方法将改变实例属性，从而切换采纳该协议类型的实例的状态。
toggle() 方法在定义的时候，使用 mutating 关键字标记，这表明当它被调用时，该方法将会改变采纳协议的类型的实例：
<pre>
protocol Togglable {
    mutating func toggle()
}
</pre>
当使用枚举或结构体来实现 Togglable 协议时，需要提供一个带有 mutating 前缀的 toggle() 方法。
下面定义了一个名为 OnOffSwitch 的枚举。这个枚举在两种状态之间进行切换，用枚举成员 On 和 Off 表示。枚举的 toggle() 方法被标记为 mutating，以满足 Togglable 协议的要求：
<pre>
enum OnOffSwitch: Togglable {
    case Off, On
    mutating func toggle() {
        switch self {
        case Off:
            self = On
        case On:
            self = Off
        }
    }
}
var lightSwitch = OnOffSwitch.Off
lightSwitch.toggle()
// lightSwitch 现在的值为 .On
</pre>

构造器要求
协议可以要求采纳协议的类型实现指定的构造器。可以像编写普通构造器那样，在协议的定义里写下构造器的声明，但不需要写花括号和构造器的实体：
<pre>
protocol SomeProtocol {
    init(someParameter: Int)
}
</pre>

构造器要求在类中的实现
可以在采纳协议的类中实现构造器，无论是作为指定构造器，还是作为便利构造器。无论哪种情况，你都必须为构造器实现标上 required 修饰符：
<pre>
class SomeClass: SomeProtocol {
    required init(someParameter: Int) {
        // 这里是构造器的实现部分
    }
}
</pre>
使用 required 修饰符可以确保所有子类也必须提供此构造器实现，从而也能符合协议。

如果一个子类重写了父类的指定构造器，并且该构造器满足了某个协议的要求，那么该构造器的实现需要同时标注 required 和 override 修饰符：
<pre>
protocol SomeProtocol {
    init()
}

class SomeSuperClass {
    init() {
        // 这里是构造器的实现部分
    }
}

class SomeSubClass: SomeSuperClass, SomeProtocol {
    // 因为采纳协议，需要加上 required
    // 因为继承自父类，需要加上 override
    required override init() {
        // 这里是构造器的实现部分
    }
}
</pre>

可失败构造器要求
采纳协议的类型可以通过可失败构造器（init?）或非可失败构造器（init）来满足协议中定义的可失败构造器要求。协议中定义的非可失败构造器要求可以通过非可失败构造器（init）或隐式解包可失败构造器（init!）来满足。

协议作为类型
尽管协议本身并未实现任何功能，但是协议可以被当做一个成熟的类型来使用。
协议可以像其他普通类型一样使用，使用场景如下：
1、作为函数、方法或构造器中的参数类型或返回值类型
2、作为常量、变量或属性的类型
3、作为数组、字典或其他容器中的元素类型
注意：协议是一种类型，因此协议类型的名称应与其他类型（例如 Int，Double，String）的写法相同，使用大写字母开头的驼峰式写法，例如（FullyNamed 和 RandomNumberGenerator）。
下面是将协议作为类型使用的例子：
<pre>
class Dice {
    let sides: Int
    let generator: RandomNumberGenerator
    init(sides: Int, generator: RandomNumberGenerator) {
        self.sides = sides
        self.generator = generator
    }
    func roll() -> Int {
        return Int(generator.random() * Double(sides)) + 1
    }
}

// 使用LinearCongruentialGenerator的实例作为随机数生成器来创建一个六面骰子
var d6 = Dice(sides: 6, generator: LinearCongruentialGenerator())
for _ in 1...5 {
    print("Random dice roll is \(d6.roll())")
}
// Random dice roll is 3
// Random dice roll is 5
// Random dice roll is 4
// Random dice roll is 5
// Random dice roll is 4
</pre>

委托（代理）模式
委托是一种设计模式，它允许类或结构体将一些需要它们负责的功能委托给其他类型的实例。委托模式的实现很简单：定义协议来封装那些需要被委托的功能，这样就能确保采纳协议的类型能提供这些功能。委托模式可以用来响应特定的动作，或者接收外部数据源提供的数据，而无需关心外部数据源的类型。
下面的例子定义了两个基于骰子游戏的协议：
<pre>
protocol DiceGame {
    var dice: Dice { get }
    func play()
}

protocol DiceGameDelegate {
    func gameDidStart(game: DiceGame)
    func game(game: DiceGame, didStartNewTurnWithDiceRoll diceRoll:Int)
    func gameDidEnd(game: DiceGame)
}

// 使用 Dice 实例作为骰子，并且实现了 DiceGame 和 DiceGameDelegate 协议，后者用来记录游戏的过程
class SnakesAndLadders: DiceGame {
    let finalSquare = 25
    let dice = Dice(sides: 6, generator: LinearCongruentialGenerator())
    var square = 0
    var board: [Int]
    init() {
        board = [Int](count: finalSquare + 1, repeatedValue: 0)
        board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
        board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
    }
    var delegate: DiceGameDelegate?
    func play() {
        square = 0
        delegate?.gameDidStart(self)
        gameLoop: while square != finalSquare {
            let diceRoll = dice.roll()
            delegate?.game(self, didStartNewTurnWithDiceRoll: diceRoll)
            switch square + diceRoll {
            case finalSquare:
                break gameLoop
            case let newSquare where newSquare > finalSquare:
                continue gameLoop
            default:
                square += diceRoll
                square += board[square]
            }
        }
        delegate?.gameDidEnd(self)
    }
}
</pre>

如下示例定义了 DiceGameTracker 类，它采纳了 DiceGameDelegate 协议：
<pre>
class DiceGameTracker: DiceGameDelegate {
    var numberOfTurns = 0
    func gameDidStart(game: DiceGame) {
        numberOfTurns = 0
        if game is SnakesAndLadders {
            print("Started a new game of Snakes and Ladders")
        }
        print("The game is using a \(game.dice.sides)-sided dice")
    }
    func game(game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int) {
        ++numberOfTurns
        print("Rolled a \(diceRoll)")
    }
    func gameDidEnd(game: DiceGame) {
        print("The game lasted for \(numberOfTurns) turns")
    }
}

let tracker = DiceGameTracker()
let game = SnakesAndLadders()
game.delegate = tracker
game.play()
// Started a new game of Snakes and Ladders
// The game is using a 6-sided dice
// Rolled a 3
// Rolled a 5
// Rolled a 4
// Rolled a 5
// The game lasted for 4 turns
</pre>

通过扩展添加协议一致性
即便无法修改源代码，依然可以通过扩展令已有类型采纳并符合协议。扩展可以为已有类型添加属性、方法、下标脚本以及构造器，因此可以符合协议中的相应要求。
注意：通过扩展令已有类型采纳并符合协议时，该类型的所有实例也会随之获得协议中定义的各项功能。
例如下面这个 TextRepresentable 协议，任何想要通过文本表示一些内容的类型都可以实现该协议。这些想要表示的内容可以是实例本身的描述，也可以是实例当前状态的文本描述：
<pre>
protocol TextRepresentable {
    var textualDescription: String { get }
}

// 通过扩展，令先前提到的 Dice 类采纳并符合 TextRepresentable 协议
extension Dice: TextRepresentable {
    var textualDescription: String {
        return "A \(sides)-sided dice"
    }
}

// 现在所有 Dice 的实例都可以看做 TextRepresentable 类型
let d12 = Dice(sides: 12,generator: LinearCongruentialGenerator())
print(d12\. textualDescription)
// 打印 “A 12-sided dice”

// SnakesAndLadders 类也可以通过扩展采纳并符合 TextRepresentable 协议
extension SnakesAndLadders: TextRepresentable {
    var textualDescription: String {
        return "A game of Snakes and Ladders with \(finalSquare) squares"
    }
}
print(game.textualDescription)
// 打印 “A game of Snakes and Ladders with 25 squares”
</pre>

通过扩展声明已采纳协议
当一个类型已经符合了某个协议中的所有要求，却还没有声明采纳该协议时，可以通过空扩展体的扩展来采纳该协议：
<pre>
struct Hamster {
    var name: String
    var textualDescription: String {
        return "A hamster named \(name)"
    }
}
extension Hamster: TextRepresentable {}

// 现在Hamster的实例可以作为 TextRepresentable 类型使用
let simonTheHamster = Hamster(name: "Simon")
let somethingTextRepresentable: TextRepresentable = simonTheHamster
print(somethingTextRepresentable.textualDescription)
// 打印 “A hamster named Simon”
</pre>
注意：即使满足了协议的所有要求，类型也不会自动采纳协议，必须显式地采纳协议。

协议类型的集合
协议类型可以在数组或者字典这样的集合中使用，在协议类型提到了这样的用法。下面的例子创建了一个元素类型为 TextRepresentable 的数组：
<pre>
let things: [TextRepresentable] = [game, d12, simonTheHamster]

// 遍历 things 数组，并打印每个元素的文本表示
for thing in things {
    print(thing.textualDescription)
}
// A game of Snakes and Ladders with 25 squares
// A 12-sided dice
// A hamster named Simon
</pre>

协议的继承
协议能够继承一个或多个其他协议，可以在继承的协议的基础上增加新的要求。协议的继承语法与类的继承相似，多个被继承的协议间用逗号分隔：
<pre>
protocol InheritingProtocol: SomeProtocol, AnotherProtocol {
    // 这里是协议的定义部分
}
</pre>

如下所示，PrettyTextRepresentable 协议继承了 TextRepresentable 协议：
<pre>
protocol PrettyTextRepresentable: TextRepresentable {
    var prettyTextualDescription: String { get }
}
</pre>

如下所示，扩展 SnakesAndLadders，使其采纳并符合 PrettyTextRepresentable 协议：
<pre>
extension SnakesAndLadders: PrettyTextRepresentable {
    var prettyTextualDescription: String {
        var output = textualDescription + ":\n"
        for index in 1...finalSquare {
            switch board[index] {
            case let ladder where ladder > 0:
                output += "▲ "
            case let snake where snake < 0:
                output += "▼ "
            default:
                output += "○ "
            }
        }
        return output
    }
}

print(game.prettyTextualDescription)
// A game of Snakes and Ladders with 25 squares:
// ○ ○ ▲ ○ ○ ▲ ○ ○ ▲ ▲ ○ ○ ○ ▼ ○ ○ ○ ○ ▼ ○ ○ ▼ ○ ▼ ○
</pre>

类类型专属协议
可以在协议的继承列表中，通过添加 class 关键字来限制协议只能被类类型采纳，而结构体或枚举不能采纳该协议。class 关键字必须第一个出现在协议的继承列表中，在其他继承的协议之前：
<pre>
protocol SomeClassOnlyProtocol: class, SomeInheritedProtocol {
    // 这里是类类型专属协议的定义部分
}
</pre>

协议合成
有时候需要同时采纳多个协议，你可以将多个协议采用 protocol<SomeProtocol, AnotherProtocol> 这样的格式进行组合，称为协议合成（protocol composition）。你可以在 <> 中罗列任意多个你想要采纳的协议，以逗号分隔。
下面的例子中，将 Named 和 Aged 两个协议按照上述语法组合成一个协议，作为函数参数的类型：
<pre>
protocol Named {
    var name: String { get }
}
protocol Aged {
    var age: Int { get }
}
struct Person: Named, Aged {
    var name: String
    var age: Int
}
func wishHappyBirthday(celebrator: protocol<Named, Aged>) {
    print("Happy birthday \(celebrator.name) - you're \(celebrator.age)!")
}
let birthdayPerson = Person(name: "Malcolm", age: 21)
wishHappyBirthday(birthdayPerson)
// 打印 “Happy birthday Malcolm - you're 21!”
</pre>
注意：协议合成并不会生成新的、永久的协议类型，而是将多个协议中的要求合成到一个只在局部作用域有效的临时协议中。

检查协议一致性
你可以使用类型转换中描述的 is 和 as 操作符来检查协议一致性，即是否符合某协议，并且可以转换到指定的协议类型。检查和转换到某个协议类型在语法上和类型的检查和转换完全相同：
1、is 用来检查实例是否符合某个协议，若符合则返回 true，否则返回 false。
2、as? 返回一个可选值，当实例符合某个协议时，返回类型为协议类型的可选值，否则返回 nil。
3、as! 将实例强制向下转换到某个协议类型，如果强转失败，会引发运行时错误。
下面的例子定义了一个 HasArea 协议，该协议定义了一个 Double 类型的只读属性 area：
<pre>
protocol HasArea {
    var area: Double { get }
}

// Circle 类和 Country 类都采纳了 HasArea 协议
class Circle: HasArea {
    let pi = 3.1415927
    var radius: Double
    var area: Double { return pi * radius * radius }
    init(radius: Double) { self.radius = radius }
}
class Country: HasArea {
    var area: Double
    init(area: Double) { self.area = area }
}

// Animal 是一个未采纳 HasArea 协议的类
class Animal {
    var legs: Int
    init(legs: Int) { self.legs = legs }
}
</pre>
Circle，Country，Animal 并没有一个共同的基类，尽管如此，它们都是类，它们的实例都可以作为 AnyObject 类型的值，存储在同一个数组中：
<pre>
let objects: [AnyObject] = [
    Circle(radius: 2.0),
    Country(area: 243_610),
    Animal(legs: 4)
]

// 对迭代出的每一个元素进行检查，看它是否符合 HasArea 协议
for object in objects {
    if let objectWithArea = object as? HasArea {
        print("Area is \(objectWithArea.area)")
    } else {
        print("Something that doesn't have an area")
    }
}
// Area is 12.5663708
// Area is 243610.0
// Something that doesn't have an area
</pre>

可选的协议要求
协议可以定义可选要求，采纳协议的类型可以选择是否实现这些要求。在协议中使用 optional 关键字作为前缀来定义可选要求。使用可选要求时（例如，可选的方法或者属性），它们的类型会自动变成可选的。比如，一个类型为 (Int) -> String 的方法会变成 ((Int) -> String)?。需要注意的是整个函数类型是可选的，而不是函数的返回值。
注意：可选的协议要求只能用在标记 @objc 特性的协议中。
该特性表示协议将暴露给 Objective-C 代码，即使不打算和 Objective-C 有什么交互，如果想要指定可选的协议要求，那么还是要为协议加上 @obj 特性。
还需要注意的是，标记 @objc 特性的协议只能被继承自 Objective-C 类的类或者 @objc 类采纳，其他类以及结构体和枚举均不能采纳这种协议。
下面的例子定义了一个名为 Counter 的用于整数计数的类，它使用外部的数据源来提供每次的增量。数据源由 CounterDataSource 协议定义，包含两个可选要求：
<pre>
@objc protocol CounterDataSource {
    optional func incrementForCount(count: Int) -> Int
    optional var fixedIncrement: Int { get }
}
</pre>
注意：严格来讲，CounterDataSource 协议中的方法和属性都是可选的，因此采纳协议的类可以不实现这些要求，尽管技术上允许这样做，不过最好不要这样写。
Counter 类含有 CounterDataSource? 类型的可选属性 dataSource，如下所示：
<pre>
class Counter {
    var count = 0
    var dataSource: CounterDataSource?
    func increment() {
        if let amount = dataSource?.incrementForCount?(count) {
            count += amount
        } else if let amount = dataSource?.fixedIncrement {
            count += amount
        }
    }
}
</pre>
这里使用了两层可选链式调用。首先，由于 dataSource 可能为 nil，因此在 dataSource 后边加上了 ?，以此表明只在 dataSource 非空时才去调用 incrementForCount(_:) 方法。其次，即使 dataSource 存在，也无法保证其是否实现了 incrementForCount(_:) 方法，因为这个方法是可选的。因此，incrementForCount(_:) 方法同样使用可选链式调用进行调用，只有在该方法被实现的情况下才能调用它，所以在 incrementForCount(_:) 方法后边也加上了 ?。

调用 incrementForCount(_:) 方法在上述两种情形下都有可能失败，所以返回值为 Int? 类型。虽然在 CounterDataSource 协议中，incrementForCount(_:) 的返回值类型是非可选 Int。另外，即使这里使用了两层可选链式调用，最后的返回结果依旧是单层的可选类型，即 Int? 而不是 Int??。关于这一点的更多信息，请查阅连接多层可选链式调用

在调用 incrementForCount(_:) 方法后，Int? 型的返回值通过可选绑定解包并赋值给常量 amount。如果可选值确实包含一个数值，也就是说，数据源和方法都存在，数据源方法返回了一个有效值。之后便将解包后的 amount 加到 count 上，增量操作完成。

如果没有从 incrementForCount(_:) 方法获取到值，可能由于 dataSource 为 nil，或者它并没有实现 incrementForCount(_:) 方法，那么 increment() 方法将试图从数据源的 fixedIncrement 属性中获取增量。fixedIncrement 是一个可选属性，因此属性值是一个 Int? 值，即使该属性在 CounterDataSource 协议中的类型是非可选的 Int。

下面的例子展示了 CounterDataSource 的简单实现。ThreeSource 类采纳了 CounterDataSource 协议，它实现了可选属性 fixedIncrement，每次会返回 3：
<pre>
class ThreeSource: NSObject, CounterDataSource {
    let fixedIncrement = 3
}

//可以使用 ThreeSource 的实例作为 Counter 实例的数据源：
var counter = Counter()
counter.dataSource = ThreeSource()
for _ in 1...4 {
    counter.increment()
    print(counter.count)
}
// 3
// 6
// 9
// 12
</pre>

下面是一个更为复杂的数据源 TowardsZeroSource，它将使得最后的值变为 0：
<pre>
@objc class TowardsZeroSource: NSObject, CounterDataSource {
    func incrementForCount(count: Int) -> Int {
        if count == 0 {
            return 0
        } else if count < 0 {
            return 1
        } else {
            return -1
        }
    }
}

// 使用 TowardsZeroSource 实例将 Counter 实例来从 -4 增加到 0。一旦增加到 0，数值便不会再有变动
counter.count = -4
counter.dataSource = TowardsZeroSource()
for _ in 1...5 {
    counter.increment()
    print(counter.count)
}
// -3
// -2
// -1
// 0
// 0
</pre>

协议扩展
协议可以通过扩展来为采纳协议的类型提供属性、方法以及下标脚本的实现。通过这种方式，你可以基于协议本身来实现这些功能，而无需在每个采纳协议的类型中都重复同样的实现，也无需使用全局函数。
例如，可以扩展 RandomNumberGenerator 协议来提供 randomBool() 方法。该方法使用协议中定义的 random() 方法来返回一个随机的 Bool 值：
<pre>
extension RandomNumberGenerator {
    func randomBool() -> Bool {
        return random() > 0.5
    }
}

// 通过协议扩展，所有采纳协议的类型，都能自动获得这个扩展所增加的方法实现，无需任何额外修改
let generator = LinearCongruentialGenerator()
print("Here's a random number: \(generator.random())")
// 打印 “Here's a random number: 0.37464991998171”
print("And here's a random Boolean: \(generator.randomBool())")
// 打印 “And here's a random Boolean: true”
</pre>

提供默认实现
可以通过协议扩展来为协议要求的属性、方法以及下标脚本提供默认的实现。如果采纳协议的类型为这些要求提供了自己的实现，那么这些自定义实现将会替代扩展中的默认实现被使用。
注意：通过协议扩展为协议要求提供的默认实现和可选的协议要求不同。虽然在这两种情况下，采纳协议的类型都无需自己实现这些要求，但是通过扩展提供的默认实现可以直接调用，而无需使用可选链式调用。
例如，PrettyTextRepresentable 协议继承自 TextRepresentable 协议，可以为其提供一个默认的 prettyTextualDescription 属性，只是简单地返回 textualDescription 属性的值：
<pre>
extension PrettyTextRepresentable  {
    var prettyTextualDescription: String {
        return textualDescription
    }
}
</pre>

为协议扩展添加限制条件
在扩展协议的时候，可以指定一些限制条件，只有采纳协议的类型满足这些限制条件时，才能获得协议扩展提供的默认实现。这些限制条件写在协议名之后，使用 where 子句来描述，正如Where子句)中所描述的。
例如，可以扩展 CollectionType 协议，但是只适用于集合中的元素采纳了 TextRepresentable 协议的情况：
<pre>
extension CollectionType where Generator.Element: TextRepresentable {
    var textualDescription: String {
        let itemsAsText = self.map { $0.textualDescription }
        return "[" + itemsAsText.joinWithSeparator(", ") + "]"
    }
}
// textualDescription 属性返回整个集合的文本描述，它将集合中的每个元素的文本描述以逗号分隔的方式连接起来，包在一对方括号中。

let murrayTheHamster = Hamster(name: "Murray")
let morganTheHamster = Hamster(name: "Morgan")
let mauriceTheHamster = Hamster(name: "Maurice")
let hamsters = [murrayTheHamster, morganTheHamster, mauriceTheHamster]
// 因为 Array 符合 CollectionType 协议，而数组中的元素又符合 TextRepresentable 协议，所以数组可以使用 textualDescription 属性得到数组内容的文本表示：
print(hamsters.textualDescription)
// 打印 “[A hamster named Murray, A hamster named Morgan, A hamster named Maurice]”
</pre>
注意：如果多个协议扩展都为同一个协议要求提供了默认实现，而采纳协议的类型又同时满足这些协议扩展的限制条件，那么将会使用限制条件最多的那个协议扩展提供的默认实现。