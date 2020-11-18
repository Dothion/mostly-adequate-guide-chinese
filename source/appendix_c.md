# 附录 C: 实用函数的 pointfree 实现

本附录将会给出几种传统 JavaScript 函数的 pointfree 版本实现。在随书练习中，这些函数看起来运转的都不错。请注意，这里的实现可能不是最快或者效率最高的，只用于教育目的。

如果需要这些函数适用于生产环境的版本，可以选用 [ramda](https://ramdajs.com/)、[lodash](https://lodash.com/) 或 [folktale](http://folktale.origamitower.com/)。

请注意，这些实现依赖于[附录 A](./appendix_a.md) 中实现的 `curry` 和 `compose` 函数。

## add 

```javascript
// add :: Number -> Number -> Number
const add = curry((a, b) => a + b);
```

## append

```javascript
// append :: String -> String -> String
const append = flip(concat);
```

## chain

```javascript
// chain :: Monad m => (a -> m b) -> m a -> m b
const chain = curry((fn, m) => m.chain(fn));
```

## concat

```javascript
// concat :: String -> String -> String
const concat = curry((a, b) => a.concat(b));
```

## eq

```javascript
// eq :: Eq a => a -> a -> Boolean
const eq = curry((a, b) => a === b);
```

## filter

```javascript
// filter :: (a -> Boolean) -> [a] -> [a]
const filter = curry((fn, xs) => xs.filter(fn));
```

## flip

```javascript
// flip :: (a -> b -> c) -> b -> a -> c
const flip = curry((fn, a, b) => fn(b, a));
```

## forEach 

```javascript
// forEach :: (a -> ()) -> [a] -> ()
const forEach = curry((fn, xs) => xs.forEach(fn));
```

## head

```javascript
// head :: [a] -> a
const head = xs => xs[0];
```

## intercalate

```javascript
// intercalate :: String -> [String] -> String
const intercalate = curry((str, xs) => xs.join(str));
```

## join

```javascript
// join :: Monad m => m (m a) -> m a
const join = m => m.join();
```

## last

```javascript
// last :: [a] -> a
const last = xs => xs[xs.length - 1];
```

## map

```javascript
// map :: Functor f => (a -> b) -> f a -> f b
const map = curry((fn, f) => f.map(fn));
```

## match

```javascript
// match :: RegExp -> String -> Boolean
const match = curry((re, str) => re.test(str));
```

## prop 

```javascript
// prop :: String -> Object -> a
const prop = curry((p, obj) => obj[p]);
```

## reduce

```javascript
// reduce :: (b -> a -> b) -> b -> [a] -> b
const reduce = curry((fn, zero, xs) => xs.reduce(fn, zero));
```

## replace

```javascript
// replace :: RegExp -> String -> String -> String
const replace = curry((re, rpl, str) => str.replace(re, rpl));
```

## reverse

```javascript
// reverse :: [a] -> [a]
const reverse = x => (Array.isArray(x) ? x.reverse() : x.split('').reverse().join(''));
```

## safeHead

```javascript
// safeHead :: [a] -> Maybe a
const safeHead = compose(Maybe.of, head);
```

## safeLast

```javascript
// safeLast :: [a] -> Maybe a
const safeLast = compose(Maybe.of, last);
```

## safeProp

```javascript
// safeProp :: String -> Object -> Maybe a
const safeProp = curry((p, obj) => compose(Maybe.of, prop(p))(obj));
```

## sequence

```javascript
// sequence :: (Applicative f, Traversable t) => (a -> f a) -> t (f a) -> f (t a)
const sequence = curry((of, f) => f.sequence(of));
```

## sortBy

```javascript
// sortBy :: Ord b => (a -> b) -> [a] -> [a]
const sortBy = curry((fn, xs) => xs.sort((a, b) => {
  if (fn(a) === fn(b)) {
    return 0;
  }

  return fn(a) > fn(b) ? 1 : -1;
}));
```

## split

```javascript
// split :: String -> String -> [String]
const split = curry((sep, str) => str.split(sep));
```

## take

```javascript
// take :: Number -> [a] -> [a]
const take = curry((n, xs) => xs.slice(0, n));
```

## toLowerCase

```javascript
// toLowerCase :: String -> String
const toLowerCase = s => s.toLowerCase();
```

## toString

```javascript
// toString :: a -> String
const toString = String;
```

## toUpperCase

```javascript
// toUpperCase :: String -> String
const toUpperCase = s => s.toUpperCase();
```

## traverse

```javascript
// traverse :: (Applicative f, Traversable t) => (a -> f a) -> (a -> f b) -> t a -> f (t b)
const traverse = curry((of, fn, f) => f.traverse(of, fn));
```

## unsafePerformIO

```javascript
// unsafePerformIO :: IO a -> a
const unsafePerformIO = io => io.unsafePerformIO();
```
