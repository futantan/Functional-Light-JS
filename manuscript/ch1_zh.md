# JavaScipt 函数式编程之光
# 第一章：为什么使用函数式编程

> 函数式程序员：（名词）。将变量命名为 “x”，将函数命名为 “f”，将代码模式称为“zygohistomorphic prepromorphism”（译者注：反正你看不懂的名词）的人。
>
> —— James Iry @jamesiry 5/13/15
>
> https://twitter.com/jamesiry/status/598547781515485184

函数式编程（FP）不是一个新的概念。它存在于整个编程的历史中。然而，虽然我不确定这样说是否有失公允，但是……直到最近几年，在开发者中，函数式编程似乎一直不是主流的编程范式。我认为函数式更加学院派一些。

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

## Communication

Why is Functional Programming important? To answer that, we need to take a bigger step back and talk about why programming itself is important.

It may surprise you to hear this, but I don't believe that code is primarily a set of instructions for the computer. Actually, I think the fact that code instructs the computer is almost a happy accident.

I believe very deeply that the vastly more important role of code is as a means of communication with other human beings.

You probably know by experience that an awful lot of your time spent "coding" is actually spent reading existing code. Very few of us are so privileged as to spend all or most of our time simply banging out all new code and never dealing with code that others (or our past selves) wrote.

It's widely estimated that developers spend 70% of code maintenance time on reading to understand it. That is eye-opening. 70%. No wonder the global average for a programmer's lines of code written per day is about 10. We spend up to 7 hours of our day just reading the code to figure out where those 10 lines should go!

We need to focus a lot more on the readability of our code. And by the way, readability is not just about fewer characters. Readability is actually most impacted by familiarity.<a href="#user-content-footnote-1"><sup>1</sup></a>

If we are going to spend our time concerned with making code that will be more readable and understandable, FP is central in that effort. The principles of FP are well established, deeply studied and vetted, and provably verifiable. Taking the time to learn and employ these FP principles will ultimately lead to more readily and recognizably familiar code for you and others. The increase in code familiarity, and the expediency of that recognition, will improve code readability.

For example, once you learn what `map(..)` does, you'll be able to almost instantly spot and understand it when you see it in any program. But every time you see a `for` loop, you're going to have to read the whole loop to understand it. The syntax of the `for` loop may be familiar, but the substance of what it's doing is not; that has to be *read*, every time.

By having more code that's recognizable at a glance, and thus spending less time figuring out what the code is doing, our focus is freed up to think about the higher levels of program logic; this is the important stuff that most needs our attention anyway.

FP (at least, without all the terminology weighing it down) is one of the most effective tools for crafting readable code. *That* is why it's so important.

## Readability

Readability is not a binary characteristic. It's a largely subjective human factor describing our relationship to code. And it will naturally vary over time as our skills and understanding evolve. I have experienced effects similar to the following figure, and anecdotally many others I've talked to have as well.

<p align="center">
    <img src="images/fig17.png" width="50%">
</p>

You may just find yourself experiencing similar effects as you work through the book. But take heart; if you stick this out, the curve comes back up!

*Imperative* describes the code most of us probably already write naturally; it's focused on precisely instructing the computer *how* to do something. Declarative code -- the kind we'll be learning to write, which adheres to FP principles -- is code that's more focused on describing the *what* outcome.

Let's revisit the two code snippets presented earlier in this chapter.

The first snippet is imperative, focused almost entirely on *how* to do the tasks; it's littered with `if` statements, `for` loops, temporary variables, reassignments, value mutations, function calls with side effects, and implicit data flow between functions. You certainly *can* trace through its logic to see how the numbers flow and change to the end state, but it's not at all clear or straightforward.

The second snippet is more declarative; it does away with most of those aforementioned imperative techniques. Notice there's no explicit conditionals, loops, side effects, reassignments, or mutations; instead, it employs well-known (to the FP world, anyway!) and trustable patterns like filtering, reduction, transducing, and composition. The focus shifts from low-level *how* to higher level *what* outcomes.

Instead of messing with an `if` statement to test a number, we delegate that to a well-known FP utility like `gte(..)` (greater-than-or-equal-to), and then focus on the more important task of combining that filter with another filter and a summation function.

Moreover, the flow of data through the second program is explicit:

1. A list of numbers goes into `printMagicNumber(..)`.
2. One at a time those numbers are processed by `sumOnlyFavorites(..)`, resulting in a single number total of only our favorite kinds of numbers.
3. That total is converted to a message string with `constructMsg(..)`.
4. The message string is printed to the console with `console.log(..)`.

