# JavaScript 函数式编程之光

# 第三章：管理函数输入

[第二章](ch2.md) 探索了 JS 中 `function` 的核心本质，并为 `function` 成为函数式编程中的函数奠定了基础。但是，为了充分发挥函数式编程的威力，我们需要一些模式和实践经验，从而将函数塑造和调整为我们期望的样子。

具体来说，本章的重点将放在函数的输入上。随着在程序中不断地引入各种类型的函数，你很快就会发现有些函数的参数个数、参数顺序或参数类型并不符合我们的期望，有时我们又期望给函数指定一些输入参数，这些问题接踵而至。

实际上，有时为了提高代码可读性，你可能期望以完全隐藏输入参数的形式来定义一个函数！

这些是让函数更加*函数式*必不可少的技巧。

## 万象归一

假设你要将一个函数作为参数传给一个工具函数，而这个工具函数在调用所传入的函数时，会给多个参数。而你只希望这个函数接收一个参数。

我们可以设计一个简单的帮助程序，来将函数包装成一个只接收一个参数的函数调用。由于这种做法实际上强制将函数视为一元（unary）函数，所以我们也可以据此命名：

<a name="unary"></a>

```js
function unary(fn) {
    return function onlyOneArg(arg){
        return fn( arg );
    };
}
```

