# JavaScipt 函数式编程之光
# 第一章：为什么使用函数式编程

> 函数式程序员：（名词）将变量命名为 “x”，将函数命名为 “f”，将代码模式称为“zygohistomorphic prepromorphism”（译者注：反正你看不懂的名词）的人。
>
> —— James Iry @jamesiry 5/13/15
>
> https://twitter.com/jamesiry/status/598547781515485184

函数式编程（FP）不是一个新的概念。它横亘于整个编程的历史中。然而，虽然我不确定这样说是否有失公允，但是……直到最近几年，在开发者中，函数式编程似乎一直不是主流的编程范式。我认为函数式更加学院派一些。

但是一切都在改变。人们对函数式越来越感兴趣，这不仅体现在编程语言层面，函数式在三方库和框架中的应用也越来越多。你也发现了无法再忽视函数式编程，所以正在阅读本书。或许你和我一样，曾多次尝试过学习函数式编程，却深陷无边的专业术语和数学符号的泥沼之中。

第一章的目的在于回答以下问题：“我为什么要在代码中使用函数式的编程风格？”以及“本书所讲的函数式与他人口中的有何不同？”。在奠定了基础之后，我们会在后续内容中逐一介绍如何在 JS 中使用函数式编程风格代码的技巧和模式。

## 管中窥豹

让我们通过代码前后对比的方式来简要展示一下书中会涉及到的概念。代码如下：

```js
var numbers = [4,10,0,27,42,17,15,-6,58];
var faves = [];
var magicNumber = 0;

pickFavoriteNumbers();
calculateMagicNumber();
outputMsg();                // The magic number is: 42

// ***************

function calculateMagicNumber() {
    for (let fave of faves) {
        magicNumber = magicNumber + fave;
    }
}

function pickFavoriteNumbers() {
    for (let num of numbers) {
        if (num >= 10 && num <= 20) {
            faves.push( num );
        }
    }
}

function outputMsg() {
    var msg = `The magic number is: ${magicNumber}`;
    console.log( msg );
}
```

现在让我们用另一种完全不同的编程风格来完成同样的事情：

```js
var sumOnlyFavorites = FP.compose( [
    FP.filterReducer( FP.gte( 10 ) ),
    FP.filterReducer( FP.lte( 20 ) )
] )( sum );

var printMagicNumber = FP.pipe( [
    FP.reduce( sumOnlyFavorites, 0 ),
    constructMsg,
    console.log
] );

var numbers = [4,10,0,27,42,17,15,-6,58];

printMagicNumber( numbers );        // The magic number is: 42

// ***************

function sum(x,y) { return x + y; }
function constructMsg(v) { return `The magic number is: ${v}`; }
```

一旦你明白了函数式或者轻量级函数式编程，在*阅读*到第二段代码的时候，内心的活动可能如下：

> 首先，我们创建了一个名为 `sumOnlyFavorites(..)` 的函数，该函数是三个其他函数的组合。我们将两个过滤函数组合起来，其中一个检测值是否大于或等于 10，另一个检查是否小于或等于 20。然后在 transducer 组合中加入 `sum(..)` reducer。最终得到的 `sumOnlyFavorites(..)` 函数是一个 reducer，检测一个给定的值是否能够同时通过两个过滤函数的检测，如果是的话，将其值加到累加值上。
>
> 随后我们创建了另一个名为 `printMagicNumber(..)` 的函数，该函数首先使用之前定义的 reducer `sumOnlyFavorites(..)` 将一个包含数字的列表进行规约，得到通过 *favorite* 数字检查的数字之和。然后 `printMagicNumber(..)` 函数将之前规约的最终结果传递给 `constructMsg(..)` 函数，构造一个 string 值并最终传递给 `console.log(..)`。

这些代码片段向函数式开发者*讲述*着它们的含义，这种方式对现在的你来说可能还很陌生。本书将帮助你*掌握*这种表达方式，从而使得这些代码对你来说像其他代码一样简单易读，或者可读性更高。

上述代码对比的一些其他要点：

* 很可能对于很多读者而言，第一种写法感觉更加舒适、可读性和可维护性都更高。如果有这种想法的话，完全是正常的，不用担心。我相信，如果你坚持阅读本书，并且将其付诸实践，会发现第二种代码的写法将会变得更加自然甚至更好！

