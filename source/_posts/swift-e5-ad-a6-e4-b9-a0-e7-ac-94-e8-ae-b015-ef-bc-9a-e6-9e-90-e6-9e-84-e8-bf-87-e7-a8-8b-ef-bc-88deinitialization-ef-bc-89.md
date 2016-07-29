---
title: swift学习笔记15：析构过程（Deinitialization）
tags:
  - iOS
  - swift
  - 析构过程
id: 96
categories:
  - iOS
  - swift学习笔记
date: 2015-12-09 00:00:15
---

析构器只适用于类类型，当一个类的实例被释放之前，析构器会被立即调用。析构器用关键字deinit来标示，类似于构造器要用init来标示。
在类的定义中，每个类最多只能有一个析构器，而且析构器不带任何参数，如下所示：
<pre>
deinit {
    // 执行析构过程
}
</pre>
析构器是在实例释放发生前被自动调用。不能主动调用析构器。子类继承了父类的析构器，并且在子类析构器实现的最后，父类的析构器会被自动调用。即使子类没有提供自己的析构器，父类的析构器也同样会被调用。
因为直到实例的析构器被调用后，实例才会被释放，所以析构器可以访问实例的所有属性，并且可以根据那些属性可以修改它的行为（比如查找一个需要被关闭的文件）。

析构器实践
这是一个析构器实践的例子。这个例子描述了一个简单的游戏，这里定义了两种新类型，分别是Bank和Player。Bank类管理一种虚拟硬币，确保流通的硬币数量永远不可能超过 10,000。在游戏中有且只能有一个Bank存在，因此Bank用类来实现，并使用静态属性和静态方法来存储和管理其当前状态。
<pre>
class Bank {
    static var coinsInBank = 10_000
    static func vendCoins(var numberOfCoinsToVend: Int) -> Int {
        numberOfCoinsToVend = min(numberOfCoinsToVend, coinsInBank)
        coinsInBank -= numberOfCoinsToVend
        return numberOfCoinsToVend
    }
    static func receiveCoins(coins: Int) {
        coinsInBank += coins
    }
}
</pre>
Bank使用coinsInBank属性来跟踪它当前拥有的硬币数量。Bank还提供了两个方法，vendCoins(_:)和receiveCoins(_:)，分别用来处理硬币的分发和收集。
vendCoins(_:)方法在Bank对象分发硬币之前检查是否有足够的硬币。如果硬币不足，Bank对象会返回一个比请求时小的数字（如果Bank对象中没有硬币了就返回0）。vendCoins方法声明numberOfCoinsToVend为一个变量参数，这样就可以在方法体内部修改分发的硬币数量，而不需要定义一个新的变量。vendCoins方法返回一个整型值，表示提供的硬币的实际数量。
receiveCoins(_:)方法只是将Bank对象接收到的硬币数目加回硬币存储中。
Player类描述了游戏中的一个玩家。每一个玩家在任意时间都有一定数量的硬币存储在他们的钱包中。这通过玩家的coinsInPurse属性来表示：
<pre>
class Player {
    var coinsInPurse: Int
    init(coins: Int) {
        coinsInPurse = Bank.vendCoins(coins)
    }
    func winCoins(coins: Int) {
        coinsInPurse += Bank.vendCoins(coins)
    }
    deinit {
        Bank.receiveCoins(coinsInPurse)
    }
}
</pre>
每个Player实例在初始化的过程中，都从Bank对象获取指定数量的硬币。如果没有足够的硬币可用，Player实例可能会收到比指定数量少的硬币.
Player类定义了一个winCoins(_:)方法，该方法从Bank对象获取一定数量的硬币，并把它们添加到玩家的钱包。Player类还实现了一个析构器，这个析构器在Player实例释放前被调用。在这里，析构器的作用只是将玩家的所有硬币都返还给Bank对象：
<pre>
var playerOne: Player? = Player(coins: 100)
print("A new player has joined the game with \(playerOne!.coinsInPurse) coins")
// 打印 "A new player has joined the game with 100 coins"
print("There are now \(Bank.coinsInBank) coins left in the bank")
// 打印 "There are now 9900 coins left in the bank"
</pre>
创建一个Player实例的时候，会向Bank对象请求 100 个硬币，如果有足够的硬币可用的话。这个Player实例存储在一个名为playerOne的可选类型的变量中。这里使用了一个可选类型的变量，因为玩家可以随时离开游戏，设置为可选使可以追踪玩家当前是否在游戏中。
因为playerOne是可选的，所以访问其coinsInPurse属性来打印钱包中的硬币数量时，使用感叹号（!）来解包：
<pre>
playerOne!.winCoins(2_000)
print("PlayerOne won 2000 coins & now has \(playerOne!.coinsInPurse) coins")
// 输出 "PlayerOne won 2000 coins & now has 2100 coins"
print("The bank now only has \(Bank.coinsInBank) coins left")
// 输出 "The bank now only has 7900 coins left"
</pre>
这里，玩家已经赢得了 2,000 枚硬币，所以玩家的钱包中现在有 2,100 枚硬币，而Bank对象只剩余 7,900 枚硬币。
<pre>
playerOne = nil
print("PlayerOne has left the game")
// 打印 "PlayerOne has left the game"
print("The bank now has \(Bank.coinsInBank) coins")
// 打印 "The bank now has 10000 coins"
</pre>
玩家现在已经离开了游戏。这通过将可选类型的playerOne变量设置为nil来表示，意味着“没有Player实例”。当这一切发生时，playerOne变量对Player实例的引用被破坏了。没有其它属性或者变量引用Player实例，因此该实例会被释放，以便回收内存。在这之前，该实例的析构器被自动调用，玩家的硬币被返还给银行。