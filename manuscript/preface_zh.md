# JavaScript 函数式编程之光

# 前言

> _一个自函子范畴上的幺半群。（A monad is just a monoid in the category of endofunctors.）_

我是不是让你非常的迷惘？别担心，我也感到非常迷惑！这些专业解释只有对已经掌握了函数式编程（Functional Programming&trade; (FP)）的人有意义，对于我们其他人来说只是一些专业术语无意义的堆砌。

本书不会教你这些词的含义。如果这正是你想要了解的东西的话，那么可以放下本书了。实际上，已经有很多讲授函数式编程的书籍了，而且非常不错，他们通过自顶向下的方式，来说明什么是函数式编程的*正确的方式*。这些术语具有非常重要的意义，如果你要正式深入学习函数式编程的话，你绝对想要搞清楚这些术语的含义。

但是这本书将以完全不同的方式来探讨这个主题。我将从头开始介绍函数式的基础概念，尽量少的使用特殊或那些让人困惑的专业术语。我们将尝试使用更加贴近编程实战的方式来探讨每一个话题，而不是从学院派的角度进行讲解。毫无疑问，我们**会接触到术语**。但是在介绍这些术语的时候，我们会非常小心谨慎，并且解释他们存在的重要性。

遗憾的是，我不是正统函数式编程俱乐部的一员，也从未正式地教过关于函数编程的任何东西。虽然我有计算机的教育背景，数学也不错，但是各种数学符号不是我理解编程的思维方式。我从未写过 Scheme，Clojure 或者 Haskell，也不是老牌的 Lisp 程序员。

我参加过无数次关于函数式编程的技术大会，每一次都抱着希望而去，心里想着*这次*我肯定能够理解函数式编程教义的全部内容！然而每一次，都是失望而归，脑海中充斥着各种专业术语，不知道自己学到了个啥。很长一段时间，我都无法搞清这些术语是什么意思。

渐渐的，在这些专业术语中浸淫的久了，我慢慢的有了一些头绪，理解了一些非常重要的概念，然而这些概念对于老牌的函数式编程使用者来说非常自然和容易理解。我通过不断的编码实践来慢慢地学习它们，而不是通过学习学院派的术语名词。不知道你们有没有过这种经历，很早之前就了解一个技术，然后在很长一段时候之后，才发现它居然有个专有名词！

也许你也和我一样；多年来，我一直听到诸如“大数据”等行业细分领域的“map-reduce”这样的术语，但是并没有真正的了解它们是什么。就像终于有一天我明白了 `map(..)` 函数做了什么——但在此之前，我丝毫不知道列表操作是函数式编程的基石，而且如此重要。我很早就明白*map*的作用，但是后来才明白 `map(..)` 是专有名词。

后来，我开始收集这些顿悟的花絮，并写成了这本《JavaScript 函数式编程之光》。

## 使命

话说回来，为什么学习函数式编程对你来说非常重要呢，甚至是学习其轻量级的版本（FLP）？

近些年来，我开始对一些观点深信不疑，*几乎*到了宗教信仰的程度。我认为编程的根本是关于人，而不是关于代码。我认为代码首先而且最重要的功能，是作为人类交流沟通的手段，同时恰巧有个*副作用*（此处有笑声），可以被计算机运行。

我认为函数式编程的核心在于，使用了一些广为人知、易于理解的编程模式，*并且*被证实能够避免出现难以理解错误。从这个角度来说，FP 或者本书 FLP——可能是任何开发人员可以获得的最重要的编程工具之一。

> monad 诅咒是指……一旦你理解了 monad……就失去了向其他人解释它的能力。
>
> —— Douglas Crockford 2012 年名为 "Monads 和 Gonads" 的演讲
>
> https://www.youtube.com/watch?v=dkZFtimgAcM

I hope this book "Maybe" breaks the spirit of that curse, even though we won't talk about "monads" until the very end in the appendices.

The formal FPer will often assert that the _real value_ of FP is in using it essentially 100%: it's an all-or-nothing proposition. The belief is that if you use FP in one part of your program but not in another, the whole program is polluted by the non-FP stuff and therefore suffers enough that the FP was probably not worth it.

I'll say unequivocally: **I think that absolutism is bogus**. That's as silly to me as suggesting that this book is only good if I use perfect grammar and active voice throughout; if I make any mistakes, it degrades the entire book's quality. Nonsense.

The better I am at writing in a clear, consistent voice, the better your reading experience will be. But I'm not a 100% perfect author. Some parts will be better written than others. The parts where I can still improve are not going to invalidate the other parts of this book which are useful.

And so it goes with our code. The more you can apply these principles to more parts of your code, the better your code will be. Use them well 25% of the time, and you'll get some good benefit. Use them 80% of the time, and you'll see even more benefit.

With perhaps a few exceptions, I don't think you'll find many absolutes in this text. We'll instead talk about aspirations, goals, principles to strive for. We'll talk about balance and pragmatism and trade-offs.

Welcome to this journey into the most useful and practical foundations of FP. We both have plenty to learn!