* 对于上述代码需要完成的工作，你可能会采用与上述两种代码完全不同的做法，这完全是可以的。本书并不会规定你应该以某种特定的方式来做某事。真正的目的在于说明各种模式的优缺点，使得你能够据此来做出选择。在阅读完本书后，你处理上述问题的方式，有可能会比现在更加接近于第二种方案。

* 有可能你已经是一个经验丰富的函数式开发者了，正在快速地浏览本书的开头部分并据此判断本书是否值得一读。第二种代码中有些地方对你来说已经非常熟悉。但我也打赌你会有多次出现“嗯……我不会用*那样*的处理方式……”的想法。那也没关系，而且这非常合理。

    这不是一本传统的，学院派的函数式编程书籍。有时我们的做法会看起来非常异端。我们都认同函数式不可否认的好处，但也努力在编写可工作、可维护性的 JS 代码与令人生畏的数学、符号和专有名词间寻找着一种务实的平衡。这不是*你的* FP，这是“FLP”。

无论是出于什么原因阅读本书，欢迎你！

## 信心

作为一个软件开发（JavaScript 领域）的教师，我有一个非常简单的假设，并以此作为一切的基础：你不信任的代码是你不理解的代码，反之亦然：你不理解的代码，你不信任它们。此外，如果你无法信任或理解自己的代码，也就没有信心确认所写的代码是否满足给定的任务。运行程序，然后双手合十，祈祷吧。

代码信心意味着什么？我认为它代表着你可以在不运行代码的情况下，通过阅读代码和推理来进行验证，从而得知这段代码*将要*做什么；而不是依赖于这段代码*应该*做什么。通常情况下，我们倾向于依靠运行测试套件来确保程序的正确性。我并不是在说测试不好，我想强调的是我们应该能够很好地理解我们的代码，从而可以在测试运行前能够具有认为测试一定会通过的信心。

仅通过阅读就能够获得对代码的极大信心，这也是函数式编程的设计理念。一个人如果懂得函数式编程，并且能够通过不断练习从而能够在程序中很好地应用函数式的话，那么他写出来的代码能够让他自己*和他人*都能通过阅读来验证程序行为是否如其所愿。

当我们使用能够避免或者最小化 bug 错误源的技术时，对代码的信息也会增加。这可能是函数式编程最大的卖点：函数式的代码通常 bug 更少，如果存在 bug 的话也更容易发现，所以更容易找到问题所在并进行修复。函数式的代码 bug 更少——当然也不是 bug 免疫的。

伴随着本书的阅读之旅，你将对所写的代码越来越有信心，因为你会使用经过充分证明的模式和实践；并且你会避免大部分常见的程序 bug！

## 代码是沟通的媒介

为什么函数式编程如此重要？要回答这个问题，我们需要先回过头来讨论一下为什么编程本身很重要。

我下面的观点可能会让你感到惊讶，但是我不认为代码主要是针对计算机的一组指令。我认为代码能够让计算机运行是一个美丽的意外。

代码的一个非常重要的作用在于充当人与人之间沟通的媒介，这比能够让计算机运行更加重要，我对此深信不疑。

如果你有编程经验的话就会意识到，“编码”过程中花费大量时间的其实是阅读现有的代码。很少有人能够一直在编写新的代码，而不用处理别人（或者是过去的自己）编写的代码。

据普遍估计，开发人员将 70% 的代码维护时间花在了阅读并理解代码上。这个数字可能让人大开眼界。也难怪程序员每天编写的代码行数全球平均值为每天大约 10 行。我们每天花费了高达 7 个小时的时间来阅读代码，并以此决定这 10 行代码该如何编写！

我们需要更多地关注代码的可读性。顺便说一句，可读性高并不是说代码越少越好。实际上，对代码可读性影响最大的是亲和度。<a href="#user-content-footnote-1"><sup>1</sup></a>

如果我们将时间花在努力改善代码的可读性上面，那么函数式编程将会是这些努力的核心。函数式编程建立在被精心审查研究和充分证明的原理之上。花时间学习并掌握函数式编程的原理能够让你写出可读性更高，对自己和他人都更容易辨识，更具亲和度的代码。代码亲和度的提升和辨识度的提高会改善代码的可读性。