You may still feel this approach is convoluted, and that the imperative snippet was easier to understand. You're much more accustomed to it; familiarity has a profound influence on our judgments of readability. By the end of this book, though, you will have internalized the benefits of the second snippet's declarative approach, and that familiarity will spring its readability to life.

I know asking you to believe that at this point is a leap of faith.

It takes a lot more effort, and sometimes more code, to improve its readability as I'm suggesting, and to minimize or eliminate many of the mistakes that lead to bugs. Quite honestly, when I started writing this book, I could never have written (or even fully understood!) that second snippet. As I'm now further along on my journey of learning, it's more natural and comfortable.

If you're hoping that FP refactoring, like a magic silver bullet, will quickly transform your code to be more graceful, elegant, clever, resilient, and concise -- that it will come easy in the short term -- unfortunately that's just not a realistic expectation.

FP is a very different way of thinking about how code should be structured, to make the flow of data much more obvious and to help your reader follow your thinking. It will take time. This effort is eminently worthwhile, but it can be an arduous journey.

It still often takes me multiple attempts at refactoring a snippet of imperative code into more declarative FP, before I end up with something that's clear enough for me to understand later. I've found converting to FP is a slow iterative process rather than a quick binary flip from one paradigm to another.

I also apply the "teach it later" test to every piece of code I write. After I've written a piece of code, I leave it alone for a few hours or days, then come back and try to read it with fresh eyes, and pretend as if I need to teach or explain it to someone else. Usually, it's jumbled and confusing the first few passes, so I tweak it and repeat!

I'm not trying to dampen your spirits. I really want you to hack through these weeds. I am glad I did it. I can finally start to see the curve bending upward toward improved readability. The effort has been worth it. It will be for you, too.

## Perspective

Most other FP texts seem to take a top-down approach, but we're going to go the opposite direction: working from the ground up, we'll uncover the basic foundational principles that I believe formal FPers would admit are the scaffolding for everything they do. But for the most part we'll stay arm's length away from most of the intimidating terminology or mathematical notation that can so easily frustrate learners.

I believe it's less important what you call something and more important that you understand what it is and how it works. That's not to say there's no importance to shared terminology -- it undoubtedly eases communication among seasoned professionals. But for the learner, I've found it can be distracting.

So this book will try to focus more on the base concepts and less on the fancy fluff. That's not to say there won't be terminology; there definitely will be. But don't get too wrapped up in the sophisticated words. Wherever necessary, look beyond them to the ideas.

I call the less formal practice herein "Functional-Light Programming" because I think where the formalism of true FP suffers is that it can be quite overwhelming if you're not already accustomed to formal thought. I'm not just guessing; this is my own personal story. Even after teaching FP and writing this book, I can still say that the formalism of terms and notation in FP is very, very difficult for me to process. I've tried, and tried, and I can't seem to get through much of it.

I know many FPers who believe that the formalism itself helps learning. But I think there's clearly a cliff where that only becomes true once you reach a certain comfort with the formalism. If you happen to already have a math background or even some flavors of CS experience, this may come more naturally to you. But some of us don't, and no matter how hard we try, the formalism keeps getting in the way.

So this book introduces the concepts that I believe FP is built on, but comes at it by giving you a boost from below to climb up the cliff wall, rather than condescendingly shouting down at you from the top, prodding you to just figure out how to climb as you go.

## How to Find Balance

If you've been around programming for very long, chances are you've heard the phrase "YAGNI" before: "You Ain't Gonna Need It". This principle primarily comes from extreme programming, and stresses the high risk and cost of building a feature before it's needed.

Sometimes we guess we'll need a feature in the future, build it now believing it'll be easier to do as we build other stuff, then realize we guessed wrong and the feature wasn't needed, or needed to be quite different. Other times we guess right, but build a feature too early, and suck up time from the features that are genuinely needed now; we incur an opportunity cost in diluting our energy.

YAGNI challenges us to remember: even if it's counterintuitive in a situation, we often should postpone building something until it's presently needed. We tend to exaggerate our mental estimates of the future refactoring cost of adding it later when it is needed. Odds are, it won't be as hard to do later as we might assume.

As it applies to functional programming, I would offer this admonition: there will be plenty of interesting and compelling patterns discussed in this text, but just because you find some pattern exciting to apply, it may not necessarily be appropriate to do so in a given part of your code.

This is where I will differ from many formal FPers: just because you *can* apply FP to something doesn't mean you *should* apply FP to it. Moreover, there are many ways to slice a problem, and even though you may have learned a more sophisticated approach that is more "future-proof" to maintenance and extensibility, a simpler FP pattern might be more than sufficient in that spot.

