---
title: swift学习笔记23：泛型（Generics）
tags:
  - iOS
  - swift
  - 泛型
id: 113
categories:
  - iOS
  - swift学习笔记
date: 2015-12-25 09:19:17
---

泛型代码可以让你写出根据自我需求定义、适用于任何类型的，灵活且可重用的函数和类型。

泛型所解决的问题
这里是一个标准的，非泛型函数swapTwoInts,用来交换两个Int值：
<pre>
func swapTwoInts(inout a: Int, inout _ b: Int) {
  let temporaryA = a
  a = b
  b = temporaryA
}

// 调用函数交换两个Int变量值
var someInt = 3
var anotherInt = 107
swapTwoInts(&someInt, &anotherInt)
print("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
// 输出 "someInt is now 107, and anotherInt is now 3"
</pre>

swapTwoInts(_:_:)函数是非常有用的，但是它只能交换Int值，如果想要交换两个String或者Double，就不得不写更多的函数，如 swapTwoStrings和swapTwoDoubles(_:_:)，如同如下所示：
<pre>
func swapTwoStrings(inout a: String, inout _ b: String) {
    let temporaryA = a
    a = b
    b = temporaryA
}

func swapTwoDoubles(inout a: Double, inout _ b: Double) {
    let temporaryA = a
    a = b
    b = temporaryA
}
</pre>

泛型函数
泛型函数可以工作于任何类型，这里是一个上面swapTwoInts(_:_:)函数的泛型版本，用于交换两个值：
<pre>
func swapTwoValues<T>(inout a: T, inout _ b: T) {
    let temporaryA = a
    a = b
    b = temporaryA
}
</pre>
这个函数的泛型版本使用了占位类型名字（通常此情况下用字母T来表示）来代替实际类型名（如Int、String或Double）。占位类型名没有提示T必须是什么类型，但是它提示了a和b必须是同一类型T，而不管T表示什么类型。只有swapTwoValues(_:_:)函数在每次调用时所传入的实际类型才能决定T所代表的类型。
另外一个不同之处在于这个泛型函数名后面跟着的占位类型名字（T）是用尖括号括起来的（<T>）。这个尖括号告诉 Swift 那个T是swapTwoValues(_:_:)函数所定义的一个类型。因为T是一个占位命名类型，Swift 不会去查找命名为T的实际类型。
在下面的两个例子中,T分别代表Int和String：
<pre>
var someInt = 3
var anotherInt = 107
swapTwoValues(&someInt, &anotherInt)
// someInt 现在等于 107, anotherInt 现在等于 3

var someString = "hello"
var anotherString = "world"
swapTwoValues(&someString, &anotherString)
// someString 现在等于 "world", anotherString 现在等于 "hello"
</pre>

类型参数
在上面的swapTwoValues例子中，占位类型T是一种类型参数的示例。类型参数指定并命名为一个占位类型，并且紧随在函数名后面，使用一对尖括号括起来（如<T>），可支持多个类型参数，命名在尖括号中，用逗号分开。

命名类型参数
在简单的情况下，泛型函数或泛型类型需要指定一个占位类型（如上面的swapTwoValues泛型函数，或一个存储单一类型的泛型集，如数组），通常用一单个字母T来命名类型参数。不过，可以使用任何有效的标识符来作为类型参数名。
注意：始终使用大写字母开头的驼峰式命名法（例如T和Key）来给类型参数命名，以表明它们是类型的占位符，而非类型值。

泛型类型
这部分展示如何写一个泛型集类型--Stack（栈）。一个栈是一系列值域的集合，和Array（数组）类似，但其是一个比 Swift 的Array类型更多限制的集合。一个数组可以允许其里面任何位置的插入/删除操作，而栈，只允许在集合的末端添加新的项（如同push一个新值进栈）。同样的一个栈也只能从末端移除项（如同pop一个值出栈）。
这里展示了如何写一个非泛型版本的栈，Int值型的栈：
<pre>
struct IntStack {
    var items = [Int]()
    mutating func push(item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
}
</pre>
这里是一个相同代码的泛型版本：
<pre>
struct Stack<T> {
    var items = [T]()
    mutating func push(item: T) {
        items.append(item)
    }
    mutating func pop() -> T {
        return items.removeLast()
    }
}
</pre>
由于Stack是泛型类型，所以在 Swift 中其可以用来创建任何有效类型的栈，这种方式如同Array和Dictionary。可以通过在尖括号里写出栈中需要存储的数据类型来创建并初始化一个Stack实例。比如，要创建一个strings的栈，可以写成Stack<String>()：
<pre>
var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tres")
stackOfStrings.push("cuatro")
// 现在栈已经有4个string了

// 从栈中pop并移除值"cuatro"：
let fromTheTop = stackOfStrings.pop()
// fromTheTop 等于 "cuatro", 现在栈中还有3个string
</pre>

扩展一个泛型类型
当扩展一个泛型类型的时候，并不需要在扩展的定义中提供类型参数列表。更加方便的是，原始类型定义中声明的类型参数列表在扩展里是可以使用的，并且这些来自原始类型中的参数名称会被用作原始定义中类型参数的引用。

下面的例子扩展了泛型Stack类型，为其添加了一个名为topItem的只读计算属性，它将会返回当前栈顶端的元素而不会将其从栈中移除。
<pre>
extension Stack {
    var topItem: T? {
        return items.isEmpty ? nil : items[items.count - 1]
    }
}
</pre>
topItem属性会返回一个T类型的可选值。当栈为空的时候，topItem将会返回nil；当栈不为空的时候，topItem会返回items数组中的最后一个元素。注意这里的扩展并没有定义一个类型参数列表。相反的，Stack类型已有的类型参数名称，T被用在扩展中当做topItem计算属性的可选类型。
topItem计算属性现在可以被用来返回任意Stack实例的顶端元素而无需移除它：
<pre>
if let topItem = stackOfStrings.topItem {
    print("The top item on the stack is \(topItem).")
}
// 输出 "The top item on the stack is tres."
</pre>

类型约束
swapTwoValues(_:_:)函数和Stack类型可以作用于任何类型，不过，有的时候对使用在泛型函数和泛型类型上的类型强制约束为某种特定类型是非常有用的。类型约束指定了一个必须继承自指定类的类型参数，或者遵循一个特定的协议或协议构成。

例如，Swift 的Dictionary类型对作用于其键的类型做了些限制。在字典的描述中，字典的键类型必须是可哈希，也就是说，必须有一种方法可以使其被唯一的表示。Dictionary之所以需要其键是可哈希是为了以便于其检查其是否已经包含某个特定键的值。如无此需求，Dictionary既不会告诉是否插入或者替换了某个特定键的值，也不能查找到已经存储在字典里面的给定键值。

这个需求强制加上一个类型约束作用于Dictionary的键上，当然其键类型必须遵循Hashable协议（Swift 标准库中定义的一个特定协议）。所有的 Swift 基本类型（如String，Int， Double和 Bool）默认都是可哈希。

类型约束语法

你可以写一个在一个类型参数名后面的类型约束，通过冒号分割，来作为类型参数链的一部分。这种作用于泛型函数的类型约束的基础语法如下所示（和泛型类型的语法相同）：
<pre>
func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {
    // 这里是函数主体
}
</pre>

上面这个假定函数有两个类型参数。第一个类型参数T，有一个需要T必须是SomeClass子类的类型约束；第二个类型参数U，有一个需要U必须遵循SomeProtocol协议的类型约束。

类型约束实例

这里有个名为findStringIndex的非泛型函数，该函数功能是去查找包含一给定String值的数组。若查找到匹配的字符串，findStringIndex(_:_:)函数返回该字符串在数组中的索引值（Int），反之则返回nil：
<pre>
func findStringIndex(array: [String], _ valueToFind: String) -> Int? {
    for (index, value) in array.enumerate() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}
</pre>

findStringIndex(_:_:)函数可以作用于查找一字符串数组中的某个字符串:
<pre>
let strings = ["cat", "dog", "llama", "parakeet", "terrapin"]
if let foundIndex = findStringIndex(strings, "llama") {
    print("The index of llama is \(foundIndex)")
}
// 输出 "The index of llama is 2"
</pre>

如果只是针对字符串而言查找在数组中的某个值的索引，用处不是很大，不过，你可以写出相同功能的泛型函数findIndex，用某个类型T值替换掉提到的字符串。

这里展示如何写一个你或许期望的findStringIndex的泛型版本findIndex。请注意这个函数仍然返回Int，是不是有点迷惑呢，而不是泛型类型?那是因为函数返回的是一个可选的索引数，而不是从数组中得到的一个可选值。需要提醒的是，这个函数不会编译，原因在例子后面会说明：
<pre>
func findIndex<T>(array: [T], _ valueToFind: T) -> Int? {
    for (index, value) in array.enumerate() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}
</pre>

上面所写的函数不会编译。这个问题的位置在等式的检查上，“if value == valueToFind”。不是所有的 Swift 中的类型都可以用等式符（==）进行比较。例如，如果你创建一个你自己的类或结构体来表示一个复杂的数据模型，那么 Swift 没法猜到对于这个类或结构体而言“等于”的意思。正因如此，这部分代码不能可能保证工作于每个可能的类型T，当你试图编译这部分代码时估计会出现相应的错误。

不过，所有的这些并不会让我们无从下手。Swift 标准库中定义了一个Equatable协议，该协议要求任何遵循的类型实现等式符（==）和不等符（!=）对任何两个该类型进行比较。所有的 Swift 标准类型自动支持Equatable协议。

任何Equatable类型都可以安全的使用在findIndex(_:_:)函数中，因为其保证支持等式操作。为了说明这个事实，当你定义一个函数时，你可以写一个Equatable类型约束作为类型参数定义的一部分：
<pre>
func findIndex<T: Equatable>(array: [T], _ valueToFind: T) -> Int? {
    for (index, value) in array.enumerate() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}
</pre>

findIndex中这个单个类型参数写做：T: Equatable，也就意味着“任何T类型都遵循Equatable协议”。

findIndex(_:_:)函数现在则可以成功的编译过，并且作用于任何遵循Equatable的类型，如Double或String:
<pre>
let doubleIndex = findIndex([3.14159, 0.1, 0.25], 9.3)
// doubleIndex is an optional Int with no value, because 9.3 is not in the array
let stringIndex = findIndex(["Mike", "Malcolm", "Andrea"], "Andrea")
// stringIndex is an optional Int containing a value of 2
</pre>

关联类型(Associated Types)
当定义一个协议时，有的时候声明一个或多个关联类型作为协议定义的一部分是非常有用的。一个关联类型作为协议的一部分，给定了类型的一个占位名（或别名）。作用于关联类型上实际类型在协议被实现前是不需要指定的。关联类型被指定为typealias关键字。

关联类型实例
这里是一个Container协议的例子，定义了一个ItemType关联类型：
<pre>
protocol Container {
    typealias ItemType
    mutating func append(item: ItemType)
    var count: Int { get }
    subscript(i: Int) -> ItemType { get }
}
</pre>

Container协议定义了三个任何容器必须支持的兼容要求：

必须可以通过append(_:)方法添加一个新元素到容器里；
必须可以通过使用count属性获取容器里元素的数量，并返回一个Int值；
必须可以通过容器的Int索引值下标可以检索到每一个元素。
这个协议没有指定容器里的元素是如何存储的或何种类型是允许的。这个协议只指定三个任何遵循Container类型所必须支持的功能点。一个遵循的类型在满足这三个条件的情况下也可以提供其他额外的功能。

任何遵循Container协议的类型必须指定存储在其里面的值类型，必须保证只有正确类型的元素可以加进容器里，必须明确可以通过其下标返回元素类型。

为了定义这三个条件，Container协议需要一个方法指定容器里的元素将会保留，而不需要知道特定容器的类型。Container协议需要指定任何通过append(_:)方法添加到容器里的值和容器里元素是相同类型，并且通过容器下标返回的容器元素类型的值的类型是相同类型。

为了达到此目的，Container协议声明了一个ItemType的关联类型，写作typealias ItemType。这个协议不会定义ItemType是什么的别名，这个信息将由任何遵循协议的类型来提供。尽管如此，ItemType别名提供了一种识别Container中元素类型的方法，并且用于append(_:)方法和subscript方法的类型定义，以便保证任何Container期望的行为能够被执行。

这里是一个早前IntStack类型的非泛型版本，遵循Container协议：
<pre>
struct IntStack: Container {
    // IntStack的原始实现
    var items = [Int]()
    mutating func push(item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
    // 遵循Container协议的实现
    typealias ItemType = Int
    mutating func append(item: Int) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Int {
        return items[i]
    }
}
</pre>

IntStack类型实现了Container协议的所有三个要求，在IntStack类型的每个包含部分的功能都满足这些要求。

此外，IntStack指定了Container的实现，适用的ItemType被用作Int类型。对于这个Container协议实现而言，定义 typealias ItemType = Int，将抽象的ItemType类型转换为具体的Int类型。

感谢Swift类型参考，你不用在IntStack定义部分声明一个具体的Int的ItemType。由于IntStack遵循Container协议的所有要求，只要通过简单的查找append(_:)方法的item参数类型和下标返回的类型，Swift就可以推断出合适的ItemType来使用。确实，如果上面的代码中你删除了 typealias ItemType = Int这一行，一切仍旧可以工作，因为它清楚的知道ItemType使用的是何种类型。

你也可以生成遵循Container协议的泛型Stack类型：
<pre>
struct Stack<T>: Container {
    // original Stack<T> implementation
    var items = [T]()
    mutating func push(item: T) {
        items.append(item)
    }
    mutating func pop() -> T {
        return items.removeLast()
    }
    // conformance to the Container protocol
    mutating func append(item: T) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> T {
        return items[i]
    }
}
</pre>

这个时候，占位类型参数T被用作append(_:)方法的item参数和下标的返回类型。Swift 因此可以推断出被用作这个特定容器的ItemType的T的合适类型。

扩展一个存在的类型为一指定关联类型

在在扩展中添加协议成员中有描述扩展一个存在的类型添加遵循一个协议。这个类型包含一个关联类型的协议。

Swift的Array已经提供append(_:)方法，一个count属性和通过下标来查找一个自己的元素。这三个功能都达到Container协议的要求。也就意味着你可以扩展Array去遵循Container协议，只要通过简单声明Array适用于该协议而已。如何实践这样一个空扩展，在通过扩展补充协议声明中有描述这样一个实现一个空扩展的行为：
<pre>
extension Array: Container {}
</pre>

如同上面的泛型Stack类型一样，Array的append(_:)方法和下标保证Swift可以推断出ItemType所使用的适用的类型。定义了这个扩展后，你可以将任何Array当作Container来使用。

Where 语句
类型约束能够确保类型符合泛型函数或类的定义约束。

对关联类型定义约束是非常有用的。你可以在参数列表中通过where语句定义参数的约束。一个where语句能够使一个关联类型遵循一个特定的协议，以及（或）那个特定的类型参数和关联类型可以是相同的。你可以写一个where语句，紧跟在在类型参数列表后面，where语句后跟一个或者多个针对关联类型的约束，以及（或）一个或多个类型和关联类型间的等价(equality)关系。

下面的例子定义了一个名为allItemsMatch的泛型函数，用来检查两个Container实例是否包含相同顺序的相同元素。如果所有的元素能够匹配，那么返回一个为true的Boolean值，反之则为false。

被检查的两个Container可以不是相同类型的容器（虽然它们可以是），但它们确实拥有相同类型的元素。这个需求通过一个类型约束和where语句结合来表示：
<pre>
func allItemsMatch<
    C1: Container, C2: Container
    where C1.ItemType == C2.ItemType, C1.ItemType: Equatable>
    (someContainer: C1, anotherContainer: C2) -> Bool {

        // 检查两个Container的元素个数是否相同
        if someContainer.count != anotherContainer.count {
            return false
        }

        // 检查两个Container相应位置的元素彼此是否相等
        for i in 0..<someContainer.count {
            if someContainer[i] != anotherContainer[i] {
                return false
            }
        }

        // 如果所有元素检查都相同则返回true
        return true

}
</pre>

这个函数用了两个参数：someContainer和anotherContainer。someContainer参数是类型C1，anotherContainer参数是类型C2。C1和C2是容器的两个占位类型参数，决定了这个函数何时被调用。

这个函数的类型参数列紧随在两个类型参数需求的后面：

C1必须遵循Container协议 (写作 C1: Container)。
C2必须遵循Container协议 (写作 C2: Container)。
C1的ItemType同样是C2的ItemType（写作 C1.ItemType == C2.ItemType）。
C1的ItemType必须遵循Equatable协议 (写作 C1.ItemType: Equatable)。
第三个和第四个要求被定义为一个where语句的一部分，写在关键字where后面，作为函数类型参数链的一部分。

这些要求意思是：

someContainer是一个C1类型的容器。 anotherContainer是一个C2类型的容器。 someContainer和anotherContainer包含相同的元素类型。 someContainer中的元素可以通过不等于操作(!=)来检查它们是否彼此不同。

第三个和第四个要求结合起来的意思是anotherContainer中的元素也可以通过 != 操作来检查，因为它们在someContainer中元素确实是相同的类型。

这些要求能够使allItemsMatch(_:_:)函数比较两个容器，即便它们是不同的容器类型。

allItemsMatch(_:_:)首先检查两个容器是否拥有同样数目的items，如果它们的元素数目不同，没有办法进行匹配，函数就会false。

检查完之后，函数通过for-in循环和半闭区间操作（..<）来迭代someContainer中的所有元素。对于每个元素，函数检查是否someContainer中的元素不等于对应的anotherContainer中的元素，如果这两个元素不等，则这两个容器不匹配，返回false。

如果循环体结束后未发现没有任何的不匹配，那表明两个容器匹配，函数返回true。

这里演示了allItemsMatch(_:_:)函数运算的过程：
<pre>
var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tres")

var arrayOfStrings = ["uno", "dos", "tres"]

if allItemsMatch(stackOfStrings, arrayOfStrings) {
    print("All items match.")
} else {
    print("Not all items match.")
}
// 输出 "All items match."
</pre>

上面的例子创建一个Stack单例来存储String，然后压了三个字符串进栈。这个例子也创建了一个Array单例，并初始化包含三个同栈里一样的原始字符串。即便栈和数组是不同的类型，但它们都遵循Container协议，而且它们都包含同样的类型值。因此你可以调用allItemsMatch(_:_:)函数，用这两个容器作为它的参数。在上面的例子中，allItemsMatch(_:_:)函数正确的显示了这两个容器的所有元素都是相互匹配的。