例如，一旦你了解了 `map(..)` 的作用，当你在任何程序中看到时，都能立即识别并理解它。但是每次你看到一个 `for` 循环，你将不得不阅读整个循环部分的代码从而理解它。你可能很熟悉 `for` 循环的语法，但是它每次所做的事情实质上都不一样，所以每次都必须*阅读*循环体的内容。

能够一目了然的代码越多，那么花费在弄清代码实际意图上的时间就会越少，于是我们能够将关注点更多的放在更高层级的程序逻辑上；而这些才是真正需要我们关注的重点。

函数式编程（至少在没有大量专业术语的情况下）是写出具有可读性代码的有效工具。*这*是它为什么如此重要的原因。

## 可读性

可读性不是二元特性，它主要是人们主观上对于代码的描述。所以它会随着我们技能和理解能力的提升而发生变化。我经历过类似于下图的变化，而且很多和我讨论过的其他人也有过这样的经历。

<p align="center">
    <img src="images/fig17.png" width="50%">
</p>

你可能会发现自己在阅读本书时，经历着和上图类似的体验。但请记住，如果你坚持到最后，可读性曲线会恢复并有很大提升。

*命令式*描述了我们大多数人非常自然的编写代码的方式；它专注于准确地告知计算机*如何*去做某事。声明式代码——我们将要学习如何去编写的代码方式，采取了函数式编程的原则——更专注于描述*做什么*而不是*如何做*。

让我们重新审视本章前面介绍的两个代码片段。

第一个代码片段是命令式的，关注点几乎都在*如何*完成任务；它充斥着 `if` 语句，`for` 循环，临时变量，重新赋值，改变现有值，带副作用的函数调用，还有函数之间的隐式数据流。你当然*可以*追溯它的逻辑从而搞清楚输入数字的流向以及如何转变为最终的状态，但是这样做一点都清晰直观。

第二个代码片段更加声明式；它没有使用前面命令式代码的方式。请注意，这里没有显式的 if 判断，循环，副作用，重新赋值或者值的修改；相反，它采用了众所周知（至少对函数式编程世界的人来说）和可信任的编程模式，例如 filter，reduce，transduce 和函数组合。关注点从低级别的*如何*做转移到了更高层级的*做什么*。

我们没有用 `if` 来判断一个数字，而是使用了一个众所周知的函数式工具方法 `gte(..)`（大于或等于 greater-than-or-equal-to 的缩写），然后将注意力放在了更重要的工作上，即如何将该 filter 函数与另一个 filter 函数以及 sum 方法组合起来。

除此之外，第二段代码的数据流更加清晰：

1. 一个数字的列表作为参数传递给 `printMagicNumber(..)`。
2. `sumOnlyFavorites(..)` 每次处理列表中的一个数字，最终归约成一个所有判定为 favorite 的数字的和。
3. 上一步的和通过 `constructMsg(..)` 函数转换为消息字符串。
4. 消息字符串通过 `console.log(..)` 打印到终端。

你可能仍然觉得这种方式非常复杂，命令式的代码更容易理解，因为你已经非常熟悉这种类型的代码了；熟悉程度对我们对代码可读性的判断有着巨大的影响。但是，到本书结束的时候，你将能够明白第二个代码片段中的声明式方法所带来的好处，对声明式代码熟悉度的提升，可以让函数式代码对你来说可读性更高。

我知道在这个时间节点让你相信我所说的话，像是一次信仰之跃。

为了像我建议的那样提高代码的可读性，最小化或消除能够导致 bug 的很多错误，这需要更多的努力，有时甚至需要更多的代码。老实说，在我开始写这本书的时候，我绝不可能写出（甚至是完全理解！）第二段代码。随着我的学习之旅，第二段代码让我觉得更加自然和舒适。

如果你指望使用函数式编程像一颗神奇的银弹一样，能够重构你的代码，让其更优雅、聪明、易于修改并简明精炼，这在短期内看起来很容易，但不幸的是，这个愿望不切实际。

作为一种代码构建方式，函数式编程的思考方式非常不同，它使得数据流更加明确，让代码的阅读者能够跟随你的想法。这需要花费时间，虽然可能是一段艰苦的旅程，但是这项工作是值得的。

将一段命令式代码重构成我认为足够清晰的声明式函数式代码时，我仍经常需要进行多次尝试才能够完成。我发现转换到函数式编程范式是一个缓慢的迭代过程，并不能一蹴而就。