Generally, I'd recommend seeking balance in what you code, and to be conservative in your application of FP concepts as you get the hang of things. Default to the YAGNI principle in deciding if a certain pattern or abstraction will help that part of the code be more readable or if it's just introducing clever sophistication that isn't (yet) warranted.

> Reminder, any extensibility point that’s never used isn’t just wasted effort, it’s likely to also get in your way as well
>
> Jeremy D. Miller @jeremydmiller 2/20/15
>
> https://twitter.com/jeremydmiller/status/568797862441586688

Remember, every single line of code you write has a reader cost associated with it. That reader may be another team member, or even your future self. Neither of those readers will be impressed with overly clever, unnecessary sophistication just to show off your FP prowess.

The best code is the code that is most readable in the future because it strikes exactly the right balance between what it can/should be (idealism) and what it must be (pragmatism).

## Resources

I have drawn on a great many different resources to be able to compose this text. I believe you, too, may benefit from them, so I wanted to take a moment to point them out.

### Books

Some FP/JavaScript books that you should definitely read:

* [Professor Frisby's Mostly Adequate Guide to Functional Programming](https://drboolean.gitbooks.io/mostly-adequate-guide/content/ch1.html) by [Brian Lonsdorf](https://twitter.com/drboolean)
* [JavaScript Allongé](https://leanpub.com/javascriptallongesix) by [Reg Braithwaite](https://twitter.com/raganwald)
* [Functional JavaScript](http://shop.oreilly.com/product/0636920028857.do) by [Michael Fogus](https://twitter.com/fogus)

### Blogs/sites

Some other authors and content you should check out:

* [Fun Fun Function Videos](https://www.youtube.com/watch?v=BMUiFMZr7vk) by [Mattias P Johansson](https://twitter.com/mpjme)
* [Awesome FP JS](https://github.com/stoeffel/awesome-fp-js)
* [Kris Jenkins](http://blog.jenkster.com/2015/12/what-is-functional-programming.html)
* [Eric Elliott](https://medium.com/@_ericelliott)
* [James A Forbes](https://james-forbes.com/)
* [James Longster](https://github.com/jlongster)
* [André Staltz](http://staltz.com/)
* [Functional Programming Jargon](https://github.com/hemanth/functional-programming-jargon#functional-programming-jargon)
* [Functional Programming Exercises](https://github.com/InceptionCode/Functional-Programming-Exercises)

### Libraries

The code snippets in this book largely do not rely on libraries. Each operation that we discover, we'll derive how to implement it in standalone, plain ol' JavaScript. However, as you begin to build more of your real code with FP, you'll soon want a library to provide optimized and highly reliable versions of these commonly accepted utilities.

By the way, you need to check the documentation for the library functions you use to ensure you know how they work. There will be a lot of similarities in many of them to the code we build on in this text, but there will undoubtedly be some differences, even between popular libraries.

Here are a few popular FP libraries for JavaScript that are a great place to start your exploration with:

* [Ramda](http://ramdajs.com)
* [lodash/fp](https://github.com/lodash/lodash/wiki/FP-Guide)
* [functional.js](http://functionaljs.com/)
* [Immutable.js](https://github.com/facebook/immutable-js)

[Appendix C takes a deeper look at these libraries](apC.md/#stuff-to-investigate) and others.

## Summary

You may have a variety of reasons for starting to read this book, and different expectations of what you'll get out of it. This chapter has explained why I want you to read the book and what I want you to get out of the journey. It also helps you articulate to others (like your fellow developers) why they should come on the journey with you!

Functional programming is about writing code that is based on proven principles so we can gain a level of confidence and trust over the code we write and read. We shouldn't be content to write code that we anxiously *hope* works, and then abruptly breathe a sigh of relief when the test suite passes. We should *know* what it will do before we run it, and we should be absolutely confident that we've communicated all these ideas in our code for the benefit of other readers (including our future selves).

This is the heart of Functional-Light JavaScript. The goal is to learn to effectively communicate with our code but not have to suffocate under mountains of notation or terminology to get there.

The journey to learning functional programming starts with deeply understanding the nature of what a function is. That's what we tackle in the next chapter.

----

<a name="footnote-1"><sup>1</sup></a>Buse, Raymond P. L., and Westley R. Weimer. “Learning a Metric for Code Readability.” IEEE Transactions on Software Engineering, IEEE Press, July 2010, dl.acm.org/citation.cfm?id=1850615.
