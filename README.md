![](./source/img/short_cover.png)

# 函数式编程指北

[![](https://readthedocs.org/projects/magc/badge/?version=latest)](http://magc.rtfd.io/)

> This is the Simplified Chinese translation of *[mostly-adequate-guide](https://github.com/MostlyAdequate/mostly-adequate-guide)*, thank Professor [Franklin Risby](https://github.com/DrBoolean) for his great work!

## 关于本书

这本书的主题是函数范式（functional paradigm），我们将使用 JavaScript 这个世界上最流行的函数式编程语言来讲述这一主题。有人可能会觉得选择 JavaScript 并不明智，因为当前的主流观点认为它是一门命令式（imperative）的语言，并不适合用来讲函数式。但我认为，这是学习函数式编程的最好方式，因为：

 * **你很有可能在日常工作中使用它**

    这让你有机会在实际的编程过程中学以致用，而不是在空闲时间用一门深奥的函数式编程语言做一些玩具性质的项目。

 * **你不必从头学起就能开始编写程序**

    在纯函数式编程语言中，你必须使用 monad 才能打印变量或者读取 DOM 节点。JavaScript 则简单得多，可以作弊走捷径，因为毕竟我们的目的是学写纯函数式代码。JavaScript 也更容易入门，因为它是一门混合范式的语言，你随时可以在感觉吃力的时候回退到原有的编程习惯上去。

 * **这门语言完全有能力书写高级的函数式代码**

    只需借助一到两个微型类库，JavaScript 就能模拟 Scala 或 Haskell 这类语言的全部特性。虽然面向对象编程（Object-oriented programing）主导着业界，但很明显这种范式在 JavaScript 里非常笨拙，用起来就像在高速公路上露营或者穿着橡胶套鞋跳踢踏舞一样。我们不得不到处使用 `bind` 以免 `this` 不知不觉地变了，语言里没有类可以用（目前还没有），我们还发明了各种变通方法来应对忘记调用 `new` 关键字后的怪异行为，私有成员只能通过闭包（closure）才能实现，等等。对大多数人来说，函数式编程看起来更加自然。

以上说明，强类型的函数式语言毫无疑问将会成为本书所示范式的最佳试验场。JavaScript 是我们学习这种范式的一种手段，将它应用于什么地方则完全取决于你自己。幸运的是，所有的接口都是数学的，因而也是普适的。最终你会发现你习惯了 swiftz、scalaz、haskell 和 purescript，以及其他各种数学偏向的语言。


## 未来计划

* 第 1 部分是基础知识。这是初版草稿，所以我会及时更正发现的的错误。欢迎提供帮助！
* 第 2 部分讲述类型类（type class），比如 functor 和 monad，最后会讲到到 traversable。我希望能塞进来一些 monad transformer 相关的知识，再写一个纯函数的应用。
* 第 3 部分将开始游走于编程实践与学院学究之间。我们将学习 comonad、f-algebra、free monad、米田定理以及其他一些范畴学概念。


## LICENSE

本作品采用 [CC BY-SA 4.0](http://creativecommons.org/licenses/by/4.0/) 协议进行许可。


## Epub 构建

```
git clone https://github.com/Dothion/mostly-adequate-guide-chinese.git
cd mostly-adequate-guide-chinese
pip install -r requirements.txt

# For Windows
.\make.bat epub
copy .\build\epub\sphinx.epub '.\函数式编程指北.epub'
```

## 在线阅读

你可以在 [Read the Docs](http://magc.rtfd.io/) 上阅读本书，或[下载 epub ](https://magc.readthedocs.io/_/downloads/zh_CN/latest/epub/)以获得更好的阅读体验。


## 鸣谢

- [MostlyAdequate/mostly-adequate-guide](https://github.com/MostlyAdequate/mostly-adequate-guide)

- [llh911001/mostly-adequate-guide-chinese](https://github.com/llh911001/mostly-adequate-guide-chinese)