对于我写的每段代码，都采用了“以教为学”的测试方式。在我写了一段代码之后，我会将其放置几个小时或几天，然后再回过头来，以全新的眼光审视这些代码，假装自己需要向别人教授或者解释这些代码。通常在前几次测试中，都会发现代码有些混乱和难以理解，于是进行调整然后重复这一过程。

我不是想打击你的积极性。我真心的希望你能够披荆斩棘并最终获得成功。我很高兴因为我做到了。我终于看到了可读性的曲线在上升，努力是值得的，你也可以做到，加油！

## 视角

大多数关于函数式编程的书都采用的是自上而下的方法，但是我们将采取完全不同的方式：自底向上。我们将揭示函数式编程老炮公认的基本原理，这些是函数式编程的基础。但是在大多数情况下，我们会避免使用那些令人生畏的术语或者数学符号，因为这些会很容易让初学者有挫败感。

我认为你如何称呼一个东西不是很重要，更重要的是理解它是什么以及是如何工作的。这并说术语不重要——它无疑会减轻富有经验的专业人士之间的沟通成本。但是对于初学者而言，术语会分散你的注意力。

因此本书将会更多关注于基础概念而不是花哨的术语。也不是说不会涉及到术语，肯定会有的，但是不用过于关注复杂的术语。在必要时，可以越过它们，直击其背后的思想。

我将这种非学院派的实践方式称为“轻量级函数式编程”，因为当你对函数式编程思想还不是特别熟悉的话，函数式的形式化方法会让你感到无所适从。这不是纸上谈兵，而是我自己的亲身经历。甚至在教授函数式编程和编写了本书之后，在遇到函数式中大量术语和符号的形式化方法的时候，我仍然认为是非常非常困难的。我尝试了多次，但其中的很多概念还是觉得难以理解。

我知道很多的函数式编程人员认为形式化方法本身有助于学习。但是我认为在你熟悉这种形式化方法之前，存在着一个学习曲线断崖。如果你恰好已经拥有数学或者计算机的背景，形式化方法可能对你来说会比较容易。但是对如没有的人，无论我们如何努力，形式化方法都会是学习道路上的一个拦路虎。

所以本书介绍了我认为的函数式编程的基础概念，在你从底攀登这个学习曲线的时候为你助力，而不是站在山顶，弯着腰督促你向上爬，然后还让你自己想办法。

## 如何取得平衡

如果你有很长时间的编程经验的话，很可能听过“YAGNI”原则：“你不会需要他（You Ain't Gonna Need It）”。这个原则最初来源于极限编程，强调在不需要某种特性之前，实现它所带来的极高的风险和成本代价。

有时我们猜测将来需要某个功能，于是现在进行开发，因为我们认为现在和其他功能模块一起开发的话会更加容易，但最终意识到猜错了，这个功能是不需要的。或者相反的，猜对了，但是在将来才需要的功能上花了太多的时间，占用了现在真正需要功能的开发时间；分散的精力让机会成本大大增加。

YAGNI 原则要求我们铭记：即使有时它是反直觉的，我们也应该推迟开发某个功能，直到我们需要它为止。我们可以在以后需要的时候进行重构，但是我们的心理预期总是倾向于夸大这种重构的成本。其实推迟做的成本常常没有我们想象的那么高。

应用到函数式编程领域，我的忠告是：本书将会讨论大量有趣且引人注目的编程模式，但这些模式对于你的一些代码可能并不适用，所以不要盲目使用它们。

这也是我和其他传统的函数式编程使用者不同的地方：在某些地方，*可以*使用函数式并不意味着你*应该*使用它。除此之外，还有很多可以解决问题的方式，即使你可能已经学会了一种更加复杂而“面向未来”的编程方式，这种方式更具可维护性和可扩展性，但是可能更简单的函数式编程模式可能就足够解决问题了。

一般来说，我建议你在编码过程中寻找一种平衡，在掌握函数式编程概念的同时，做一个保守派人士，在应用的时候严格克己。在决定是否采用某个特定编程模式或者抽象的时候，默认采用 YAGNI 原则，从而判断它是提高了代码的可读性还是引入了一种未经证实的看似聪明实则不然的复杂性。

