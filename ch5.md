# 第 5 章：代码组合（compose）

## 函数饲养

这就是 `组合`（compose，以下将称之为组合）：

```js
const compose = (...fns) => (...args) => fns.reduceRight((res, fn) => [fn.call(null, ...res)], args)[0];
```

... 别怕！上面给出的是 `compose` 的超级赛亚人 9000 版本。为了便于理解，我们可以放弃上面的可变参数定义，考虑一种更简单的，只将两个函数结合在一起的形式。一旦你理解了下面的定义，你就会进一步发现它也可以用于组合任意数量的函数。

它就是：

```js
const compose2 = (f, g) => x => f(g(x));
```

`f` 和 `g` 都是函数，`x` 是在它们之间通过“管道”传输的值。

`组合`看起来像是在饲养函数。你就是饲养员，选择两个有特点又遭你喜欢的函数，让它们结合，产下一个崭新的函数。组合的用法如下：

```js
const toUpperCase = x => x.toUpperCase();
const exclaim = x => `${x}!`;
const shout = compose(exclaim, toUpperCase);

shout('send in the clowns'); // "SEND IN THE CLOWNS!"
```

两个函数组合之后返回了一个新函数是完全讲得通的：组合某种类型（本例中是函数）的两个元素本就该生成一个该类型的新元素。把两个乐高积木组合起来绝不可能得到一个林肯积木。所以这是有道理的，我们将在适当的时候探讨这方面的一些底层理论。

在 `compose` 的定义中，`g` 将先于 `f` 执行，因此就创建了一个从右到左的数据流。这样做的可读性远远高于嵌套一大堆的函数调用，如果不用组合，`shout` 函数将会是这样的：

```js
const shout = x => exclaim(toUpperCase(x));
```

让代码从右向左运行，而不是由内而外运行，我觉得可以称之为“左倾”（吁——）。我们来看一个顺序很重要的例子：

```js
const head = x => x[0];
const reverse = reduce((acc, x) => [x, ...acc], []);
const last = compose(head, reverse);

last(['jumpkick', 'roundhouse', 'uppercut']); // 'uppercut'
```

`reverse` 反转列表，`head` 取列表中的第一个元素；所以结果就是得到了一个 `last` 函数（译者注：即取列表的最后一个元素），虽然它性能不高。这个组合中函数的执行顺序应该是显而易见的。尽管我们可以定义一个从左向右的版本，但是从右向左执行更加能够反映数学上的含义——是的，组合的概念直接来自于数学课本。实际上，现在是时候去看看所有的组合都有的一个特性了。

```js
// 结合律（associativity）
compose(f, compose(g, h)) === compose(compose(f, g), h);
```

这个特性就是结合律，符合结合律意味着不管你是把 `g` 和 `h` 分到一组，还是把 `f` 和 `g` 分到一组都不重要。所以，如果我们想把字符串变为大写，可以这么写：

```js
compose(toUpperCase, compose(head, reverse));
// 或者
compose(compose(toUpperCase, head), reverse);
```

因为如何为 `compose` 的调用分组不重要，所以结果都是一样的。这也让我们有能力写一个可变的组合（variadic compose），用法如下：

```js
// 前面的例子中我们必须要写两个组合才行，但既然组合是符合结合律的，我们就可以只写一个，
// 而且想传给它多少个函数就传给它多少个，然后让它自己决定如何分组。
const arg = ['jumpkick', 'roundhouse', 'uppercut'];
const lastUpper = compose(toUpperCase, head, reverse);
const loudLastUpper = compose(exclaim, toUpperCase, head, reverse);

lastUpper(arg); // 'UPPERCUT'
loudLastUpper(arg); // 'UPPERCUT!'
```

运用结合律能为我们带来强大的灵活性，还有对执行结果不会出现意外的那种平和心态。至于稍微复杂些的可变组合，也都包含在本书的 `support` 库里了，而且你也可以在类似 [lodash][lodash-website]、[underscore][underscore-website] 以及 [ramda][ramda-website] 这样的类库中找到它们的常规定义。

结合律的一大好处是任何一个函数分组都可以被拆开来，然后再以它们自己的组合方式打包在一起。让我们来重构重构前面的例子：

```js
const loudLastUpper = compose(exclaim, toUpperCase, head, reverse);

// 或

const last = compose(head, reverse);
const loudLastUpper = compose(exclaim, toUpperCase, last);

// 或

const last = compose(head, reverse);
const angry = compose(exclaim, toUpperCase);
const loudLastUpper = compose(angry, last);

// 更多变种...
```

关于如何组合，并没有标准的答案——我们只是以自己喜欢的方式搭乐高积木罢了。通常来说，最佳实践是让组合可重用，就像 `last` 和 `angry` 那样。如果熟悉 Fowler 的《[重构][refactoring-book]》一书的话，你可能会认识到这个过程叫做 “[extract method][extract-method-refactor]”——只不过不需要关心对象的状态。

