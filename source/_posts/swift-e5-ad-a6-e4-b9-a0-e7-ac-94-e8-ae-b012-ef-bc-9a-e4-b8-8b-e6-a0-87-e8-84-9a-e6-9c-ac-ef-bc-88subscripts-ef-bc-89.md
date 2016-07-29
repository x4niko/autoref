---
title: swift学习笔记12：下标脚本（Subscripts）
tags:
  - iOS
  - swift
  - 下标脚本
id: 89
categories:
  - iOS
  - swift学习笔记
date: 2015-12-03 10:27:35
---

下标脚本语法
下标脚本允许你通过在实例名称后面的方括号中传入一个或者多个索引值来对实例进行存取。语法类似于实例方法语法和计算型属性语法的混合。与定义实例方法类似，定义下标脚本使用subscript关键字，指定一个或多个入参和返回类型。与实例方法不同的是，下标脚本可以设定为读写或只读。这种行为由 getter 和 setter 实现，有点类似计算型属性：
<pre>
subscript(index: Int) -> Int {
    get {
      // 返回一个适当的 Int 类型的值
    }

    set(newValue) {
      // 执行适当的赋值操作
    }
}
</pre>
newValue的类型和下标脚本的返回类型相同。如同计算型属性，可以不指定 setter 的参数（newValue）。如果不指定参数，setter 会提供一个名为newValue的默认参数。

如同只读计算型属性，可以省略只读下标脚本的get关键字。下面代码演示了只读下标脚本的实现，这里定义了一个TimesTable结构体，用来表示传入整数的乘法表：
<pre>
struct TimesTable {
    let multiplier: Int
    subscript(index: Int) -> Int {
        return multiplier * index
    }
}
let threeTimesTable = TimesTable(multiplier: 3)
print("six times three is \(threeTimesTable[6])")
// 输出 "six times three is 18"
</pre>

下标脚本用法
下标脚本的确切含义取决于使用场景。下标脚本通常作为访问集合（collection），列表（list）或序列（sequence）中元素的快捷方式。可以针对自己特定的类或结构体的功能来自由地以最恰当的方式实现下标脚本。

例如，Swift的Dictionary类型实现下标脚本用于对其实例中储存的值进行存取操作。为字典设值时，在下标脚本中使用和字典的键类型相同的键，并把一个和字典的值类型相同的值赋给这个下标脚本：
<pre>
var numberOfLegs = ["spider": 8, "ant": 6, "cat": 4]
numberOfLegs["bird"] = 2
</pre>
上例定义一个名为numberOfLegs的变量，并用一个包含三对键值的字典字面量初始化它。numberOfLegs字典的类型被推断为[String: Int]。字典创建完成后，该例子通过下标脚本将String类型的键bird和Int类型的值2添加到字典中。
注意：Swift 的Dictionary类型的下标脚本接受并返回可选类型的值。上例中的numberOfLegs字典通过下标脚本返回的是一个Int?或者说“可选的int”。Dictionary类型之所以如此实现下标脚本，是因为不是每个键都有个对应的值，同时这也提供了一种通过键删除对应值的方式，只需将键对应的值赋值为nil即可。

下标脚本选项
下标脚本可以接受任意数量的入参，并且这些入参可以是任意类型。下标脚本的返回值也可以是任意类型。下标脚本可以使用变量参数和可变参数，但不能使用输入输出参数，也不能给参数设置默认值。
一个类或结构体可以根据自身需要提供多个下标脚本实现，使用下标脚本时将通过入参的数量和类型进行区分，自动匹配合适的下标脚本，这就是下标脚本的重载。
虽然接受单一入参的下标脚本是最常见的，但也可以根据情况定义接受多个入参的下标脚本。例如下例定义了一个Matrix结构体，用于表示一个Double类型的二维矩阵。Matrix结构体的下标脚本接受两个整型参数：
<pre>
struct Matrix {
    let rows: Int, columns: Int
    var grid: [Double]
    init(rows: Int, columns: Int) {
        self.rows = rows
        self.columns = columns
        grid = Array(count: rows * columns, repeatedValue: 0.0)
    }
    func indexIsValidForRow(row: Int, column: Int) -> Bool {
        return row >= 0 && row < rows && column >= 0 && column < columns
    }
    subscript(row: Int, column: Int) -> Double {
        get {
            assert(indexIsValidForRow(row, column: column), "Index out of range")
            return grid[(row * columns) + column]
        }
        set {
            assert(indexIsValidForRow(row, column: column), "Index out of range")
            grid[(row * columns) + column] = newValue
        }
    }
}
</pre>
Matrix提供了一个接受两个入参的构造方法，入参分别是rows和columns，创建了一个足够容纳rows * columns个Double类型的值的数组。通过传入数组长度和初始值0.0到数组的构造器，将矩阵中每个位置的值初始化为0.0。
可以通过传入合适的row和column的数量来构造一个新的Matrix实例：
<pre>
// 创建一个Matrix实例来表示两行两列的矩阵
var matrix = Matrix(rows: 2, columns: 2)
// 将row和column的值传入下标脚本来为矩阵设值，下标脚本的入参使用逗号分隔
matrix[0, 1] = 1.5
matrix[1, 0] = 3.2

// Matrix包含了一个名为indexIsValidForRow(_:column:)的便利方法，用来检查入参row和column的值是否在矩阵范围内
let someValue = matrix[2, 2]
// 断言将会触发，因为 [2, 2] 已经超过了 matrix 的范围
</pre>