> 友情提醒，从未使用的可扩展性不仅仅是浪费精力，有时也可能会妨碍你走的更远
>
> Jeremy D. Miller @jeremydmiller 2/20/15
>
> https://twitter.com/jeremydmiller/status/568797862441586688

请记住，你所编写的每一行代码，都会给阅读者带来相应的阅读成本。这个阅读者可能是其他团队的成员，也可能是未来的你自己。在阅读代码的时候，对于那些为了炫技函数式编程而自认聪明引入的复杂度，从来不会得到认可。

最好的代码是在将来仍最具可读性的代码，因为它在可以/应该是什么（理想主义）和它必须是什么（实用主义）之前取得了恰到好处的平衡。

## 资源

在撰写本书的时候，参考了许多不同的资源。我相信你也能够从中受益，所以我想花点时间向你介绍它们。

### 书籍

一些强烈推荐的函数式、JavaScript 编程书籍：

* [Professor Frisby's Mostly Adequate Guide to Functional Programming](https://drboolean.gitbooks.io/mostly-adequate-guide/content/ch1.html) 作者 [Brian Lonsdorf](https://twitter.com/drboolean)
* [JavaScript Allongé](https://leanpub.com/javascriptallongesix) 作者 [Reg Braithwaite](https://twitter.com/raganwald)
* [Functional JavaScript](http://shop.oreilly.com/product/0636920028857.do) 作者 [Michael Fogus](https://twitter.com/fogus)

### 博客/网站

你应该查看的一些作者和内容：

* [Fun Fun Function Videos](https://www.youtube.com/watch?v=BMUiFMZr7vk) 作者 [Mattias P Johansson](https://twitter.com/mpjme)
* [Awesome FP JS](https://github.com/stoeffel/awesome-fp-js)
* [Kris Jenkins](http://blog.jenkster.com/2015/12/what-is-functional-programming.html)
* [Eric Elliott](https://medium.com/@_ericelliott)
* [James A Forbes](https://james-forbes.com/)
* [James Longster](https://github.com/jlongster)
* [André Staltz](http://staltz.com/)
* [Functional Programming Jargon](https://github.com/hemanth/functional-programming-jargon#functional-programming-jargon)
* [Functional Programming Exercises](https://github.com/InceptionCode/Functional-Programming-Exercises)

### 库

本书中的代码片段基本不依赖于三方库。我们遇到的每一个操作，都会使用无依赖、原生的 JavaScript 进行推导。但是，当你使用函数式编程来构建越来越多的实际代码时，你会希望拥有一个库能够提供那些高度优化并且高度可靠的常用函数。

顺便说一句，你需要查看所使用的库的文档从而确保了解它们的工作方式。库提供的工具和本书构建的有很多相似之处，但毫无疑问会存在一些差异，即使是在很流行的库之间也是如此。

以下是一些流行的 JavaScript 的函数式编程库，可以通过它们来开始你的函数式编程之旅：

* [Ramda](http://ramdajs.com)
* [lodash/fp](https://github.com/lodash/lodash/wiki/FP-Guide)
* [functional.js](http://functionaljs.com/)
* [Immutable.js](https://github.com/facebook/immutable-js)

[附录 C 深入研究了这些](apC_zh.md/#stuff-to-investigate)以及其他的一些库。

## 总结

You may have a variety of reasons for starting to read this book, and different expectations of what you'll get out of it. This chapter has explained why I want you to read the book and what I want you to get out of the journey. It also helps you articulate to others (like your fellow developers) why they should come on the journey with you!

Functional programming is about writing code that is based on proven principles so we can gain a level of confidence and trust over the code we write and read. We shouldn't be content to write code that we anxiously *hope* works, and then abruptly breathe a sigh of relief when the test suite passes. We should *know* what it will do before we run it, and we should be absolutely confident that we've communicated all these ideas in our code for the benefit of other readers (including our future selves).

This is the heart of Functional-Light JavaScript. The goal is to learn to effectively communicate with our code but not have to suffocate under mountains of notation or terminology to get there.

The journey to learning functional programming starts with deeply understanding the nature of what a function is. That's what we tackle in the next chapter.

----

<a name="footnote-1"><sup>1</sup></a>Buse, Raymond P. L., and Westley R. Weimer. “Learning a Metric for Code Readability.” IEEE Transactions on Software Engineering, IEEE Press, July 2010, dl.acm.org/citation.cfm?id=1850615.