## pointfree

pointfree 模式指的是，永远不必说出你的数据。咳咳对不起（译者注：此处原文是“Pointfree style means never having to say your data”，源自 1970 年的电影 *Love Story* 里的一句著名台词“Love means never having to say you're sorry”。紧接着作者又说了一句“Excuse me”，大概是一种幽默）。它的意思是说，函数无须提及将要操作的数据是什么样的。一等公民的函数、柯里化（curry）以及组合协作起来非常有助于实现这种模式。

> 注：你可以在附录 C 里找到 pointfree 版本的 `replace` 和 `toLowerCase` 定义。去看一下，别犹豫！

```js
// 非 pointfree，因为提到了数据：word
const snakeCase = word => word.toLowerCase().replace(/\s+/ig, '_');

// pointfree
const snakeCase = compose(replace(/\s+/ig, '_'), toLowerCase);
```

看到 `replace` 是如何被局部调用的了么？这里所做的事情就是通过管道把数据在接受单个参数的函数间传递。利用 curry，我们能够做到让每个函数都先接收数据，然后操作数据，最后再把数据传递到下一个函数那里去。另外注意在 pointfree 版本中，不需要 `word` 参数就能构造函数；而在非 pointfree 的版本中，必须要有 `word` 才能进行一切操作。

我们再来看一个例子。

```js
// 非 pointfree，因为提到了数据：name
const initials = name => name.split(' ').map(compose(toUpperCase, head)).join('. ');

// pointfree
// 注：我们用第九章定义的 `intercalte` 代替了 `join`
const initials = compose(intercalate('. '), map(compose(toUpperCase, head)), split(' '));

initials('hunter stockton thompson'); // 'H. S. T'
```

另外，pointfree 模式能够帮助我们减少不必要的命名，让代码保持简洁和通用。对函数式代码来说，pointfree 是非常好的石蕊试验，因为它能告诉我们一个函数是否是接受输入返回输出的小函数。比如，while 循环是不能组合的。不过你也要警惕，pointfree 就像是一把双刃剑，有时候也能混淆视听。并非所有的函数式代码都是 pointfree 的，不过这没关系。可以使用它的时候就使用，不能使用的时候就用普通函数。

## debug

组合的一个常见错误是，在没有局部调用之前，就组合类似 `map` 这样接受两个参数的函数。

```js
// 错误做法：我们传给了 `angry` 一个数组，根本不知道最后传给 `map` 的是什么东西。
const latin = compose(map, angry, reverse);

latin(['frog', 'eyes']); // error


// 正确做法：每个函数都接受一个实际参数。
const latin = compose(map(angry), reverse);

latin(['frog', 'eyes']); // ['EYES!', 'FROG!'])
```

如果在 debug 组合的时候遇到了困难，那么可以使用下面这个实用的，但是不纯的 `trace` 函数来追踪代码的执行情况。

```js
const trace = curry((tag, x) => {
  console.log(tag, x);
  return x;
});

const dasherize = compose(
  intercalate('-'),
  toLower,
  split(' '),
  replace(/\s{2,}/ig, ' '),
);

dasherize('The world is a vampire');
// TypeError: Cannot read property 'apply' of undefined
```

这里报错了，来 `trace` 下：

```js
const dasherize = compose(
  intercalate('-'),
  toLower,
  trace('after split'),
  split(' '),
  replace(/\s{2,}/ig, ' '),
);

dasherize('The world is a vampire');
// after split [ 'The', 'world', 'is', 'a', 'vampire' ]
```

啊！`toLower` 的参数是一个数组，所以需要先用 `map` 调用一下它。

```js
const dasherize = compose(
  intercalate('-'),
  map(toLower),
  split(' '),
  replace(/\s{2,}/ig, ' '),
);

dasherize('The world is a vampire'); // 'the-world-is-a-vampire'
```

`trace` 函数允许我们在某个特定的点观察数据以便 debug。像 haskell 和 purescript 之类的语言出于开发的方便，也都提供了类似的函数。

组合将成为我们构造程序的工具，而且幸运的是，它背后是有一个强大的理论做支撑的。让我们来研究研究这个理论。

## 范畴学

范畴学（category theory）是数学中的一个抽象分支，能够形式化诸如集合论（set theory）、类型论（type theory）、群论（group theory）以及逻辑学（logic）等数学分支中的一些概念。范畴学主要处理对象（object）、态射（morphism）和变化式（transformation），而这些概念跟编程的联系非常紧密。下图是一些相同的概念分别在不同理论下的形式：

<img src="images/cat_theory.png" />

