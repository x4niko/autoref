---
title: swift学习笔记19：类型转换（Type Casting）
tags:
  - iOS
  - swift
  - 类型转换
id: 105
categories:
  - iOS
  - swift学习笔记
date: 2015-12-17 18:05:19
---

类型转换在Swift中使用is和as操作符实现，这两个操作符提供了一种简单达意的方式去检查值的类型或者转换它的类型。也可以用它来检查一个类型是否实现了某个协议。

定义一个类层次作为例子
可以将类型转换用在类和子类的层次结构上，检查特定类实例的类型并且转换这个类实例的类型成为这个层次结构中的其他类型。下面的代码定义了一个类层次和一个包含了这些类实例的数组，作为类型转换的例子。
<pre>
// 这个类为任何出现在数字媒体库的媒体项提供基础功能
class MediaItem {
    var name: String
    init(name: String) {
        self.name = name
    }
}

// 子类 Movie 封装了与电影相关的额外信息
class Movie: MediaItem {
    var director: String
    init(name: String, director: String) {
        self.director = director
        super.init(name: name)
    }
}

// 增加了一个 artist（艺术家）属性，和相应的初始化器
class Song: MediaItem {
    var artist: String
    init(name: String, artist: String) {
        self.artist = artist
        super.init(name: name)
    }
}

let library = [
    Movie(name: "Casablanca", director: "Michael Curtiz"),
    Song(name: "Blue Suede Shoes", artist: "Elvis Presley"),
    Movie(name: "Citizen Kane", director: "Orson Welles"),
    Song(name: "The One And Only", artist: "Chesney Hawkes"),
    Song(name: "Never Gonna Give You Up", artist: "Rick Astley")
]
// Swift 的类型检测器能够推断出 Movie 和 Song 有共同的父类 MediaItem，数组 library 的类型被推断为 [MediaItem]
</pre>

检查类型（Checking Type）
用类型检查操作符（is）来检查一个实例是否属于特定子类型。若实例属于那个子类型，类型检查操作符返回 true，否则返回 false。

下面的例子定义了两个变量，movieCount 和 songCount，用来计算数组 library 中 Movie 和 Song 类型的实例数量：
<pre>
var movieCount = 0
var songCount = 0

for item in library {
    if item is Movie {
        ++movieCount
    } else if item is Song {
        ++songCount
    }
}
print("Media library contains \(movieCount) movies and \(songCount) songs")
// 打印 “Media library contains 2 movies and 3 songs”
</pre>

向下转型（Downcasting）
某类型的一个常量或变量可能在幕后实际上属于一个子类。当确定是这种情况时，可以尝试向下转到它的子类型，用类型转换操作符（as? 或 as!）。
因为向下转型可能会失败，类型转型操作符带有两种不同形式。条件形式（conditional form）as? 返回一个试图向下转成的类型的可选值（optional value）。强制形式 as! 把试图向下转型和强制解包（force-unwraps）转换结果结合为一个操作。

在这个示例中，数组中的每一个 item 可能是 Movie 或 Song。事前不知道每个 item 的真实类型，所以这里使用条件形式的类型转换（as?）去检查循环里的每次下转：
<pre>
for item in library {
    if let movie = item as? Movie {
        print("Movie: '\(movie.name)', dir. \(movie.director)")
    } else if let song = item as? Song {
        print("Song: '\(song.name)', by \(song.artist)")
    }
}

// Movie: 'Casablanca', dir. Michael Curtiz
// Song: 'Blue Suede Shoes', by Elvis Presley
// Movie: 'Citizen Kane', dir. Orson Welles
// Song: 'The One And Only', by Chesney Hawkes
// Song: 'Never Gonna Give You Up', by Rick Astley
</pre>

Any 和 AnyObject 的类型转换
Swift 为不确定类型提供了两种特殊的类型别名：
1、AnyObject可以表示任何类类型的实例。
2、Any可以表示任何类型，包括函数类型。

AnyObject 类型
定义一个 [AnyObject] 类型的数组并填入三个 Movie 类型的实例：
<pre>
let someObjects: [AnyObject] = [
    Movie(name: "2001: A Space Odyssey", director: "Stanley Kubrick"),
    Movie(name: "Moon", director: "Duncan Jones"),
    Movie(name: "Alien", director: "Ridley Scott")
]

// 因为知道这个数组只包含 Movie 实例，可以直接用（as!）下转并解包到非可选的 Movie 类型：
for object in someObjects {
    let movie = object as! Movie
    print("Movie: '\(movie.name)', dir. \(movie.director)")
}
// Movie: '2001: A Space Odyssey', dir. Stanley Kubrick
// Movie: 'Moon', dir. Duncan Jones
// Movie: 'Alien', dir. Ridley Scott

// 为了变为一个更简短的形式，下转 someObjects 数组为 [Movie] 类型而不是下转数组中的每一项：
for movie in someObjects as! [Movie] {
    print("Movie: '\(movie.name)', dir. \(movie.director)")
}
// Movie: '2001: A Space Odyssey', dir. Stanley Kubrick
// Movie: 'Moon', dir. Duncan Jones
// Movie: 'Alien', dir. Ridley Scott
</pre>

Any 类型
使用Any类型来和混合的不同类型一起工作，包括函数类型和非类类型。它创建了一个可以存储Any类型的数组things：
<pre>
var things = [Any]()
things.append(0)
things.append(0.0)
things.append(42)
things.append(3.14159)
things.append("hello")
things.append((3.0, 5.0))
things.append(Movie(name: "Ghostbusters", director: "Ivan Reitman"))
things.append({ (name: String) -> String in "Hello, \(name)" })

// 迭代 things 数组中的每一项，并用 switch 语句查找每一项的类型。
for thing in things {
    switch thing {
    case 0 as Int:
        print("zero as an Int")
    case 0 as Double:
        print("zero as a Double")
    case let someInt as Int:
        print("an integer value of \(someInt)")
    case let someDouble as Double where someDouble > 0:
        print("a positive double value of \(someDouble)")
    case is Double:
        print("some other double value that I don't want to print")
    case let someString as String:
        print("a string value of \"\(someString)\"")
    case let (x, y) as (Double, Double):
        print("an (x, y) point at \(x), \(y)")
    case let movie as Movie:
        print("a movie called '\(movie.name)', dir. \(movie.director)")
    case let stringConverter as String -> String:
        print(stringConverter("Michael"))
    default:
        print("something else")
    }
}

// zero as an Int
// zero as a Double
// an integer value of 42
// a positive double value of 3.14159
// a string value of "hello"
// an (x, y) point at 3.0, 5.0
// a movie called 'Ghostbusters', dir. Ivan Reitman
// Hello, Michael
</pre>