Many FPers tend to prefer the shorter `=>` arrow function syntax for such code (see [Chapter 2, "Functions without `function`"](ch2.md/#functions-without-function)), such as:

对这类代码，很多函数式程序员更倾向于使用更简洁的 `=>` 箭头函数（参考  [第二章，“没有 `function` 关键词的函数”](ch2.md/#没有 `function` 关键词的函数)））：

```js
var unary =
    fn =>
        arg =>
            fn( arg );
```

**注意：** 毫无疑问，这种写法更简洁，代码量也更少。但是我个人认为，虽然它和数学符号的表示更相近，但是这些函数都是匿名的，模糊了范围边界，使得闭包更加晦涩难懂。比起代码阅读性的丢失，这一点好处显得得不偿失。

对于 `unary(..)` 函数，一个被广泛使用的例子是 `map(..)` （参考[第九章，“Map”](ch9.md/#map))以及 `parseInt(..)` 。 `map(..)` 为列表中的每一项调用一个 mapper 函数，每次调用 mapper 函数的时候，会传入三个参数： `value`, `idx`, `arr`。

这种行为通常不是一个问题，除非这个 mapper 函数在传入过多参数的时候会出现异常行为。考虑如下代码：

```js
["1","2","3"].map( parseInt );
// [1,NaN,NaN]
```

对于函数签名 `parseInt(str,radix)` 很显然，当 `map(..)` 函数将 `index` 作为第二个参数传入的时候，会被 `parseInt(..)` 理解为 `radix`，这不是我们期望的行为。

`unary(..)` 创建一个函数，它会忽略除第一个之外的其他参数，这意味着传入的 `index` 参数不会被 `parseInt(..)` 接收到，也就不会被误认为是 `radix`：

<a name="mapunary"></a>

```js
["1","2","3"].map( unary( parseInt ) );
// [1,2,3]
```

### 一一得一

说到一元函数，函数式工具箱中还有另一个基础的实用工具，它接收一个参数，直接原封不动地返回这个值，也不做任何其他的事情：

```js
function identity(v) {
    return v;
}

// 或者使用 ES6 => 箭头函数的形式
var identity =
    v =>
        v;
```

这个工具看起来如此的简单，好像没什么用。但是即使是非常简单的函数在函数式编程的领域也可以发挥很大的作用。就像表演领域中所说的：有没小角色，只有小演员。

例如，假设你想使用正则表达式来拆分字符串，但是拆分后的得到的数组中含有一些空值。为了丢弃这些值，我们可以使用 JS 中数组的`filter(..)` 操作（参考[第九章 “过滤操作”](ch9.md/#filter)），将 `identity(..)` 函数作为谓词逻辑传入：

```js
var words = "   Now is the time for all...  ".split( /\s|\b/ );
words;
// ["","Now","is","the","time","for","all","...",""]

words.filter( identity );
// ["Now","is","the","time","for","all","..."]
```

因为 `identity(..)` 所做的知识简单地将传入的值返回，JS 将每个值强转为 `true` 或 `false` ，从而确定在最终的数组中是保留该值还是丢弃掉。

**提示：**再上一个示例中，另一个可以作为谓词的是 JS 内置的 `Boolean(..)` 函数，该函数现实地将值强转为 `true` 或 `false`。

另一个 `identity(..)` 函数的应用场景是作为转换函数的默认参数：

```js
function output(msg,formatFn = identity) {
    msg = formatFn( msg );
    console.log( msg );
}

function upper(txt) {
    return txt.toUpperCase();
}

output( "Hello World", upper );     // HELLO WORLD
output( "Hello World" );            // Hello World
```

You also may see `identity(..)` used as a default transformation function for `map(..)` calls or as the initial value in a `reduce(..)` of a list of functions; both of these utilities will be covered in [Chapter 9](ch9.md).

你可能还会看到其他的使用场景，比如将 `identity(..)` 作为 `map(..)` 中转换函数的默认值，或者作为列表的 `reduce(..)` 函数调用的初始值，这两种会在[第九章](ch9.md)中讲解。

### 一成不变

有些 API 的方法要求传入而一个函数而不是一个值，虽然函数只是简单地返回了这个值。一个例子是 JS 中 Promise 的 `then(..)` 方法：

```js
// 不工作：
p1.then( foo ).then( p2 ).then( bar );

// 工作：
p1.then( foo ).then( function(){ return p2; } ).then( bar );
```

Many claim that ES6 `=>` arrow functions are the best "solution":

许多人认为 ES6 的 `=>` 箭头函数是最好的“解决方案”：

```js
p1.then( foo ).then( () => p2 ).then( bar );
```

但是函数式的工具库中有一个更适合于这种任务：

```js
function constant(v) {
    return function value(){
        return v;
    };
}

// 或者使用 ES6 => 的形式
var constant =
    v =>
        () =>
            v;
```

利用这个函数式工具，我们可以更优雅地解决 `then(..)` 调用的烦恼：

```js
p1.then( foo ).then( constant( p2 ) ).then( bar );
```

**警告：** 尽管 `() => p2` 箭头函数的版本比 `constant(p2)` 更短，我还是鼓励你抑制住使用它的冲动。这个箭头函数返回了一个在该函数函数外部的值，从函数式的角度来看是不太好的。在本书后面的内容中，会介绍这类行为的陷阱（参考[第五章](ch5.md)）。

## 参数适配

有很多种模式和技巧可以用来调整一个函数的签名，从而符合我们对一个函数参数的要求。

回想一下[第二章中 foo 函数的签名](ch2.md/#user-content-funcparamdestr) 它强调了数组解构的参数用法：

```js
function foo( [x,y,...args] = [] ) {
```

如果入参是一个数组，而你又想将数组的内容视为独立的参数的话，那么这种模式是很方便的。`foo(..)` 从技术上的角度是一元函数——执行它时，只有一个参数（数组）会被传入。但是在函数的内部，可以分别处理不同的输入，例如 `x`，`y` 等等。

有时你期望使用函数解构的参数接口，但是可能没有办法做到。例如，假设有以下函数：

```js
function foo(x,y) {
    console.log( x + y );
}

function bar(fn) {
    fn( [ 3, 9 ] );
}

bar( foo );         // 调用失败
```

发现为什么 `bar(foo)` 失败了吗？

数组 `[3,9]` 作为一个单一的值传给 `fn(..)`，但是 `foo(..)` 期望的是 `x` 和 `y` 两个值。如果我们可以将 `foo(..)` 的声明改为 `function foo([x,y]) { ..` 的话，就工作了。或者，我们可以改变 `bar(..)` 的行为，让它以 `fn(...[3,9])` 的形式来调用，那么 `3` 和 `9` 的值就可以分别被传递过去了。

在一些情况下，你可能会有两个函数，而它们的接口是不兼容的，而且你无法改变他们的声明或定义。那么该如何才能够很好地使用它们呢？

我们可以定义一个辅助函数，对该函数进行适配，从而将接收到的单个数组参数，展开，并作为一个个的独立参数传入：

<a name="spreadargs"></a>

```js
function spreadArgs(fn) {
    return function spreadFn(argsArr){
        return fn( ...argsArr );
    };
}

// 或者使用 ES6 的 => 箭头函数形式
var spreadArgs =
    fn =>
        argsArr =>
            fn( ...argsArr );
```

**注意：** 我将这个辅助函数命名为 `spreadArgs(..)`，但是在一些像 Ramda 之类的库中，通常将其称之为 `apply(..)`。

现在我们可以使用 `spreadArgs(..)` 来适配 `foo(..)`，从而可以将其作为 `bar(..)` 的正确输入：

```js
bar( spreadArgs( foo ) );           // 12
```

虽然还不清楚为什么会出现需要参数适配的情况，但是你会经常遇到它们。本质上来说，`spreadArgs(..)` 允许我们定义一个能够通过数组 `return` 多个值的函数，而这些值分别作为另一个函数的独立参数使用。

既然聊到了 `spreadArgs(..)` 工具，让我们再定义一个相反功用的工具：

```js
function gatherArgs(fn) {
    return function gatheredFn(...argsArr){
        return fn( argsArr );
    };
}

// 或者使用 ES6 => 的形式
var gatherArgs =
    fn =>
        (...argsArr) =>
            fn( argsArr );
```

**注意：** 在 Ramda 中，这个工具函数被称为 `unapply(..)`，因为它是 `apply(..)` 相对的函数。我个人认为“spread”和“gather” 这两个术语更具表意性。

有时一个函数接收一个解构数组作为参数，而我们期望将其适配成一个分别处理各个参数的函数，这时我们可以使用这个工具，将各个独立的参数收集到一个数组中。我们会在[第九章中更详细地介绍 `reduce(..)`](ch9.md/#reduce)；简而言之，它将反复调用 reducer 函数，并传入两个独立的参数，我们可以将这些参数 *gather* 起来：
```js
function combineFirstTwo([ v1, v2 ]) {
    return v1 + v2;
}

[1,2,3,4,5].reduce( gatherArgs( combineFirstTwo ) );
// 15
```

## Some Now, Some Later

如果一个函数接收多个参数，你可能期望预先指定其中的某一些，其余的参数以后再决定。

考虑如下函数：

```js
function ajax(url,data,callback) {
    // ..
}
```

假设你想要预设几个 API 调用，这些调用中的 URL 是预先已知的，而 data 和处理响应的 callback 需要后面才能够定下来。

当然你可以推迟对 `ajax(..)` 的调用，直到所有参数都已确定，对于 URL，可以引用一些全局常量。而另一种方式是创建一个持有预设的 `url` 引用的的函数。

我们要做的是创建一个新函数，它在底层仍然会调用 `ajax(..)`，并手动将第一个参数设置为需要的 API URL，同时等待着接收另外的两个参数：

```js
function getPerson(data,cb) {
    ajax( "http://some.api/person", data, cb );
}

function getOrder(data,cb) {
    ajax( "http://some.api/order", data, cb );
}
```

当然可以像上面这样手动指定这些包装函数，但是这样的代码会很麻烦，尤其是当预设的参数结构可能不同的时候，例如：

```js
function getCurrentUser(cb) {
    getPerson( { user: CURRENT_USER_ID }, cb );
}
```

函数式程序员的常用做法是在重复代码中寻找通用模式，并尝试将这些操作转换为通用的工具。事实上，我相信对于很多读者来说，这已经是一种代码本能，不是函数式程序员所专有的。但对于函数式编程来说，这种做法毫无疑问非常重要。

为了构思这样一个用于参数预设的函数，除了观察上面手动实现的代码之外，让我们从概念出发，审视发生了什么。

问题的一种解释是：`getOrder(data,cb)` 是函数 `ajax(url,data,cb)` 的*部分应用（partial application）*。在函数被调用时，实参被*应用*到形参上，这个术语也是源自于此。正如你所看到的，我们仅应用了前面的某些参数——具体来说就是 `url` 参数——而其余部分在将来再被应用。

这种模式的更正式定义是：部分应用严格地说是函数参数个数（artity）的规约；记住，个数指的是所期望的形参的数量。对于 `getOrder(..)` 函数，我们将原始的 `ajax(..)` 函数的参数个数从 3 减少到了 2。

让我们定义一个 `partial(..)` 工具函数：

```js
function partial(fn,...presetArgs) {
    return function partiallyApplied(...laterArgs){
        return fn( ...presetArgs, ...laterArgs );
    };
}

// 或者使用 ES6 => 箭头函数形式
var partial =
    (fn,...presetArgs) =>
        (...laterArgs) =>
            fn( ...presetArgs, ...laterArgs );
```

**提示：** 不要仅停留在表面。稍作驻足，看看这个函数究竟在做什么。确保能够真正*理解它*。

`partial(..)` 函数接收一个我们将要进行部分应用的 `fn` 函数。然后，任何所传入的后续参数都将被收集到 `presetArgs` 数组中，保存并以备后用。

一个新的内部函数（为了清晰起见，起名为 `partiallyApplied(..)`）被创建并被 `return`；内部函数自己的参数将被收集到名为 `laterArgs` 的数组中。

注意到内部函数对 `fn` 和 `presetArgs` 的引用了吗？这是怎么工作的？在 `partial(..)` 调用完成之后，内部的函数为何能够继续访问 `fn` 和 `presetArgs`？如果你的回答是**闭包**，完全正确！内部函数 `partiallyApplied(..)` 同时捕获（close over） `fn` 和 `presetArgs` 两个变量，因此无论函数在喊出运行，都可以继续保持对它们的引用。这就是为什么理解闭包至关重要的原因。

随后，当在程序的其他地方执行 `partiallyApplied(..)` 函数的时候，它使用闭包持有的 `fn` 来执行原来的函数，首先提供捕获的 `presetArgs` 作为一部分的运行参数，继而是 `laterArgs` 参数。

如果上面的任何一点让你有所疑惑，请停下脚步再读一次。相信我，随着内容的不断深入，你会庆幸自己这么做了。

现在让我们使用 `partial(..)` 工具来实现之前部分应用的函数：

```js
var getPerson = partial( ajax, "http://some.api/person" );

var getOrder = partial( ajax, "http://some.api/order" );
```

停下来想一想 `getPerson(..)` 的内部实现。它看起来像是这样：

```js
var getPerson = function partiallyApplied(...laterArgs) {
    return ajax( "http://some.api/person", ...laterArgs );
};
```

`getOrder(..)` 也是如此。`getCurrentUser(..)` 函数又会如何呢？

```js
// 版本 1
var getCurrentUser = partial(
    ajax,
    "http://some.api/person",
    { user: CURRENT_USER_ID }
);

// 版本 2
var getCurrentUser = partial( getPerson, { user: CURRENT_USER_ID } );
```

我们可以在定义 `getCurrentUser(..)` 函数的时候，直接同时指定 `url` 和 `data`（版本 1），或者将通过 `getPerson(..)` 的部分应用来定义 `getCurrentUser(..)`，仅指定一个额外的参数 `data` 参数（版本 2）。

因为重用了已经定义好的函数，版本 2 的表达会更加清晰简洁。也是这个原因，我认为它更符合函数式编程的精神。

为了确保我们了解这两个版本在幕后是如何工作的，它们看上去分别像是：

```js
// 版本 1
var getCurrentUser = function partiallyApplied(...laterArgs) {
    return ajax(
        "http://some.api/person",
        { user: CURRENT_USER_ID },
        ...laterArgs
    );
};

// 版本 2
var getCurrentUser = function outerPartiallyApplied(...outerLaterArgs){
    var getPerson = function innerPartiallyApplied(...innerLaterArgs){
        return ajax( "http://some.api/person", ...innerLaterArgs );
    };

    return getPerson( { user: CURRENT_USER_ID }, ...outerLaterArgs );
}
```

同样，稍作停留重读一下这些代码片段，以确保了解它们的工作原理。

**注意：**版本 2 中有含有一个额外的包装函数。看起来可能有些奇怪和多此一举，但是这种操作在函数式编程中很常见，也是需要适应的事情之一。在本书内容的推进，我们将会经常使用包装函数。记住，这是*函数*式编程！

让我们看一下部分应用的另一个例子。考虑一个 `add(..)` 函数，它接收两个参数并将它们相加：

```js
function add(x,y) {
    return x + y;
}
```

Now imagine we'd like take a list of numbers and add a certain number to each of them. We'll use the `map(..)` utility (see [Chapter 9, "Map"](ch9.md/#map)) built into JS arrays:
现在想想一下，我们想要将一个数字列表中的每一个数字加上一个特定的数。我们将使用 JS 数组内置的 `map(..)` 函数 （参见[第九章 “Map”](ch9.md/#map)）：

```js
[1,2,3,4,5].map( function adder(val){
    return add( 3, val );
} );
// [4,5,6,7,8]
```

我们之所以不能将 `add(..)` 直接传给 `map(..)` 是因为 `add(..)` 的函数签名和 `map(..)` 所期望的不符。这就是部分应用可以提供帮助的地方：我们可以将 `add(..)` 的签名修改为期望的样子：

```js
[1,2,3,4,5].map( partial( add, 3 ) );
// [4,5,6,7,8]
```

`partial(add,3)` 调用产生一个新的一元函数，该函数只接收一个参数。

`map(..)` 函数将遍历数组（`[1,2,3,4,5]`）并重复调用该一元函数，每次将其中的一个值应用到该函数上。所以调用实际上为 `add(3,1)`，`add(3,2)`，`add(3,3)`，`add(3,4)` 和 `add(3,5)`。调用的结果是数组 `[4,5,6,7,8]`。

### `bind(..)`

JavaScript 的函数都拥有一个内置的 `bind(..)` 工具。它有两个功能：预设 `this` 的值和部分应用一些参数。

我认为将这两种功能合并在一个函数中非常令人误解。有时你会想要硬绑定 `this` 上下文的值，但不用部分应用参数。有时又只想部分应用某些参数，但是不关心 `this` 的绑定。我从未同时使用这两种功能。

后一种情况（部分应用参数，但是不设置 `this`）很尴尬，因为你必须为 `this` （第一个参数）传递一个可忽略的占位符，通常为 `null`。

考虑如下代码：

```js
var getPerson = ajax.bind( null, "http://some.api/person" );
```

这里的 `null` 让我烦恼不已。尽管这里的 *this* 有点恼火，但是 JS 有个内置的部分应用工具，这一点缺失还是非常方便的。然而，大多是函数式程序员更倾向于使用他们选择的函数式库中专用的 `partial(..)` 函数。

### 反转参数

Recall that the signature for our Ajax function is: `ajax( url, data, cb )`. What if we wanted to partially apply the `cb` but wait to specify `data` and `url` later? We could create a utility that wraps a function to reverse its argument order:
回想一下我们 Ajax 函数的签名：`ajax( url, data, cb )`。如果我们想将 `cb` 参数部分应用，而稍后再指定 `data` 和 `url` 呢？我们可以创建一个工具，它包装一个函数，并将参数的顺序进行反转；

```js
function reverseArgs(fn) {
    return function argsReversed(...args){
        return fn( ...args.reverse() );
    };
}

// 或者使用 ES6 => 箭头函数形式
var reverseArgs =
    fn =>
        (...args) =>
            fn( ...args.reverse() );
```

现在我们可以颠倒 `ajax(..)` 函数的参数顺序，这样我们就可以从右侧而不是左侧进行参数的部分应用。进行部分应用后，为了恢复期望的参数顺序，我们需要再次进行参数反转：

```js
var cache = {};

var cacheResult = reverseArgs(
    partial( reverseArgs( ajax ), function onResult(obj){
        cache[obj.id] = obj;
    } )
);

// 随后：
cacheResult( "http://some.api/person", { user: CURRENT_USER_ID } );
```

Instead of manually using `reverseArgs(..)` (twice!) for this purpose, we can define a `partialRight(..)` which partially applies the rightmost arguments. Under the covers, it can use the same double-reverse trick:

<a name="partialright"></a>

```js
function partialRight(fn,...presetArgs) {
    return reverseArgs(
        partial( reverseArgs( fn ), ...presetArgs.reverse() )
    );
}

var cacheResult = partialRight( ajax, function onResult(obj){
    cache[obj.id] = obj;
});

// later:
cacheResult( "http://some.api/person", { user: CURRENT_USER_ID } );
```

Another more straightforward (and certainly more performant) implementation of `partialRight(..)` that doesn't use the double-reverse trick:

```js
function partialRight(fn,...presetArgs) {
    return function partiallyApplied(...laterArgs){
        return fn( ...laterArgs, ...presetArgs );
    };
}

// or the ES6 => arrow form
var partialRight =
    (fn,...presetArgs) =>
        (...laterArgs) =>
            fn( ...laterArgs, ...presetArgs );
```

None of these implementations of `partialRight(..)` guarantee that a specific parameter will receive a specific partially applied value; it only ensures that the partially applied value(s) appear as the rightmost (aka, last) argument(s) passed to the original function.

For example:

```js
function foo(x,y,z,...rest) {
    console.log( x, y, z, rest );
}

var f = partialRight( foo, "z:last" );

f( 1, 2 );          // 1 2 "z:last" []

f( 1 );             // 1 "z:last" undefined []

f( 1, 2, 3 );       // 1 2 3 ["z:last"]

f( 1, 2, 3, 4 );    // 1 2 3 [4,"z:last"]
```

The value `"z:last"` is only applied to the `z` parameter in the case where `f(..)` is called with exactly two arguments (matching `x` and `y` parameters). In all other cases, the `"z:last"` will just be the rightmost argument, however many arguments precede it.

## One at a Time

Let's examine a technique similar to partial application, where a function that expects multiple arguments is broken down into successive chained functions that each take a single argument (arity: 1) and return another function to accept the next argument.

This technique is called currying.

To first illustrate, let's imagine we had a curried version of `ajax(..)` already created. This is how we'd use it:

```js
curriedAjax( "http://some.api/person" )
    ( { user: CURRENT_USER_ID } )
        ( function foundUser(user){ /* .. */ } );
```

The three sets of `(..)`s denote three chained function calls. But perhaps splitting out each of the three calls helps see what's going on better:

```js
var personFetcher = curriedAjax( "http://some.api/person" );

var getCurrentUser = personFetcher( { user: CURRENT_USER_ID } );

getCurrentUser( function foundUser(user){ /* .. */ } );
```

Instead of taking all the arguments at once (like `ajax(..)`), or some of the arguments up front and the rest later (via `partial(..)`), this `curriedAjax(..)` function receives one argument at a time, each in a separate function call.

Currying is similar to partial application in that each successive curried call partially applies another argument to the original function, until all arguments have been passed.

The main difference is that `curriedAjax(..)` will return a function (we call it `personFetcher(..)`) that expects **only the next argument** `data`, not one that (like the earlier `getPerson(..)`) can receive all the rest of the arguments.

If an original function expected five arguments, the curried form of that function would take just the first argument, and return a function to accept the second. That one would take just the second argument, and return a function to accept the third. And so on.

So currying unwinds a single higher-arity function into a series of chained unary functions.

How might we define a utility to do this currying? Consider:

<a name="curry"></a>

```js
function curry(fn,arity = fn.length) {
    return (function nextCurried(prevArgs){
        return function curried(nextArg){
            var args = [ ...prevArgs, nextArg ];

            if (args.length >= arity) {
                return fn( ...args );
            }
            else {
                return nextCurried( args );
            }
        };
    })( [] );
}

// or the ES6 => arrow form
var curry =
    (fn,arity = fn.length,nextCurried) =>
        (nextCurried = prevArgs =>
            nextArg => {
                var args = [ ...prevArgs, nextArg ];

                if (args.length >= arity) {
                    return fn( ...args );
                }
                else {
                    return nextCurried( args );
                }
            }
        )( [] );
```

The approach here is to start a collection of arguments in `prevArgs` as an empty `[]` array, and add each received `nextArg` to that, calling the concatenation `args`. While `args.length` is less than `arity` (the number of declared/expected parameters of the original `fn(..)` function), make and return another `curried(..)` function to collect the next `nextArg` argument, passing the running `args` collection along as its `prevArgs`. Once we have enough `args`, execute the original `fn(..)` function with them.

By default, this implementation relies on being able to inspect the `length` property of the to-be-curried function to know how many iterations of currying we'll need before we've collected all its expected arguments.

**Note:** If you use this implementation of `curry(..)` with a function that doesn't have an accurate `length` property, you'll need to pass the `arity` (the second parameter of `curry(..)`) to ensure `curry(..)` works correctly. `length` will be inaccurate if the function's parameter signature includes default parameter values, parameter destructuring, or is variadic with `...args` (see [Chapter 2](ch2.md)).

Here's how we would use `curry(..)` for our earlier `ajax(..)` example:

```js
var curriedAjax = curry( ajax );

var personFetcher = curriedAjax( "http://some.api/person" );

var getCurrentUser = personFetcher( { user: CURRENT_USER_ID } );

getCurrentUser( function foundUser(user){ /* .. */ } );
```

Each call partially applies one more argument to the original `ajax(..)` call, until all three have been provided and `ajax(..)` is actually invoked.

Remember our example from the discussion of partial application about adding `3` to each value in a list of numbers? As currying is similar to partial application, we could do that task with currying in almost the same way:

```js
[1,2,3,4,5].map( curry( add )( 3 ) );
// [4,5,6,7,8]
```

The difference between the two? `partial(add,3)` vs `curry(add)(3)`.

Why might you choose `curry(..)` over `partial(..)`? It might be helpful in the case where you know ahead of time that `add(..)` is the function to be adapted, but the value `3` isn't known yet:

```js
var adder = curry( add );

// later
[1,2,3,4,5].map( adder( 3 ) );
// [4,5,6,7,8]
```

Let's look at another numbers example, this time adding a list of them together:

```js
function sum(...nums) {
    var total = 0;
    for (let num of nums) {
        total += num;
    }
    return total;
}

sum( 1, 2, 3, 4, 5 );                       // 15

// now with currying:
// (5 to indicate how many we should wait for)
var curriedSum = curry( sum, 5 );

curriedSum( 1 )( 2 )( 3 )( 4 )( 5 );        // 15
```

The advantage of currying here is that each call to pass in an argument produces another function that's more specialized, and we can capture and use *that* new function later in the program. Partial application specifies all the partially applied arguments up front, producing a function that's waiting for all the rest of the arguments **on the next call**.

If you wanted to use partial application to specify one parameter (or several!) at a time, you'd have to keep calling `partial(..)` again on each successive partially applied function. By contrast, curried functions do this automatically, making working with individual arguments one-at-a-time more ergonomic.

Both currying and partial application use closure to remember the arguments over time until all have been received, and then the original function can be invoked.

### Visualizing Curried Functions

Let's examine more closely the `curriedSum(..)` from the previous section. Recall its usage: `curriedSum(1)(2)(3)(4)(5)`; five subsequent (chained) function calls.

What if we manually defined a `curriedSum(..)` instead of using `curry(..)`? How would that look?

```js
function curriedSum(v1) {
    return function(v2){
        return function(v3){
            return function(v4){
                return function(v5){
                    return sum( v1, v2, v3, v4, v5 );
                };
            };
        };
    };
}
```

Definitely uglier, no question. But this is an important way to visualize what's going on with a curried function. Each nested function call is returning another function that's going to accept the next argument, and that continues until we've specified all the expected arguments.

When trying to decipher curried functions, I've found it helps me tremendously if I can unwrap them mentally as a series of nested functions.

In fact, to reinforce that point, let's consider the same code but written with ES6 arrow functions:

```js
curriedSum =
    v1 =>
        v2 =>
            v3 =>
                v4 =>
                    v5 =>
                        sum( v1, v2, v3, v4, v5 );
```

And now, all on one line:

```js
curriedSum = v1 => v2 => v3 => v4 => v5 => sum( v1, v2, v3, v4, v5 );
```

Depending on your perspective, that form of visualizing the curried function may be more or less helpful to you. For me, it's a fair bit more obscured.

But the reason I show it that way is that it happens to look almost identical to the mathematical notation (and Haskell syntax) for a curried function! That's one reason why those who like mathematical notation (and/or Haskell) like the ES6 arrow function form.

### Why Currying and Partial Application?

With either style -- currying (such as `sum(1)(2)(3)`) or partial application (such as `partial(sum,1,2)(3)`) -- the call-site unquestionably looks stranger than a more common one like `sum(1,2,3)`. So **why would we ever go this direction** when adopting FP? There are multiple layers to answering that question.

The first and most obvious reason is that both currying and partial application allow you to separate in time/space (throughout your codebase) when and where separate arguments are specified, whereas traditional function calls require all the arguments to be present at the same time. If you have a place in your code where you'll know some of the arguments and another place where the other arguments are determined, currying or partial application are very useful.

Another layer to this answer, specifically for currying, is that composition of functions is much easier when there's only one argument. So a function that ultimately needs three arguments, if curried, becomes a function that needs just one, three times over. That kind of unary function will be a lot easier to work with when we start composing them. We'll tackle this topic later in [Chapter 4](ch4.md).

But the most important layer is specialization of generalized functions, and how such abstraction improves readability of code.

Consider our running `ajax(..)` example:

```js
ajax(
    "http://some.api/person",
    { user: CURRENT_USER_ID },
    function foundUser(user){ /* .. */ }
);
```

The call-site includes all the information necessary to pass to the most generalized version of the utility (`ajax(..)`). The potential readability downside is that it may be the case that the URL and the data are not relevant information at this point in the program, but yet that information is cluttering up the call-site nonetheless.

Now consider:

```js
var getCurrentUser = partial(
    ajax,
    "http://some.api/person",
    { user: CURRENT_USER_ID }
);

// later

getCurrentUser( function foundUser(user){ /* .. */ } );
```

In this version, we define a `getCurrentUser(..)` function ahead of time that already has known information like URL and data preset. The call-site for `getCurrentUser(..)` then isn't cluttered by information that **at that point of the code** isn't relevant.

Moreover, the semantic name for the function `getCurrentUser(..)` more accurately depicts what is happening than just `ajax(..)` with a URL and data would.

That's what abstraction is all about: separating two sets of details -- in this case, the *how* of getting a current user and the *what* we do with that user -- and inserting a semantic boundary between them, which eases the reasoning of each part independently.

Whether you use currying or partial application, creating specialized functions from generalized ones is a powerful technique for semantic abstraction and improved readability.

### Currying More Than One Argument?

The definition and implementation I've given of currying thus far is, I believe, as true to the spirit as we can likely get in JavaScript.

Specifically, if we look briefly at how currying works in Haskell, we can observe that multiple arguments always go in to a function one at a time, one per curried call -- other than tuples (analogous to arrays for our purposes) that transport multiple values in a single argument.

For example, in Haskell:

```haskell
foo 1 2 3
```

This calls the `foo` function, and has the result of passing in three values `1`, `2`, and `3`. But functions are automatically curried in Haskell, which means each value goes in as a separate curried-call. The JS equivalent of that would look like `foo(1)(2)(3)`, which is the same style as the `curry(..)` I presented earlier.

**Note:** In Haskell, `foo (1,2,3)` is not passing in those three values at once as three separate arguments, but a tuple (kinda like a JS array) as a single argument. To work, `foo` would need to be altered to handle a tuple in that argument position. As far as I can tell, there's no way in Haskell to pass all three arguments separately with just one function call; each argument gets its own curried-call. Of course, the presence of multiple calls is transparent to the Haskell developer, but it's a lot more syntactically obvious to the JS developer.

For these reasons, I think the `curry(..)` that I demonstrated earlier is a faithful adaptation, or what I might call "strict currying". However, it's important to note that there's a looser definition used in most popular JavaScript FP libraries.

Specifically, JS currying utilities typically allow you to specify multiple arguments for each curried-call. Revisiting our `sum(..)` example from before, this would look like:

```js
var curriedSum = looseCurry( sum, 5 );

curriedSum( 1 )( 2, 3 )( 4, 5 );            // 15
```

We see a slight syntax savings of fewer `( )`, and an implied performance benefit of now having three function calls instead of five. But other than that, using `looseCurry(..)` is identical in end result to the narrower `curry(..)` definition from earlier. I would guess the convenience/performance factor is probably why frameworks allow multiple arguments. This seems mostly like a matter of taste.

We can adapt our previous currying implementation to this common looser definition:

<a name="loosecurry"></a>

```js
function looseCurry(fn,arity = fn.length) {
    return (function nextCurried(prevArgs){
        return function curried(...nextArgs){
            var args = [ ...prevArgs, ...nextArgs ];

            if (args.length >= arity) {
                return fn( ...args );
            }
            else {
                return nextCurried( args );
            }
        };
    })( [] );
}
```

Now each curried-call accepts one or more arguments (as `nextArgs`). We'll leave it as an exercise for the interested reader to define the ES6 `=>` version of `looseCurry(..)` similar to how we did it for `curry(..)` earlier.

### No Curry for Me, Please

It may also be the case that you have a curried function that you'd like to essentially un-curry -- basically, to turn a function like `f(1)(2)(3)` back into a function like `g(1,2,3)`.

The standard utility for this is (un)shockingly typically called `uncurry(..)`. Here's a simple naive implementation:

```js
function uncurry(fn) {
    return function uncurried(...args){
        var ret = fn;

        for (let arg of args) {
            ret = ret( arg );
        }

        return ret;
    };
}

// or the ES6 => arrow form
var uncurry =
    fn =>
        (...args) => {
            var ret = fn;

            for (let arg of args) {
                ret = ret( arg );
            }

            return ret;
        };
```

**Warning:** Don't just assume that `uncurry(curry(f))` has the same behavior as `f`. In some libraries the uncurrying would result in a function like the original, but not all of them; certainly our example here does not. The uncurried function acts (mostly) the same as the original function if you pass as many arguments to it as the original function expected. However, if you pass fewer arguments, you still get back a partially curried function waiting for more arguments; this quirk is illustrated in the following snippet:

```js
function sum(...nums) {
    var sum = 0;
    for (let num of nums) {
        sum += num;
    }
    return sum;
}

var curriedSum = curry( sum, 5 );
var uncurriedSum = uncurry( curriedSum );

curriedSum( 1 )( 2 )( 3 )( 4 )( 5 );        // 15

uncurriedSum( 1, 2, 3, 4, 5 );              // 15
uncurriedSum( 1, 2, 3 )( 4 )( 5 );          // 15
```

Probably the more common case of using `uncurry(..)` is not with a manually curried function as just shown, but with a function that comes out curried as a result of some other set of operations. We'll illustrate that scenario later in this chapter in the ["No Points" discussion](#no-points).

## Order Matters

In Chapter 2, we explored the [named arguments pattern](ch2.md/#named-arguments). One primary advantage of named arguments is not needing to juggle argument ordering, thereby improving readability.

We've looked at the advantages of using currying/partial application to provide individual arguments to a function separately. But the downside is that these techniques are traditionally based on positional arguments; argument ordering is thus an inevitable headache.

Utilities like `reverseArgs(..)` (and others) are necessary to juggle arguments to get them into the right order. Sometimes we get lucky and define a function with parameters in the order that we later want to curry them, but other times that order is incompatible and we have to jump through hoops to reorder.

The frustration is not merely that we need to use some utility to juggle the properties, but the fact that the usage of the utility clutters up our code a bit with extra noise. These kinds of things are like little paper cuts; one here or there isn't a showstopper, but the pain can certainly add up.

Can we improve currying/partial application to free it from these ordering concerns? Let's apply the tricks from named arguments style and invent some helper utilities for this adaptation:

```js
function partialProps(fn,presetArgsObj) {
    return function partiallyApplied(laterArgsObj){
        return fn( Object.assign( {}, presetArgsObj, laterArgsObj ) );
    };
}

function curryProps(fn,arity = 1) {
    return (function nextCurried(prevArgsObj){
        return function curried(nextArgObj = {}){
            var [key] = Object.keys( nextArgObj );
            var allArgsObj = Object.assign(
                {}, prevArgsObj, { [key]: nextArgObj[key] }
            );

            if (Object.keys( allArgsObj ).length >= arity) {
                return fn( allArgsObj );
            }
            else {
                return nextCurried( allArgsObj );
            }
        };
    })( {} );
}
```

**Tip:** We don't even need a `partialPropsRight(..)` because we don't need to care about what order properties are being mapped; the name mappings make that ordering concern moot!

Here's how to use those helpers:

```js
function foo({ x, y, z } = {}) {
    console.log( `x:${x} y:${y} z:${z}` );
}

var f1 = curryProps( foo, 3 );
var f2 = partialProps( foo, { y: 2 } );

f1( {y: 2} )( {x: 1} )( {z: 3} );
// x:1 y:2 z:3

f2( { z: 3, x: 1 } );
// x:1 y:2 z:3
```

Even with currying or partial application, order doesn't matter anymore! We can now specify which arguments we want in whatever sequence makes sense. No more `reverseArgs(..)` or other nuisances. Cool!

**Tip:** If this style of function arguments seems useful or interesting to you, check out coverage of my [FPO library in Appendix C](apC.md/#bonus-fpo).

### Spreading Properties

Unfortunately, we can only take advantage of currying with named arguments if we have control over the signature of `foo(..)` and define it to destructure its first parameter. What if we wanted to use this technique with a function that had its parameters individually listed (no parameter destructuring!), and we couldn't change that function signature? For example:

```js
function bar(x,y,z) {
    console.log( `x:${x} y:${y} z:${z}` );
}
```

Just like the `spreadArgs(..)` utility earlier, we can define a `spreadArgProps(..)` helper that takes the `key: value` pairs out of an object argument and "spreads" the values out as individual arguments.

There are some quirks to be aware of, though. With `spreadArgs(..)`, we were dealing with arrays, where ordering is well defined and obvious. However, with objects, property order is less clear and not necessarily reliable. Depending on how an object is created and properties set, we cannot be absolutely certain what enumeration order properties would come out.

Such a utility needs a way to let you define what order the function in question expects its arguments (e.g., property enumeration order). We can pass an array like `["x","y","z"]` to tell the utility to pull the properties off the object argument in exactly that order.

That's decent, but it's also unfortunate that it then *obligates* us to add that property-name array even for the simplest of functions. Is there any kind of trick we could use to detect what order the parameters are listed for a function, in at least the common simple cases? Fortunately, yes!

JavaScript functions have a `.toString()` method that gives a string representation of the function's code, including the function declaration signature. Dusting off our regular expression parsing skills, we can parse the string representation of the function, and pull out the individually named parameters. The code looks a bit gnarly, but it's good enough to get the job done:

```js
function spreadArgProps(
    fn,
    propOrder =
        fn.toString()
        .replace( /^(?:(?:function.*\(([^]*?)\))|(?:([^\(\)]+?)
            \s*=>)|(?:\(([^]*?)\)\s*=>))[^]+$/, "$1$2$3" )
        .split( /\s*,\s*/ )
        .map( v => v.replace( /[=\s].*$/, "" ) )
) {
    return function spreadFn(argsObj){
        return fn( ...propOrder.map( k => argsObj[k] ) );
    };
}
```

**Note:** This utility's parameter parsing logic is far from bullet-proof; we're using regular expressions to parse code, which is already a faulty premise! But our only goal here is to handle the common cases, which this does reasonably well. We only need a sensible default detection of parameter order for functions with simple parameters (including those with default parameter values). We don't, for example, need to be able to parse out a complex destructured parameter, because we wouldn't likely be using this utility with such a function, anyway. So, this logic gets the job done 80% of the time; it lets us override the `propOrder` array for any other more complex function signature that wouldn't otherwise be correctly parsed. That's the kind of pragmatic balance this book seeks to find wherever possible.

Let's illustrate using our `spreadArgProps(..)` utility:

```js
function bar(x,y,z) {
    console.log( `x:${x} y:${y} z:${z}` );
}

var f3 = curryProps( spreadArgProps( bar ), 3 );
var f4 = partialProps( spreadArgProps( bar ), { y: 2 } );

f3( {y: 2} )( {x: 1} )( {z: 3} );
// x:1 y:2 z:3

f4( { z: 3, x: 1 } );
// x:1 y:2 z:3
```

While order is no longer a concern, usage of functions defined in this style requires you to know what each argument's exact name is. You can't just remember, "oh, the function goes in as the first argument" anymore. Instead, you have to remember, "the function parameter is called 'fn'." Conventions can create consistency of naming that lessens this burden, but it's still something to be aware of.

Weigh these trade-offs carefully.

## No Points

A popular style of coding in the FP world aims to reduce some of the visual clutter by removing unnecessary parameter-argument mapping. This style is formally called tacit programming, or more commonly: point-free style. The term "point" here is referring to a function's parameter input.

**Warning:** Stop for a moment. Let's make sure we're careful not to take this discussion as an unbounded suggestion that you go overboard trying to be point-free in your FP code at all costs. This should be a technique for improving readability, when used in moderation. But as with most things in software development, you can definitely abuse it. If your code gets harder to understand because of the hoops you have to jump through to be point-free, stop. You won't win a blue ribbon just because you found some clever but esoteric way to remove another "point" from your code.

Let's start with a simple example:

```js
function double(x) {
    return x * 2;
}

[1,2,3,4,5].map( function mapper(v){
    return double( v );
} );
// [2,4,6,8,10]
```

Can you see that `mapper(..)` and `double(..)` have the same (or compatible, anyway) signatures? The parameter ("point") `v` can directly map to the corresponding argument in the `double(..)` call. As such, the `mapper(..)` function wrapper is unnecessary. Let's simplify with point-free style:

```js
function double(x) {
    return x * 2;
}

[1,2,3,4,5].map( double );
// [2,4,6,8,10]
```

Let's revisit an example from earlier:

```js
["1","2","3"].map( function mapper(v){
    return parseInt( v );
} );
// [1,2,3]
```

In this example, `mapper(..)` is actually serving an important purpose, which is to discard the `index` argument that `map(..)` would pass in, because `parseInt(..)` would incorrectly interpret that value as a `radix` for the parsing.

If you recall from the beginning of this chapter, this was an example where `unary(..)` helps us out:

```js
["1","2","3"].map( unary( parseInt ) );
// [1,2,3]
```

Point-free!

The key thing to look for is if you have a function with parameter(s) that is/are directly passed to an inner function call. In both of the preceding examples, `mapper(..)` had the `v` parameter that was passed along to another function call. We were able to replace that layer of abstraction with a point-free expression using `unary(..)`.

**Warning:** You might have been tempted, as I was, to try `map(partialRight(parseInt,10))` to right-partially apply the `10` value as the `radix`. However, as we saw earlier, `partialRight(..)` only guarantees that `10` will be the last argument passed in, not that it will be specifically the second argument. Since `map(..)` itself passes three arguments (`value`, `index`, `arr`) to its mapping function, the `10` value would just be the fourth argument to `parseInt(..)`; it only pays attention to the first two.

<a name="shortlongenough"></a>

Here's another example:

```js
// convenience to avoid any potential binding issue
// with trying to use `console.log` as a function
function output(txt) {
    console.log( txt );
}

function printIf( predicate, msg ) {
    if (predicate( msg )) {
        output( msg );
    }
}

function isShortEnough(str) {
    return str.length <= 5;
}

var msg1 = "Hello";
var msg2 = msg1 + " World";

printIf( isShortEnough, msg1 );         // Hello
printIf( isShortEnough, msg2 );
```

Now let's say you want to print a message only if it's long enough; in other words, if it's `!isShortEnough(..)`. Your first thought is probably this:

```js
function isLongEnough(str) {
    return !isShortEnough( str );
}

printIf( isLongEnough, msg1 );
printIf( isLongEnough, msg2 );          // Hello World
```

Easy enough... but "points" now! See how `str` is passed through? Without re-implementing the `str.length` check, can we refactor this code to point-free style?

Let's define a `not(..)` negation helper (often referred to as `complement(..)` in FP libraries):

```js
function not(predicate) {
    return function negated(...args){
        return !predicate( ...args );
    };
}

// or the ES6 => arrow form
var not =
    predicate =>
        (...args) =>
            !predicate( ...args );
```

Next, let's use `not(..)` to alternatively define `isLongEnough(..)` without "points":

```js
var isLongEnough = not( isShortEnough );

printIf( isLongEnough, msg2 );          // Hello World
```

That's pretty good, isn't it? But we *could* keep going. `printIf(..)` could be refactored to be point-free itself.

We can express the `if` conditional part with a `when(..)` utility:

```js
function when(predicate,fn) {
    return function conditional(...args){
        if (predicate( ...args )) {
            return fn( ...args );
        }
    };
}

// or the ES6 => form
var when =
    (predicate,fn) =>
        (...args) =>
            predicate( ...args ) ? fn( ...args ) : undefined;
```

Let's mix `when(..)` with a few other helper utilities we've seen earlier in this chapter, to make the point-free `printIf(..)`:

```js
var printIf = uncurry( partialRight( when, output ) );
```

Here's how we did it: we right-partially-applied the `output` method as the second (`fn`) argument for `when(..)`, which leaves us with a function still expecting the first argument (`predicate`). *That* function when called produces another function expecting the message string; it would look like this: `fn(predicate)(str)`.

A chain of multiple (two) function calls like that looks an awful lot like a curried function, so we `uncurry(..)` this result to produce a single function that expects the two `str` and `predicate` arguments together, which matches the original `printIf(predicate,str)` signature.

Here's the whole example put back together (assuming various utilities we've already detailed in this chapter are present):

<a name="finalshortlong"></a>

```js
function output(msg) {
    console.log( msg );
}

function isShortEnough(str) {
    return str.length <= 5;
}

var isLongEnough = not( isShortEnough );

var printIf = uncurry( partialRight( when, output ) );

var msg1 = "Hello";
var msg2 = msg1 + " World";

printIf( isShortEnough, msg1 );         // Hello
printIf( isShortEnough, msg2 );

printIf( isLongEnough, msg1 );
printIf( isLongEnough, msg2 );          // Hello World
```

Hopefully the FP practice of point-free style coding is starting to make a little more sense. It'll still take a lot of practice to train yourself to think this way naturally. **And you'll still have to make judgement calls** as to whether point-free coding is worth it, as well as what extent will benefit your code's readability.

What do you think? Points or no points for you?

**Note:** Want more practice with point-free style coding? We'll revisit this technique in [Chapter 4, "Revisiting Points"](ch4.md/#revisiting-points), based on newfound knowledge of function composition.

## Summary

Partial application is a technique for reducing the arity (that is, the expected number of arguments to a function) by creating a new function where some of the arguments are preset.

Currying is a special form of partial application where the arity is reduced to 1, with a chain of successive chained function calls, each which takes one argument. Once all arguments have been specified by these function calls, the original function is executed with all the collected arguments. You can also undo a currying.

Other important utilities like `unary(..)`, `identity(..)`, and `constant(..)` are part of the base toolbox for FP.

Point-free is a style of writing code that eliminates unnecessary verbosity of mapping parameters ("points") to arguments, with the goal of making code easier to read/understand.

All of these techniques twist functions around so they can work together more naturally. With your functions shaped compatibly now, the next chapter will teach you how to combine them to model the flows of data through your program.