抱歉，我没有任何要吓唬你的意思。我并不假设你对这些概念都了如指掌，我只是想让你明白这里面有多少重复的内容，让你知道为何范畴学要统一这些概念。

在范畴学中，有一个概念叫做...范畴。有着以下这些组件（component）的搜集（collection）就构成了一个范畴：

  * 对象的搜集
  * 态射的搜集
  * 态射的组合
  * identity 这个独特的态射

范畴学抽象到足以模拟任何事物，不过目前我们最关心的还是类型和函数，所以让我们把范畴学运用到它们身上看看。

**对象的搜集**

对象就是数据类型，例如 `String`、`Boolean`、`Number` 和 `Object` 等等。通常我们把数据类型视作所有可能的值的一个集合（set）。像 `Boolean` 就可以看作是 `[true, false]` 的集合，`Number` 可以是所有实数的一个集合。把类型当作集合对待是有好处的，因为我们可以利用集合论（set theory）处理类型。

**态射的搜集**

态射是标准的、普通的纯函数。

**态射的组合**

你可能猜到了，这就是本章介绍的新玩意儿——`组合`。我们已经讨论过 `compose` 函数是符合结合律的，这并非巧合，结合律是在范畴学中对任何组合都适用的一个特性。

这张图展示了什么是组合：

<img src="images/cat_comp1.png" />
<img src="images/cat_comp2.png" />

这里有一个具体的例子：

```js
const g = x => x.length;
const f = x => x === 4;
const isFourLetterWord = compose(f, g);
```

**identity 这个独特的态射**

让我们介绍一个名为 `id` 的实用函数。这个函数接受随便什么输入然后原封不动地返回它：

```js
const id = x => x;
```

你可能会问“这到底哪里有用了？”。别急，我们会在随后的章节中拓展这个函数的，暂时先把它当作一个可以替代给定值的函数——一个假装自己是普通数据的函数。

`id` 函数跟组合一起使用简直完美。下面这个特性对所有的一元函数（unary function）（一元函数：只接受一个参数的函数） `f` 都成立：

```js
// identity
compose(id, f) === compose(f, id) === f;
// true
```

嘿，这就是实数的单位元（identity property）嘛！如果这还不够清楚直白，别着急，慢慢理解它的无用性。很快我们就会到处使用 `id` 了，不过暂时我们还是把它当作一个替代给定值的函数。这对写 pointfree 的代码非常有用。

好了，以上就是类型和函数的范畴。不过如果你是第一次听说这些概念，我估计你还是有些迷糊，不知道范畴到底是什么，为什么有用。没关系，本书全书都在借助这些知识编写示例代码。至于现在，就在本章，本行文字中，你至少可以认为它向我们提供了有关组合的知识——比如结合律和单位律。

除了类型和函数，还有什么范畴呢？还有很多，比如我们可以定义一个有向图（directed graph），以节点为对象，以边为态射，以路径连接为组合。还可以定义一个实数类型（Number），以所有的实数为对象，以 `>=` 为态射（实际上任何偏序（partial order）或全序（total order）都可以成为一个范畴）。范畴的总数是无限的，但是要达到本书的目的，我们只需要关心上面定义的范畴就好了。至此我们已经大致浏览了一些表面的东西，必须要继续后面的内容了。

## 总结

组合像一系列管道那样把不同的函数联系在一起，数据就可以也必须在其中流动——毕竟纯函数就是输入对输出，所以打破这个链条就是不尊重输出，就会让我们的应用一无是处。

我们认为组合是高于其他所有原则的设计原则，这是因为组合让我们的代码简单而富有可读性。另外范畴学将在应用架构、模拟副作用和保证正确性方面扮演重要角色。

现在我们已经有足够的知识去进行一些实际的练习了，让我们来编写一个示例应用。

## 练习

在下列练习中，我们使用形如

```js
{
  name: 'Aston Martin One-77',
  horsepower: 750,
  dollar_value: 1850000,
  in_stock: true,
}
```

的车辆对象。

1. 用 `compose` 重写下面的函数

```js
const isLastInStock = (cars) => {  
  const lastCar = last(cars);  
  return prop('in_stock', lastCar);  
};  
```

2. 用帮助函数 `average` 重写 `averageDollarValue` 函数

```js
const average = xs => reduce(add, 0, xs) / xs.length;
const averageDollarValue = (cars) => {  
  const dollarValues = map(c => c.dollar_value, cars);  
  return average(dollarValues);  
};  
```

3. 用 `compose` 重写 `fastestCar` 函数

> 提示：`append` 函数可能会很有用。

```js
const fastestCar = (cars) => {  
  const sorted = sortBy(car => car.horsepower);  
  const fastest = last(sorted);  
  return concat(fastest.name, ' is the fastest');  
};  
```

