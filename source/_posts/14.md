---
title: swift学习笔记4：集合类型 (Collection Types)
tags:
  - iOS
  - swift
  - 集合
id: 63
categories:
  - iOS
  - swift学习笔记
date: 2015-11-21 15:31:29
---

swift提供三种基本的集合类型：Array, Set, 和Dictionary。
Array为有序值的集合，Set为无序的唯一值的集合，Dictionary为无序的键值对关联集合
![swift三种基本的集合类型](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Art/CollectionTypes_intro_2x.png)

Array：
<pre>
var someInts = [Int]()   // 创建一个Int的空数组
someInts.append(3)
someInts = []            // 现在为空数组，但类型还是[Int]

// 创建一个[Double]类型的数组，大小为3，默认值为[0.0, 0.0, 0.0]
var threeDoubles = [Double](count: 3, repeatedValue: 0.0)
// 再创建另外一个[Double]类型的数组
var anotherThreeDoubles = [Double](count: 3, repeatedValue: 2.5)
var sixDoubles = threeDoubles + anotherThreeDoubles
// 相加后类型推断为[Double], 值为[0.0, 0.0, 0.0, 2.5, 2.5, 2.5]

// 或者下面两种方式初始化
var shoppingList: [String] = ["Eggs", "Milk"]
var shoppingList = ["Eggs", "Milk"]
shoppingList.count    // 数组大小
shoppingList.isEmpty  // 是否为空

// 数组相加
shoppingList += ["Baking Powder"]
shoppingList += ["Chocolate Spread", "Cheese", "Butter"]
// 获取第一个值
var firstItem = shoppingList[0]
// 更改第一个值
shoppingList[0] = "Six eggs"
//  用"Bananas" and "Apples"替换索引4、5、6的"Chocolate Spread", "Cheese"和"Butter"
shoppingList[4...6] = ["Bananas", "Apples"]

// 在指定位置插入值
shoppingList.insert("Maple Syrup", atIndex: 0)
// 移除指定位置的值
let mapleSyrup = shoppingList.removeAtIndex(0)
// 移除最后一个值
let apples = shoppingList.removeLast()

// 遍历数组
for item in shoppingList {
    print(item)
}
for (index, value) in shoppingList.enumerate() {
    print("Item \(index + 1): \(value)")
}
</pre>

Set：
Set必须存储可哈希的类型， 如果a == b, 那么a.hashValue == b.hashValue。Swift’s 基本类型(String, Int, Double和Bool)默认都是可哈希的。
<pre>
// 创建一个空集合
var letters = Set<Character>()
// 插入值
letters.insert("a")
// 置空，还是Set<Character>类型
letters = []

// 两种初始化
var favoriteGenres: Set<String> = ["Rock", "Classical", "Hip hop"]
var favoriteGenres: Set = ["Rock", "Classical", "Hip hop"]

favoriteGenres.count        // 集合大小
favoriteGenres.isEmpty      // 是否为空

favoriteGenres.insert("Jazz")      // 插入值
favoriteGenres.remove("Rock")      // 删除值
favoriteGenres.contains("Funk")    // 判断是否包含某个值

// 遍历集合
for genre in favoriteGenres {
    print("\(genre)")
}
// 排序后遍历
for genre in favoriteGenres.sort() {
    print("\(genre)")
}
</pre>

基本的Set操作：
![基本的Set操作](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Art/setVennDiagram_2x.png)

intersect(_:) 返回共同值的集合.
exclusiveOr(_:) 返回不同值的集合.
union(_:) 返回两个集合合并后的集合，忽略重复的值.
subtract(_:) 返回一个集合中得值不在另一个集合的值的集合.
<pre>
let oddDigits: Set = [1, 3, 5, 7, 9]
let evenDigits: Set = [0, 2, 4, 6, 8]
let singleDigitPrimeNumbers: Set = [2, 3, 5, 7]

oddDigits.union(evenDigits).sort()
// [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
oddDigits.intersect(evenDigits).sort()
// []
oddDigits.subtract(singleDigitPrimeNumbers).sort()
// [1, 9]
oddDigits.exclusiveOr(singleDigitPrimeNumbers).sort()
// [1, 2, 9]
</pre>

使用(==)判断两个集合所有的值是否相等
使用isSubsetOf(_:)判断一个集合是否为另一个集合的子集
使用isSupersetOf(_:)判断一个集合是否为另一个集合的母集
使用isStrictSubsetOf(_:)或isStrictSupersetOf(_:)判断一个集合是否为另一个集合的子集或母集，但两个集合不相等
使用isDisjointWith(_:)判断两个集合是否有共同的值，没有为true，反之false
<pre>
let houseAnimals: Set = ["dog", "cat"]
let farmAnimals: Set = ["cow", "chicken", "sheep", "dog", "cat"]
let cityAnimals: Set = ["bird", "mouse"]

houseAnimals.isSubsetOf(farmAnimals)
// true
farmAnimals.isSupersetOf(houseAnimals)
// true
farmAnimals.isDisjointWith(cityAnimals)
// true
</pre>

Dictionary：
和Set一样，Dictionary的值也要是可哈希的。
<pre>
// 创建一个空字典
var namesOfIntegers = [Int: String]()
namesOfIntegers[16] = "sixteen"
// 置空
namesOfIntegers = [:]

// 两种初始化
var airports: [String: String] = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]
var airports = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]

airports.count          // 字典大小
airports.isEmpty        // 判断是否为空

airports["LHR"] = "London"              // 增加一个字典值
airports["LHR"] = "London Heathrow"     // 修改字典值

// updateValue返回更新前的值
let oldValue = airports.updateValue("Dublin Airport", forKey: "DUB")

// removeValueForKey删除指定key的值，返回被删除的值，如果没有指定的key，则返回nil
let removedValue = airports.removeValueForKey("DUB")

// 遍历键值对
for (airportCode, airportName) in airports {
    print("\(airportCode): \(airportName)")
}
// 遍历键
for airportCode in airports.keys {
    print("Airport code: \(airportCode)")
}
// 遍历值
for airportName in airports.values {
    print("Airport name: \(airportName)")
}

// 分别对键值操作
let airportCodes = [String](airports.keys)      // ["YYZ", "LHR"]
let airportNames = [String](airports.values)    // ["Toronto Pearson", "London Heathrow"]
</pre>