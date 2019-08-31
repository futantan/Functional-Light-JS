# JavaScript 函数式编程之光
# 第二章：函数的本质

函数式编程*并不意味着只是用 `function` 关键字编程*。如果真的这么容易的话，本书到这就可以结束啦！实际上，函数确实*是*函数式编程的核心。正确使用函数，是通往函数式的必经之路。

但是你确定明白 *function* 的真正含义吗？

在本章，我们会探索函数的方方面面，从而为本书的其余部分奠定基础。实际上这是对必须了解的函数知识的回顾，每个人包括非函数式程序员都应该有所了解。如果你想要充分掌握和使用函数式编程概念的话，就必须对函数了如指掌。

都搜一下精神，因为函数的知识点可能比你想象的要多的多。

## 函数是什么？

“函数是什么”这个问题的答案似乎很明显：函数是一个可以被执行一次或多次的代码的集合。

虽然这个定义是合理的，但是缺少了对函数式编程中*函数*本质的描述。因此，让我们更加深入地来探究函数的本质。

### 简要的数学回顾

我知道之前承诺过要尽可能地不谈及数学，但是在继续之前请给我一点时间允许我快速过一下代数中关于函数和几何的一些基本内容。

你还记得在学校里许过的关于 `f(x)` 的内容吗？还记得等式 `y = f(x)` 吗？

假设方程的定义如下：<code>f(x) = 2x<sup>2</sup> + 3</code>。这代表什么意思？等式的方程图像又代表什么？图像如下：

<p align="center">
    <img src="images/fig1.png" width="40%">
</p>

你可能注意到，对于任何的 `x` 值，比如 `2`，如果将其代入等式，会得到 `11`。`11` 代表什么？它是 `f(x)` 的返回值，也就是 `y` 的值。

换句话说，我们可以将输入和输出值对应到图中曲线上的点 `(2,11)`。对于每一个输入 `x`，我们可以得到一个与之对应的 `y` 值，类似的还有 `(0,3)`，`(-1,5)` 等等。将所有的这些点连在一起，就可以得到上图中的抛物线曲线图。

那么这些与函数式编程有什么关系呢？

在数学中，函数总是接受输入，同时有输出值。在函数式编程中你经常听到的一个术语是“态射（morphism）”；这个名词很花俏，它描述了一个集合中的值映射到另一个集合中，例如一个函数的输入到输入的映射。

在代数数学中，这些输入和输出通常被解释为绘制图形的坐标分量。然而在我们的程序中，我们定义的函数可能有各种各样的输入和输出，也很少被解释为图形上的绘制的曲线。

### 函数和过程

那为什么我们还要讨论数学和函数图象呢？因为函数式编程中函数的本质是数学意义上的*函数*。

而函数有一个或多个输入，并且总是有一个`返回`值。

如果你想使用函数式编程，**你应该尽可能地使用我们这里所说的函数**，并且尽可能地避免使用过程。所编写的所有 `function` 都应该有输入和输出。

为何？这个问题的答案是多方面的，我们会在本书中慢慢揭示。

## 函数输入

到目前位置，我们可以得出结论：函数必须具有输入。让我们来深入探究函数输入的工作原理。

有时人们会将函数的输入称为“实参（arguments）”有时也称为“形参（parameters）”。这些究竟是怎么回事？

*实参*是传入函数的值，*形参*指代这些传入的参数的变量名称。例如：

```js
function foo(x,y) {
    // ..
}

var a = 3;

foo( a, a * 2 );
```

`a` 和 `a * 2` （实际上是 `a * 2` 的结果，也就是 `6`）是 `foo(..)` 的实参。`x` 和 `y` 是接收实参的形参，这里分别代表 `3` 和 `6`。

**注意：**在 JavaScript 中，*实参*和*形参*的数量并不要求严格匹配。如果你传递的*实参*个数超过所声明的*形参*的话，超过的参数也不会有什么影响。这些多余的参数有多种获取的方式，例如你可能听过的 `arguments` 对象。如果传入的*实参*个数少于所声明的*形参*个数，未匹配到的参数会被认为是 “undefined”，表示这些参数在函数作用域内有值，只不过值是 `undefined`。

### 默认参数

从 ES6 开始，参数可以声明*默认值*。当拥有默认值的参数没有传入或者传入的值为 `undefined` 的时候，则使用默认的赋值表达式进行替换。

考虑如下代码：

```js
function foo(x = 3) {
    console.log( x );
}

foo();                  // 3
foo( undefined );       // 3
foo( null );            // null
foo( 0 );               // 0
```

给函数参数加上默认值可以提高函数度易用性，也不失为一个好的实践。但是，默认参数也会导致阅读和理解成本的增加，需要搞清楚函数的最终参数究竟是什么。在使用这个功能的时候，需要细细思忖。

### 入参计数

函数所“期望”的参数个数，也就是你想要传递给该函数的实参数量，取决于函数声明时的参数数量：

```js
function foo(x,y,z) {
    // ..
}
```

`foo(..)` *期望*有三个参数，因为在它声明三个形参。参数个数有个专用的术语：arity。arity 表示函数声明中的形参个数。`foo(..)` 的 arity 是 `3`。

