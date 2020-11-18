# 附录 A: 常用 JavaScript 函数

本附录将会给出书中所述的几种基本函数的实现。请注意，这里的实现可能不是最快或者效率最高的，只用于教育目的。

如果需要这些函数适用于生产环境的版本，可以选用 [ramda](https://ramdajs.com/)、[lodash](https://lodash.com/), 或者 [folktale](http://folktale.origamitower.com/).

请注意，其中一些函数依赖于[附录 B](./appendix_b.md) 中实现的数据类型。

## always

```javascript
// always :: a -> b -> a
const always = curry((a, b) => a);
```

## compose

```javascript
// compose :: ((y -> z), (x -> y),  ..., (a -> b)) -> a -> z
const compose = (...fns) => (...args) => fns.reduceRight((res, fn) => [fn.call(null, ...res)], args)[0];
```

## curry

```javascript
// curry :: ((a, b, ...) -> c) -> a -> b -> ... -> c
function curry(fn) {
  const arity = fn.length;

  return function $curry(...args) {
    if (args.length < arity) {
      return $curry.bind(null, ...args);
    }

    return fn.call(null, ...args);
  };
}
```

## either

```javascript
// either :: (a -> c) -> (b -> c) -> Either a b -> c
const either = curry((f, g, e) => {
  if (e.isLeft) {
    return f(e.$value);
  }

  return g(e.$value);
});
```

## identity

```javascript
// identity :: x -> x
const identity = x => x;
```

## inspect

```javascript
// inspect :: a -> String
const inspect = (x) => {
  if (x && typeof x.inspect === 'function') {
    return x.inspect();
  }

  function inspectFn(f) {
    return f.name ? f.name : f.toString();
  }

  function inspectTerm(t) {
    switch (typeof t) {
      case 'string':
        return `'${t}'`;
      case 'object': {
        const ts = Object.keys(t).map(k => [k, inspect(t[k])]);
        return `{${ts.map(kv => kv.join(': ')).join(', ')}}`;
      }
      default:
        return String(t);
    }
  }

  function inspectArgs(args) {
    return Array.isArray(args) ? `[${args.map(inspect).join(', ')}]` : inspectTerm(args);
  }

  return (typeof x === 'function') ? inspectFn(x) : inspectArgs(x);
};
```

## left

```javascript
// left :: a -> Either a b
const left = a => new Left(a);
```

## liftA2

```javascript
// liftA2 :: (Applicative f) => (a1 -> a2 -> b) -> f a1 -> f a2 -> f b
const liftA2 = curry((fn, a1, a2) => a1.map(fn).ap(a2));
```

## liftA3

```javascript
// liftA3 :: (Applicative f) => (a1 -> a2 -> a3 -> b) -> f a1 -> f a2 -> f a3 -> f b
const liftA3 = curry((fn, a1, a2, a3) => a1.map(fn).ap(a2).ap(a3));
```

## maybe

```javascript
// maybe :: b -> (a -> b) -> Maybe a -> b
const maybe = curry((v, f, m) => {
  if (m.isNothing) {
    return v;
  }

  return f(m.$value);
});
```

## nothing

```javascript
// nothing :: Maybe a
const nothing = Maybe.of(null);
```

## reject

```javascript
// reject :: a -> Task a b
const reject = a => Task.rejected(a);
```
