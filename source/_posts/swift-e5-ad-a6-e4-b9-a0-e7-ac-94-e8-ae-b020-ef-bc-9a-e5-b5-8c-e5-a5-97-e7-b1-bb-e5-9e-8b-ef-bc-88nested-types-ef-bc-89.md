---
title: swift学习笔记20：嵌套类型（Nested Types）
tags:
  - iOS
  - swift
  - 嵌套类型
id: 107
categories:
  - iOS
  - swift学习笔记
date: 2015-12-19 11:17:05
---

要在一个类型中嵌套另一个类型，将嵌套类型的定义写在其外部类型的{}内，而且可以根据需要定义多级嵌套。

嵌套类型实践
下面这个例子定义了一个结构体BlackjackCard（二十一点），用来模拟BlackjackCard中的扑克牌点数。BlackjackCard结构体包含两个嵌套定义的枚举类型Suit和Rank。在BlackjackCard中，Ace牌可以表示1或者11，Ace牌的这一特征通过一个嵌套在Rank枚举中的结构体Values来表示：
<pre>
struct BlackjackCard {
   // 嵌套的 Suit 枚举
   enum Suit: Character {
      case Spades = "♠", Hearts = "♡", Diamonds = "♢", Clubs = "♣"
   }

   // 嵌套的 Rank 枚举
   enum Rank: Int {
      case Two = 2, Three, Four, Five, Six, Seven, Eight, Nine, Ten
      case Jack, Queen, King, Ace
      struct Values {
         let first: Int, second: Int?
      }
      var values: Values {
         switch self {
            case .Ace:
               return Values(first: 1, second: 11)
            case .Jack, .Queen, .King:
               return Values(first: 10, second: nil)
            default:
               return Values(first: self.rawValue, second: nil)
         }
      }
   }

   // BlackjackCard 的属性和方法
   let rank: Rank, suit: Suit
      var description: String {
      var output = "suit is \(suit.rawValue),"
      output += " value is \(rank.values.first)"
      if let second = rank.values.second {
         output += " or \(second)"
      }
      return output
   }
}
</pre>
Suit枚举用来描述扑克牌的四种花色，并用一个Character类型的原始值表示花色符号。
Rank枚举用来描述扑克牌从Ace~10，以及J、Q、K，这13种牌，并用一个Int类型的原始值表示牌的面值。（这个Int类型的原始值未用于Ace、J、Q、K这4种牌。）

因为BlackjackCard是一个没有自定义构造器的结构体，在结构体的逐一成员构造器中可知，结构体有默认的成员构造器，所以可以用默认的构造器去初始化新常量theAceOfSpades：
<pre>
let theAceOfSpades = BlackjackCard(rank: .Ace, suit: .Spades)
print("theAceOfSpades: \(theAceOfSpades.description)")
// 打印 “theAceOfSpades: suit is ♠, value is 1 or 11”
</pre>
尽管Rank和Suit嵌套在BlackjackCard中，但它们的类型仍可从上下文中推断出来，所以在初始化实例时能够单独通过成员名称（.Ace和.Spades）引用枚举实例。在上面的例子中，description属性正确地反映了黑桃A牌具有1和11两个值。

引用嵌套类型
在外部引用嵌套类型时，在嵌套类型的类型名前加上其外部类型的类型名作为前缀：
<pre>
let heartsSymbol = BlackjackCard.Suit.Hearts.rawValue
// 红心符号为 “♡”
</pre>