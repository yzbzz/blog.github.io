---
title: 10个惊艳的Swift单行代码
date: 2016-05-22 18:59:51
tags: ios
#top: true
---

# 概述

> 摘至：[10 Swift One Liners To Impress Your Friends](https://www.uraimo.com/2016/01/06/10-Swift-One-Liners-To-Impress-Your-Friends/)



1. 数组中的每个元素乘以2

   ```swift
   (1...1024).map{ $0 * 2 }
   ```

2. 数组中的元素求和

   ```swift
   (1...1024).reduce(0, combine: +)
   ```

<!-- More -->

1. 验证在字符串是否存在指定单词

   使用filter

   ```swift
   let words = ["Swift","iOS","cocoa","OSX","tvOS"]
   let tweet = "This is an example tweet larking about Swift"
   let valid = !words.filter({tweet.containsString($0)}).isEmpty
   print(valid) // true
   // or
   words.contains(tweet.containsString)
   // or
   tweet.characters.split(" ").lazy.map(String.init).contains(Set(words).contains)
   ```

2. 读取文件

   ```swift
   let path = NSBundle.mainBundle().pathForResource("test", ofType: "txt")
   let lines = try? String(contentsOfFile: path!).characters.split{$0 == "\n"}.map(String.init)
   if let lines=lines {
       lines[0] // O! for a Muse of fire, that would ascend
       lines[1] // The brightest heaven of invention!
       lines[2] // A kingdom for a stage, princes to act
       lines[3] // And monarchs to behold the swelling scene.
   }
   ```

3. 祝你生日快乐!

   ```swift
    let name = "uraimo"
    (1...4).forEach{print("Happy Birthday " + (($0 == 3) ? "dear \(name)":"to You"))}
   ```

4. 过滤数组中的数字

   ```swift
    var part3 = [82, 58, 76, 49, 88, 90].reduce( ([],[]), combine: {
        (a:([Int],[Int]),n:Int) -> ([Int],[Int]) in
        (n<60) ? (a.0+[n],a.1) : (a.0,a.1+[n])
    })
    print(part3) // ([58, 49], [82, 76, 88, 90])
   ```

5. 获取并解析XML Web服务

   ```swift
    let xmlDoc = try? AEXMLDocument(xmlData: NSData(contentsOfURL: NSURL(string:"https://www.ibiblio.org/xml/examples/shakespeare/hen_v.xml")!)!)
    if let xmlDoc=xmlDoc {
        var prologue = xmlDoc.root.children[6]["PROLOGUE"]["SPEECH"]
        prologue.children[1].stringValue // Now all the youth of England are on fire,
        prologue.children[2].stringValue // And silken dalliance in the wardrobe lies:
        prologue.children[3].stringValue // Now thrive the armourers, and honour's thought
        prologue.children[4].stringValue // Reigns solely in the breast of every man:
        prologue.children[5].stringValue // They sell the pasture now to buy the horse,
    }
   ```

6. 在数组中查找最小（或最大）值

   ```swift
    //Find the minimum of an array of Ints
    [10,-22,753,55,137,-1,-279,1034,77].sort().first
    [10,-22,753,55,137,-1,-279,1034,77].reduce(Int.max, combine: min)
    [10,-22,753,55,137,-1,-279,1034,77].minElement()
    //Find the maximum of an array of Ints
    [10,-22,753,55,137,-1,-279,1034,77].sort().last
    [10,-22,753,55,137,-1,-279,1034,77].reduce(Int.min, combine: max)
    [10,-22,753,55,137,-1,-279,1034,77].maxElement()
   ```

7. 并行处理

   某些语言允许用一种简单和透明的方式启用数组对功能，例如map和flatMap的并行处理，以加  快顺序和独立操作的执行。

   此功能Swift中还不可用，但可以使用GCD构建：http://moreindirection.blogspot.it/2015/07/gcd-and-parallel-collections-in-swift.html

8. 埃拉托斯特尼筛法

   埃拉托斯特尼筛法用于查找所有的素数直到给定的上限n。

   从小于n的所有整数序列开始，算法删除所有整数的倍数，直到只剩下素数。并且为了加快执行速	度，我们实际上并不需要检查每个整数的倍数，我们止步于n的平方根就可以了。

   根据这一定义首次执行可能是这样的：

   ```swift
   var n = 50
   var primes = Set(2...n)

   (2...Int(sqrt(Double(n)))).forEach{primes.subtractInPlace((2*$0).stride(through:n, by:$0))}
   primes.sort()
   ```

   我们使用外部范围来迭代我们要检查的整数，并且对于每一个整数我们使用 stride(through:Int by:Int)计算出数字的倍数的序列。那些序列然后从Set中减去，Set用所有从2到n的整数初始化。

   但正如你所看到的，为了实际移除倍数，我们使用外部可变Set，导致了附带后果。

   为了消除附带后果，正如我们通常应该做的那样，我们会先计算所有序列，用倍数的单一数组来	flatMap它们，并从初始Set中删除这些整数。

   ```swift
   var sameprimes = Set(2...n)

   sameprimes.subtractInPlace((2...Int(sqrt(Double(n))))
   						   .flatMap{ (2*$0).stride(through:n, by:$0)})
   sameprimes.sort()
   ```

   方式更清洁，使用flatMap的一个很好的例子以生成扁平化的嵌套数组。

9. 元组交换

   ```swift
   var a=1, b=2
   (a, b) = (b, a)
   print(a) // 2
   print(b) // 1
   ```