除此之外，arity 为 1 的函数也被称为“一元”函数，arity 为 2 的函数被称为“二元”函数，arity 为 3 或者更高的被称为“n 元（n-ary）”函数。

你可能想要在运行时来检查程序中一个函数的 arity。可以通过使用该函数引用的 `length` 属性来做到：

```js
function foo(x,y,z) {
    // ..
}

foo.length;             // 3
```

作为一个在运行时获取 arity 的一个可能的场景，想象一下一段代码有可能持有不同情况的函数引用，需要根据函数 arity 的不同，传入不同的参数值。

例如，假设一个函数引用 `fn`，它可能期望一个，两个或者三个参数，但是我们总是希望将变量 `x` 作为最后一个参数传入。

```js
// `fn` 是某个函数的引用
// `x` 中存放了某个值

if (fn.length == 1) {
    fn( x );
}
else if (fn.length == 2) {
    fn( undefined, x );
}
else if (fn.length == 3) {
    fn( undefined, undefined, x );
}
```

**提示：** 函数的 `length` 属性是只读的，在函数声明的时候就已确定。它可以被认为是描述函数使用意图的一个元数据。

需要注意的是，某些类型的参数列表的使用方式会使函数 `length` 属性和你的预期不符：

```js
function foo(x,y = 2) {
    // ..
}

function bar(x,...args) {
    // ..
}

function baz( {a,b} ) {
    // ..
}

foo.length;             // 1
bar.length;             // 1
baz.length;             // 1
```

该如何计算当前的函数调用所接收到的参数个数呢？这个问题曾经很简单，但是现在的情况会稍微复杂一些。每个函数都拥有一个类数组的 `arguments` 对象，该对象持有每个实参的引用。你可以使用 `arguments` 的 `length` 属性来确定实际传入的参数个数：

```js
function foo(x,y,z) {
    console.log( arguments.length );
}

foo( 3, 4 );    // 2
```

从 ES5 开始（特别是在严格模式下），有些人认为 `arguments` 被弃用了；许多人也尽可能避免使用它。在 JS 中，无论新功能对未来进展有多大的助益，我们都“永远不会”破坏向后兼容性，所以 `arguments` 永远都不会被移出。但是现在通常建议尽可能地避免使用它。

但是，我认为在这种情况下，也仅当在这种情况下是可以继续使用 `arguments.length` 来获取实参个数的。 JS 的未来版本可能会添加在不使用 `arguments.length` 情况下获取实参数量的功能；如果有这种新功能的话，我们就可以彻底抛弃 `arguments` 了！

要小心：**永远不要**通过位置索引访问，例如 `arguments[1]`。只使用 `arguments.length`，除非走投无路。

除此之外，如何才能访问所有声明参数之外位置传递的参数呢？马上我们会来揭晓答案；但在此之前，退一步认真问问自己，“我为什么需要这样做？”。花一分钟时间，仔细思考一下。

这种场景应该极少发生；在编写函数时，它不应该是你期望或者依赖的东西。如果你发现你在这样的场景中，请花额外的 20 分钟时间来尝试使用另一种方式来设计该函数。即使是个例外场景，也请将该额外参数变为命名参数。

接受不确定数量的参数的函数被称为可变参数（variadic）函数。有些人更喜欢这种函数的设计，但是你会发现函数式编程者会想要尽量避免这种设计。

OK，在这一点上已经唠叨的够多的了。

假设你确实需要以类似数组的方式来获取某个位置的参数情况，这有可能是因为想要获取的参数没有对应的形参。我们该如何做到呢？

ES6 to the rescue! Let's declare our function with the `...` operator -- variously referred to as "spread", "rest", or (my preference) "gather":
ES6 前来救驾！让我们使用 `...` 操作符——有多种称呼，像“spred”，“rest”或者（我更喜欢的）“gather”。（译者注：中文一般称为扩展）

```js
function foo(x,y,z,...args) {
    // ..
}
```

注意到参数列表中的 `...args` 没有？这是一个 ES6 的声明形式，告诉引擎将所有未赋值给形参的剩余的参数（如果有的话）收集到一个名为 `args` 的真数组中。`args` 将总会是一个数组，即使是空的。但是它**不会**包含 `x`, `y` 和 `z` 对应的参数，只有包含除了前三个之外的所有参数：

```js
function foo(x,y,z,...args) {
    console.log( x, y, z, args );
}

foo();                  // undefined undefined undefined []
foo( 1, 2, 3 );         // 1 2 3 []
foo( 1, 2, 3, 4 );      // 1 2 3 [ 4 ]
foo( 1, 2, 3, 4, 5 );   // 1 2 3 [ 4, 5 ]
```

所以，如果你*确定*想要设计一个函数能够接受任意个数的参数的话，可以在最后加上 `...args` （或者其他你喜欢的名字）。现在，你拥有了一个真正的数组，能够从中获取参数，之前的方式是被废弃的，也不是那么的优雅。

请注意 `4` 位于 `args` 中的第 `0` 个位置而不是第 `3` 个。`args` 的 `length` 值不会将之前的 `1`，`2` 和 `3` 3个值算在内。`...args` 将其余的所有值，除了 `x`，`y` 和 `z` 收集在一起。

你*可以*在参数列表中使用 `...` 操作符，即使没有声明其他形参：

```js
function foo(...args) {
    // ..
}
```

现在 `args` 将会是完整的参数数组，无论参数是什么，你都可以使用 `args.length` 来获取传入参数的确切数量。如果你愿意的话，你可以安全地使用 `args[1]` 或者 `args[317]`。但是请不要传入像 318 个这么多的参数。

### 实参数组

如果你想要将一个数组内的值作为参数传递给一个函数，该怎么做？

```js
function foo(...args) {
    console.log( args[3] );
}

var arr = [ 1, 2, 3, 4, 5 ];

foo( ...arr );                      // 4
```

我们的新朋友 `...` 可以大显神威了，但是现在不仅是用在形参列表中；它也可以在函数调用的时候在实参列表中使用。在这种情况下，它的功能恰好相反。在形参列表中，它将参数*聚集*在一起。在实参列表中，它将参数*展开*。因此 `arr` 数组的值被展开作为每个独立的参数传入 `foo(...)` 的调用中。你能看出这与直接传入 `arr` 数组引用的区别吗？

顺便提一下，值和 `...` 可以多次交替使用，例如：

```js
var arr = [ 2 ];

foo( 1, ...arr, 3, ...[4,5] );      // 4
```

可以对称地考虑 `...` 操作符：在期望是值的时候，它将值*展开*。在赋值的时候——例如形参列表，实参会被*赋值*给形参，它会进行*收集*操作。

无论是使用 `...` 的哪种行为，它都能够让处理参数数组更加容易。使用 `slice(..)`，`concat(..)` 和 `apply(..)` 来折腾参数数组的日志已经一去不复返了。

**提示：**实际上，这些方法并非毫无用处。在本书中，我们也有一些代码依赖于它们，但是在绝大部分地方 `...` 将会是我们的不二之选，它更加声明式，也更加易读。

### Parameter Destructuring

考虑上一节中的可变参数 `foo(..)`：

```js
function foo(...args) {
    // ..
}

foo( ...[1,2,3] );
```

如果我们想改变函数的接口，使得函数调用者可以直接将数组传入而不用传入每一个参数值，该怎么办？把两处 `...` 的地方去掉即可：

```js
function foo(args) {
    // ..
}

foo( [1,2,3] );
```

代码很简单。但是如果现在我们想给所传入数组的前两个参数分别绑定变量名该怎么办呢？我们无法做到，似乎失去了这种能力。

值得庆幸的是，ES6 解构（Destructuring）能够帮助我们解决这个问题。解构允许按照一定的*模式*，按照你期望的样子从一些特定的数据结构（对象，数组等）中提取部分值，并赋值给变量。

考虑如下代码：

<a name="funcparamdestr"></a>

```js
function foo( [x,y,...args] = [] ) {
    // ..
}

foo( [1,2,3] );
```

注意到形参列表中的 `[ .. ]` 了吗？这称为数组参数结构。

在这个例子中，解构告诉引擎在该参数位置，期望的是一个数组。这个模式的意思是说，将数组中的第一个值赋值给局部参数变量 `x`，将第二个值赋值给 `y`，剩下的所有值*收集*起来并赋值给 `args`

### 声明式风格的重要性

与之前我们在 `foo(..)` 中使用的解构相比，我们也可以使用手动处理参数：

```js
function foo(params) {
    var x = params[0];
    var y = params[1];
    var args = params.slice( 2 );

    // ..
}
```

但是这里我们需要强调在[第一章](ch1_zh.md/#可读性)中简要介绍的原则：声明式的代码比命令式代码的可读性更好。

声明式代码（例如前面 `foo(..)` 代码片段中的解构，以及 `...` 操作符的使用）关注的是一段代码的结果应该是什么。

命令式代码（例如第二个代码片段中的手动赋值）则更侧重于如果获取结果。如果你以后再来阅读这段命令式的代码，你必须在心中讲全部代码都执行一遍从而理解输出结果会是什么。结果就藏在*编码*里面，但是由于被*如何做*的细节笼罩，所以并不是那么清晰易懂。

第一个 `foo(..)` 的版本被认为可读性更高，因为解构隐藏了*如何*管理参数输入这些不必要的细节；读者可以更容易地只关注于使用这些参数*做什么*。让读者能够更专注于全面滴理解代码在做什么，才是最重要的。

无论我们所使用的编程语言，库或者框架是什么，以及其支持程度如何，**我们都应该竭尽所能编写声明式，能够自解释的代码。**

## 具名参数

就像我们可以解构数组参数一样，我们也可以对对象进行解构：

```js
function foo( {x,y} = {} ) {
    console.log( x, y );
}

foo( {
    y: 3
} );                    // undefined 3
```

我们将对象作为唯一的输入参数，它被解构为两个独立的参数变量 `x` 和 `y`，值为所传入对象中同名属性对应的值。输入对象中是否有 `x` 这个属性无关紧要，如果没有的话，解构后的 `x` 会和我们所期望的一样，是 `undefined`。

但是我真正想让你注意的是传递给 `foo(..)` 进行解构的参数对象。

通过普通的函数调用方式，像 `foo(undefined, 3)` 实参位置和形参匹配；我们将 `3` 放在了第二个参数位置上，从而将其赋值给 `y`。而在使用参数解构的函数调用中，属性名可以指明实参 `3` 的值会赋值给哪一个形参（`y`）。

在函数调用中我们不必指明 `x` 的值，因为我们实际上并不关心 `x`。这里只是省略了它，而不用做必须传递一个 `undefined` 作为占位这样令人分心的事。

有些语言直接具有这种编程特性：具名参数。也就是说在调用的时候，标明了输入值会映射到哪个对应的参数之上。JavaScript 中没有具名参数的特性，但是形参对象解构是最佳的替代选项。

另外一个使用对象解构传递多个可能参数的好处与函数式编程有关，那就是函数有且仅有一个形参（那个对象），能够更容易地与其他函数的单返回值进行函数组合。我们会在[第四章](ch4_zh.md)中讲解更多内容。

### 无序参数

具名参数由于指定为对象的属性名，另一个由此带来的主要好处是无需特定的顺序。这意味着我们可以按照想要的任何顺序传入输入：

```js
function foo( {x,y} = {} ) {
    console.log( x, y );
}

foo( {
    y: 3
} );                    // undefined 3
```

要想跳过形参 `x` 我们可以直接省略它。如果关心 `x` 的内容的话，可以指定它的值，也可以将它列在对象字面量的 `y` 之后。调用者不会因为有 `undefined` 这样的占位值而跳过某些参数。

从可读性的角度来说，具名参数更加灵活，也更具吸引力，尤其是当所讨论的函数涉及三个，四个或者更多入参的时候。

**提示：**如果你觉得这种风格的函数参数很有用，或者很感兴趣，可以查看[附录 C 中的 FPO 库](apC_zh.md/#bonus-fpo)。

## 函数输出

聊完了函数的输入，来看看函数输出。

在 JavaScript 中，函数总是具有一个返回值。下面的三个函数具有等价的 `return` 行为：

```js
function foo() {}

function bar() {
    return;
}

function baz() {
    return undefined;
}
```

如果没有 `return` 或者只有空的 `return` 语句的话，`undefined` 会作为隐式的返回值。

函数式编程中函数的定义要求尽可能地使用函数，而不是过程。为了尽可能地遵循这一原则，我们的函数应该总是具有返回值，也就意味着它们应该显式地`返回`一个值，并且通常来说不为 `undefined`。

`return` 语句只能返回一个值。因此如果你的函数需要返回多个值，那么唯一可能的选择是将它们放在数组或者对象这样的复合值中。

```js
function foo() {
    var retValue1 = 11;
    var retValue2 = 31;
    return [ retValue1, retValue2 ];
}
```

然后，我们将 `foo()` 返回数组中的值分别赋给 `x` 和 `y`：

```js
var [ x, y ] = foo();
console.log( x + y );           // 42
```

将多个值放在数组（或者对象）中然后返回，然后使用解构将值分别取出再赋值，是一种函数返回多个值的一种处理方式。

**提示：**建议在使用多返回值函数的时候，多考虑一下是否可以通过重构来避免这种情况的发生，是否可以将其分解成两个或者更多单一职责的小函数。有时候可以避免，有时候不行；但是至少应该在使用之前，细细思忖。

### Early Returns

The `return` statement doesn't just return a value from a function. It's also a flow control structure; it ends the execution of the function at that point. A function with multiple `return` statements thus has multiple possible exit points, meaning that it may be harder to read a function to understand its output behavior if there are many paths that could produce that output.

Consider:

```js
function foo(x) {
    if (x > 10) return x + 1;

    var y = x / 2;

    if (y > 3) {
        if (x % 2 == 0) return x;
    }

    if (y > 1) return y;

    return x;
}
```

Pop quiz: without cheating and running this code in your browser, what does `foo(2)` return? What about `foo(4)`? And `foo(8)`? And `foo(12)`?

How confident are you in your answers? How much mental tax did you pay to get those answers? I got it wrong the first two times I tried to think it through, and I wrote it!

I think part of the readability problem here is that we're using `return` not just to return different values, but also as a flow control construct to quit a function's execution early in certain cases. There are obviously better ways to write that flow control (the `if` logic, etc.), but I also think there are ways to make the output paths more obvious.

**Note:** The answers to the pop quiz are `2`, `2`, `8`, and `13`.

Consider this version of the code:

```js
function foo(x) {
    var retValue;

    if (retValue == undefined && x > 10) {
        retValue = x + 1;
    }

    var y = x / 2;

    if (y > 3) {
        if (retValue == undefined && x % 2 == 0) {
            retValue = x;
        }
    }

    if (retValue == undefined && y > 1) {
        retValue = y;
    }

    if (retValue == undefined) {
        retValue = x;
    }

    return retValue;
}
```

This version is unquestionably more verbose. But I would argue it's slightly simpler logic to follow, because every branch where `retValue` can get set is *guarded* by the condition that checks if it's already been set.

Rather than `return`ing from the function early, we used normal flow control (`if` logic) to determine the `retValue`'s assignment. At the end, we simply `return retValue`.

I'm not unconditionally saying that you should always have a single `return`, or that you should never do early `return`s, but I do think you should be careful about the flow control part of `return` creating more implicitness in your function definitions. Try to figure out the most explicit way to express the logic; that will often be the best way.

### Un`return`ed Outputs

One technique that you've probably used in most code you've written, and maybe didn't even think about it much, is to have a function output some or all of its values by simply changing variables outside itself.

Remember our <code>f(x) = 2x<sup>2</sup> + 3</code> function from earlier in the chapter? We could have defined it like this in JS:

```js
var y;

function f(x) {
    y = (2 * Math.pow( x, 2 )) + 3;
}

f( 2 );

y;                      // 11
```

I know this is a silly example; we could just as easily have `return`d the value instead of setting it into `y` from within the function:

```js
function f(x) {
    return (2 * Math.pow( x, 2 )) + 3;
}

var y = f( 2 );

y;                      // 11
```

Both functions accomplish the same task, so is there any reason we should pick one version over the other? **Yes, absolutely.**

One way to explain the difference is that the `return` in the latter version signals an explicit output, whereas the `y` assignment in the former is an implicit output. You may already have some intuition that guides you in such cases; typically, developers prefer explicit patterns over implicit ones.

But changing a variable in an outer scope, as we did with the `y` assignment inside of `foo(..)`, is just one way of achieving an implicit output. A more subtle example is making changes to non-local values via reference.

Consider:

```js
function sum(list) {
    var total = 0;
    for (let i = 0; i < list.length; i++) {
        if (!list[i]) list[i] = 0;

        total = total + list[i];
    }

    return total;
}

var nums = [ 1, 3, 9, 27, , 84 ];

sum( nums );            // 124
```

The most obvious output from this function is the sum `124`, which we explicitly `return`ed. But do you spot the other output? Try that code and then inspect the `nums` array. Now do you spot the difference?

Instead of an `undefined` empty slot value in position `4`, now there's a `0`. The harmless looking `list[i] = 0` operation ended up affecting the array value on the outside, even though we operated on a local `list` parameter variable.

Why? Because `list` holds a reference-copy of the `nums` reference, not a value-copy of the `[1,3,9,..]` array value. JavaScript uses references and reference-copies for arrays, objects, and functions, so we may create an accidental output from our function all too easily.

This implicit function output has a special name in the FP world: side effects. And a function that has *no side effects* also has a special name: pure function. We'll talk a lot more about these in [Chapter 5](ch5.md), but the punchline is that we'll want to prefer pure functions and avoid side effects wherever possible.

## Functions of Functions

Functions can receive and return values of any type. A function that receives or returns one or more other function values has the special name: higher-order function.

Consider:

```js
function forEach(list,fn) {
    for (let v of list) {
        fn( v );
    }
}

forEach( [1,2,3,4,5], function each(val){
    console.log( val );
} );
// 1 2 3 4 5
```

`forEach(..)` is a higher-order function because it receives a function as an argument.

A higher-order function can also output another function, like:

```js
function foo() {
    return function inner(msg){
        return msg.toUpperCase();
    };
}

var f = foo();

f( "Hello!" );          // HELLO!
```

`return` is not the only way to "output" an inner function:

```js
function foo() {
    return bar( function inner(msg){
        return msg.toUpperCase();
    } );
}

function bar(func) {
    return func( "Hello!" );
}

foo();                  // HELLO!
```

Functions that treat other functions as values are higher-order functions by definition. FPers write these all the time!

### Keeping Scope

One of the most powerful things in all of programming, and especially in FP, is how a function behaves when it's inside another function's scope. When the inner function makes reference to a variable from the outer function, this is called closure.

Defined pragmatically:

> Closure is when a function remembers and accesses variables from outside of its own scope, even when that function is executed in a different scope.

Consider:

```js
function foo(msg) {
    var fn = function inner(){
        return msg.toUpperCase();
    };

    return fn;
}

var helloFn = foo( "Hello!" );

helloFn();              // HELLO!
```

The `msg` parameter variable in the scope of `foo(..)` is referenced inside the inner function. When `foo(..)` is executed and the inner function is created, it captures the access to the `msg` variable, and retains that access even after being `return`ed.

Once we have `helloFn`, a reference to the inner function, `foo(..)` has finished and it would seem as if its scope should have gone away, meaning the `msg` variable would no longer exist. But that doesn't happen, because the inner function has a closure over `msg` that keeps it alive. The closed over `msg` variable survives for as long as the inner function (now referenced by `helloFn` in a different scope) stays around.

Let's look at a few more examples of closure in action:

```js
function person(name) {
    return function identify(){
        console.log( `I am ${name}` );
    };
}

var fred = person( "Fred" );
var susan = person( "Susan" );

fred();                 // I am Fred
susan();                // I am Susan
```

The inner function `identify()` has closure over the parameter `name`.

The access that closure enables is not restricted to merely reading the variable's original value -- it's not just a snapshot but rather a live link. You can update the value, and that new current state remains remembered until the next access:

```js
function runningCounter(start) {
    var val = start;

    return function current(increment = 1){
        val = val + increment;
        return val;
    };
}

var score = runningCounter( 0 );

score();                // 1
score();                // 2
score( 13 );            // 15
```

**Warning:** For reasons that we'll explore in more depth later in the book, this example of using closure to remember a state that changes (`val`) is probably something you'll want to avoid where possible.

If you have an operation that needs two inputs, one of which you know now but the other will be specified later, you can use closure to remember the first input:

```js
function makeAdder(x) {
    return function sum(y){
        return x + y;
    };
}

// we already know `10` and `37` as first inputs, respectively
var addTo10 = makeAdder( 10 );
var addTo37 = makeAdder( 37 );

// later, we specify the second inputs
addTo10( 3 );           // 13
addTo10( 90 );          // 100

addTo37( 13 );          // 50
```

Normally, a `sum(..)` function would take both an `x` and `y` input to add them together. But in this example we receive and remember (via closure) the `x` value(s) first, while the `y` value(s) are separately specified later.

**Note:** This technique of specifying inputs in successive function calls is very common in FP, and comes in two forms: partial application and currying. We'll dive into them more thoroughly in [Chapter 3](ch3.md/#some-now-some-later).

Of course, since functions are just values in JS, we can remember function values via closure:

```js
function formatter(formatFn) {
    return function inner(str){
        return formatFn( str );
    };
}

var lower = formatter( function formatting(v){
    return v.toLowerCase();
} );

var upperFirst = formatter( function formatting(v){
    return v[0].toUpperCase() + v.substr( 1 ).toLowerCase();
} );

lower( "WOW" );             // wow
upperFirst( "hello" );      // Hello
```

Instead of distributing/repeating the `toUpperCase()` and `toLowerCase()` logic all over our code, FP encourages us to create simple functions that encapsulate -- a fancy way of saying wrapping up -- that behavior.

Specifically, we create two simple unary functions `lower(..)` and `upperFirst(..)`, because those functions will be much easier to wire up to work with other functions in the rest of our program.

**Tip:** Did you spot how `upperFirst(..)` could have used `lower(..)`?

We'll use closure heavily throughout the rest of the text. It may just be the most important foundational practice in all of FP, if not programming as a whole. Make sure you're really comfortable with it!

## Syntax

Before we move on from this primer on functions, let's take a moment to discuss their syntax.

More than many other parts of this text, the discussions in this section are mostly opinion and preference, whether you agree with the views presented here or take opposite ones. These ideas are highly subjective, though many people seem to feel rather absolutely about them.

Ultimately, you get to decide.

### What's in a Name?

Syntactically speaking, function declarations require the inclusion of a name:

```js
function helloMyNameIs() {
    // ..
}
```

But function expressions can come in both named and anonymous forms:

```js
foo( function namedFunctionExpr(){
    // ..
} );

bar( function(){    // <-- look, no name!
    // ..
} );
```

What exactly do we mean by anonymous, by the way? Specifically, functions have a `name` property that holds the string value of the name the function was given syntactically, such as `"helloMyNameIs"` or `"namedFunctionExpr"`. This `name` property is most notably used by the console/developer tools of your JS environment to list the function when it participates in a stack trace (usually from an exception).

Anonymous functions are generally displayed as `(anonymous function)`.

If you've ever had to debug a JS program from nothing but a stack trace of an exception, you probably have felt the pain of seeing `(anonymous function)` appear line after line. This listing doesn't give a developer any clue whatsoever as to the path the exception came from. It's not doing the developer any favors.

If you name your function expressions, the name is always used. So if you use a good name like `handleProfileClicks` instead of `foo`, you'll get much more helpful stack traces.

As of ES6, anonymous function expressions are in certain cases aided by *name inferencing*. Consider:

```js
var x = function(){};

x.name;         // x
```

If the engine is able to guess what name you *probably* want the function to take, it will go ahead and do so.

But beware, not all syntactic forms benefit from name inferencing. Probably the most common place a function expression shows up is as an argument to a function call:

```js
function foo(fn) {
    console.log( fn.name );
}

var x = function(){};

foo( x );               // x
foo( function(){} );    //
```

When the name can't be inferred from the immediate surrounding syntax, it remains an empty string. Such a function will be reported as `(anonymous function)` in a stack trace should one occur.

There are other benefits to a function being named besides the debugging question. First, the syntactic name (aka lexical name) is useful for internal self-reference. Self-reference is necessary for recursion (both sync and async) and also helpful with event handlers.

Consider these different scenarios:

```js
// sync recursion:
function findPropIn(propName,obj) {
    if (obj == undefined || typeof obj != "object") return;

    if (propName in obj) {
        return obj[propName];
    }
    else {
        for (let prop of Object.keys( obj )) {
            let ret = findPropIn( propName, obj[prop] );
            if (ret !== undefined) {
                return ret;
            }
        }
    }
}
```

```js
// async recursion:
setTimeout( function waitForIt(){
    // does `it` exist yet?
    if (!o.it) {
        // try again later
        setTimeout( waitForIt, 100 );
    }
}, 100 );
```

```js
// event handler unbinding
document.getElementById( "onceBtn" )
    .addEventListener( "click", function handleClick(evt){
        // unbind event
        evt.target.removeEventListener( "click", handleClick, false );

        // ..
    }, false );
```

In all these cases, the named function's lexical name was a useful and reliable self-reference from inside itself.

Moreover, even in simple cases with one-liner functions, naming them tends to make code more self-explanatory and thus easier to read for those who haven't read it before:

```js
people.map( function getPreferredName(person){
    return person.nicknames[0] || person.firstName;
} )
// ..
```

The function name `getPreferredName(..)` tells the reader something about what the mapping operation is intending to do that is not entirely obvious from just its code. This name label helps the code be more readable.

Another place where anonymous function expressions are common is with immediately invoked function expressions (IIFEs):

```js
(function(){

    // look, I'm an IIFE!

})();
```

You virtually never see IIFEs using names for their function expressions, but they should. Why? For all the same reasons we just went over: stack trace debugging, reliable self-reference, and readability. If you can't come up with any other name for your IIFE, at least use the word IIFE:

```js
(function IIFE(){

    // You already knew I was an IIFE!

})();
```

What I'm getting at is there are multiple reasons why **named functions are always more preferable to anonymous functions.** As a matter of fact, I'd go so far as to say that there's basically never a case where an anonymous function is more preferable. They just don't really have any advantage over their named counterparts.

It's incredibly easy to write anonymous functions, because it's one less name we have to devote our mental attention to figuring out.

I'll be honest; I'm as guilty of this as anyone. I don't like to struggle with naming. The first few names I come up with for a function are usually bad. I have to revisit the naming over and over. I'd much rather just punt with a good ol' anonymous function expression.

But we're trading ease-of-writing for pain-of-reading. This is not a good trade-off. Being lazy or uncreative enough to not want to figure out names for your functions is an all too common, but poor, excuse for using anonymous functions.

**Name every single function.** And if you sit there stumped, unable to come up with a good name for some function you've written, I'd strongly suggest you don't fully understand that function's purpose yet -- or it's just too broad or abstract. You need to go back and re-design the function until this is more clear. And by that point, a name will become more apparent.

In my practice, if I don't have a good name to use for a function, I name it `TODO` initially. I'm certain that I'll at least catch that later when I search for "TODO" comments before committing code.

I can testify from my own experience that in the struggle to name something well, I usually have come to understand it better, later, and often even refactor its design for improved readability and maintainability.

This time investment is well worth it.

### Functions Without `function`

So far we've been using the full canonical syntax for functions. But you've no doubt also heard all the buzz around the ES6 `=>` arrow function syntax.

Compare:

```js
people.map( function getPreferredName(person){
    return person.nicknames[0] || person.firstName;
} );

// vs.

people.map( person => person.nicknames[0] || person.firstName );
```

Whoa.

The keyword `function` is gone, so is `return`, the parentheses (`( )`), the curly braces (`{ }`), and the innermost semicolon (`;`). In place of all that, we used a so-called fat arrow symbol (`=>`).

But there's another thing we omitted. Did you spot it? The `getPreferredName` function name.

That's right; `=>` arrow functions are lexically anonymous; there's no way to syntactically provide it a name. Their names can be inferred like regular functions, but again, the most common case of function expression values passed as arguments won't get any assistance in that way. Bummer.

If `person.nicknames` isn't defined for some reason, an exception will be thrown, meaning this `(anonymous function)` will be at the top of the stack trace. Ugh.

Honestly, the anonymity of `=>` arrow functions is a `=>` dagger to the heart, for me. I cannot abide by the loss of naming. It's harder to read, harder to debug, and impossible to self-reference.

But if that wasn't bad enough, the other slap in the face is that there's a whole bunch of subtle syntactic variations that you must wade through if you have different scenarios for your function definition. I'm not going to cover all of them in detail here, but briefly:

```js
people.map( person => person.nicknames[0] || person.firstName );

// multiple parameters? need ( )
people.map( (person,idx) => person.nicknames[0] || person.firstName );

// parameter destructuring? need ( )
people.map( ({ person }) => person.nicknames[0] || person.firstName );

// parameter default? need ( )
people.map( (person = {}) => person.nicknames[0] || person.firstName );

// returning an object? need ( )
people.map( person =>
    ({ preferredName: person.nicknames[0] || person.firstName })
);
```

The case for excitement over `=>` in the FP world is primarily that it follows almost exactly from the mathematical notation for functions, especially in FP languages like Haskell. The shape of `=>` arrow function syntax communicates mathematically.

Digging even further, I'd suggest that the argument in favor of `=>` is that by using much lighter-weight syntax, we reduce the visual boundaries between functions which lets us use simple function expressions much like we'd use lazy expressions -- another favorite of the FPer.

I think most FPers are going to wave off the concerns I'm sharing. They love anonymous functions and they love saving on syntax. But like I said before: you decide.

**Note:** Though I do not prefer to use `=>` in practice in my production code, they are useful in quick code explorations. Moreover, we will use arrow functions in many places throughout the rest of this book -- especially when we present typical FP utilities -- where conciseness is preferred to optimize for the limited physical space in code snippets. Make your own determinations whether this approach will make your own production-ready code more or less readable.

## What's This?

If you're not familiar with the `this` binding rules in JavaScript, I recommend checking out my book *You Don't Know JS: this & Object Prototypes*. For the purposes of this section, I'll assume you know how `this` gets determined for a function call (one of the four rules). But even if you're still fuzzy on *this*, the good news is we're going to conclude that you shouldn't be using `this` if you're trying to do FP.

**Note:** We're tackling a topic that we'll ultimately conclude we shouldn't use. Why!? Because the topic of `this` has implications for other topics covered later in this book. For example, our notions of function purity are impacted by `this` being essentially an implicit input to a function (see [Chapter 5](ch5.md)). Additionally, our perspective on `this` affects whether we choose array methods (`arr.map(..)`) versus standalone utilities (`map(..,arr)`) (see [Chapter 9](ch9.md)). Understanding `this` is essential to understanding why `this` really should *not* be part of your FP!

JavaScript `function`s have a `this` keyword that's automatically bound per function call. The `this` keyword can be described in many different ways, but I prefer to say it provides an object context for the function to run against.

`this` is an implicit parameter input for your function.

Consider:

```js
function sum() {
    return this.x + this.y;
}

var context = {
    x: 1,
    y: 2
};

sum.call( context );        // 3

context.sum = sum;
context.sum();              // 3

var s = sum.bind( context );
s();                        // 3
```

Of course, if `this` can be input into a function implicitly, the same object context could be sent in as an explicit argument:

```js
function sum(ctx) {
    return ctx.x + ctx.y;
}

var context = {
    x: 1,
    y: 2
};

sum( context );
```

Simpler. And this kind of code will be a lot easier to deal with in FP. It's much easier to wire multiple functions together, or use any of the other input wrangling techniques we will get into in the next chapter, when inputs are always explicit. Doing them with implicit inputs like `this` ranges from awkward to nearly impossible depending on the scenario.

There are other tricks we can leverage in a `this`-based system, including prototype-delegation (also covered in detail in *You Don't Know JS: this & Object Prototypes*):

```js
var Auth = {
    authorize() {
        var credentials = `${this.username}:${this.password}`;
        this.send( credentials, resp => {
            if (resp.error) this.displayError( resp.error );
            else this.displaySuccess();
        } );
    },
    send(/* .. */) {
        // ..
    }
};

var Login = Object.assign( Object.create( Auth ), {
    doLogin(user,pw) {
        this.username = user;
        this.password = pw;
        this.authorize();
    },
    displayError(err) {
        // ..
    },
    displaySuccess() {
        // ..
    }
} );

Login.doLogin( "fred", "123456" );
```

**Note:** `Object.assign(..)` is an ES6+ utility for doing a shallow assignment copy of properties from one or more source objects to a single target object: `Object.assign( target, source1, ... )`.

In case you're having trouble parsing what this code does: we have two separate objects `Login` and `Auth`, where `Login` performs prototype-delegation to `Auth`. Through delegation and the implicit `this` context sharing, these two objects virtually compose during the `this.authorize()` function call, so that properties/methods on `this` are dynamically shared with the `Auth.authorize(..)` function.

*This* code doesn't fit with various principles of FP for a variety of reasons, but one of the obvious hitches is the implicit `this` sharing. We could be more explicit about it and keep code closer to FP-friendly style:

```js
// ..

authorize(ctx) {
    var credentials = `${ctx.username}:${ctx.password}`;
    Auth.send( credentials, function onResp(resp){
        if (resp.error) ctx.displayError( resp.error );
        else ctx.displaySuccess();
    } );
}

// ..

doLogin(user,pw) {
    Auth.authorize( {
        username: user,
        password: pw
    } );
}

// ..
```

From my perspective, the problem is not with using objects to organize behavior. It's that we're trying to use implicit input instead of being explicit about it. When I'm wearing my FP hat, I want to leave `this` stuff on the shelf.

## Summary

Functions are powerful.

But let's be clear what a function is. It's not just a collection of statements/operations. Specifically, a function needs one or more inputs (ideally, just one!) and an output.

Functions inside of functions can have closure over outer variables and remember them for later. This is one of the most important concepts in all of programming, and a fundamental foundation of FP.

Be careful of anonymous functions, especially `=>` arrow functions. They're convenient to write, but they shift the cost from author to reader. The whole reason we're studying FP here is to write more readable code, so don't be so quick to jump on that bandwagon.

Don't use `this`-aware functions. Just don't.

You should now be developing a clear and colorful perspective in your mind of what *function* means in Functional Programming. It's time to start wrangling functions to get them to interoperate, and the next chapter teaches you a variety of critical techniques you'll need along the way.
