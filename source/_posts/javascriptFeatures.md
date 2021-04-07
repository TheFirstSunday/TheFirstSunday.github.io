---
title: JavaScript features in 2021
date: 2021-03-18 12:02:08
categories: 'Diary'
tags: 'JavaScript'
---

在撰写本文时，以下新的JavaScript提案功能已进入第4阶段，并且几乎肯定会包含在ES2021中。您已经可以开始在[最新版本的浏览器，Node.js和Babel中使用](https://kangax.github.io/compat-table/es2016plus/)。

注意：ECMAScript是JavaScript所基于的标准，由TC39委员会管理。ECMAScript始终是一个不需要的名称，这会使所有内容对初学者都感到困惑。人们经常谈论JavaScript功能，但参考的是ECMAScript规范。

## Numeric Separators

``` JavaScript

    1_000_000_000 // decimal literals
    0b1010_0001 // binary literals
    101_475_938.38 // floating-point literals
    0.000_001 // in fractional part
    1e10_000 // in exponent part
```

## Logical Assignment

支持与新的运营逻辑分配 &&=，||= 和 ??=。与它们的 [mathematical and bitwise counterparts](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators#assignment_operators)不同，逻辑分配遵循其各自逻辑操作的短路行为。仅当逻辑运算将评估右侧时，它们才执行分配。

``` JavaScript

    // falsy: false, 0, -0, 0n, "", null, undefined, and NaN
    // truthy: all values are truthy unless defined as falsy
    // nullish: null or undefined

    a ||= b
    // Logical OR assignment
    // Equivalent to: a || (a = b);
    // Only assigns if a is falsy

    a &&= b
    // Logical AND assignment
    // Equivalent to: a && (a = b);
    // Only assigns if a is truthy

    a ??= b
    // Logical nullish assignment
    // Equivalent to: a ?? (a = b);
    // Only assigns if a is nullish

    // examples: 

    // Logical Assignment Operator with && operator
    let a = 1
    const b = 2
    a &&= b
    console.log(a) // 2
    // Equivalent to
    // 1. a && (a = b)
    // 2. if (a) a = b

    // Logical Assignment Operator with || operator
    let a
    const b = 2
    a ||= b
    console.log(a) // 2
    // Equivalent to
    // 1. a || (a = b)
    // 2. if (!a) a = b

    // Logical Assignment Operator with ?? operator
    let a
    const b = 2
    a ??= b
    console.log(a) // 2
    a = false
    a ??= b
    console.log(a) // false
    // Equivalent to
    // a ?? (a = b)
```

## Weak references and finalizers

此功能包含两个高级对象WeakRef和FinalizationRegistry。根据使用情况，这些接口可以单独使用，也可以一起使用。正确使用它们需要仔细考虑，如果可能，最好避免使用它们。

WeakRef 是一个更高级的 API，它提供了真正的弱引用，Weakref 实例具有一个方法 deref，该方法返回被引用的原始对象，如果原始对象已被收集，则返回 undefined 对象。

``` JavaScript

    // Create a WeakRef object referring to a given target object
    const ref = new WeakRef(targetObject)

    // Return the WeakRef instance's target object, or undefined if the target object has been garbage-collected
    const obj = ref.deref()
```

使用FinalizationRegistry对象可以在垃圾回收对象时请求回调。

``` JavaScript

    // Create a registry object that uses the given callback
    const registry = new FinalizationRegistry([callback])

    // Register an object with a registry instance so that if the object is garbage-collected, the registry's callback may get called
    registry.register(target, heldValue, [unregisterToken])

    // Unregister a target object from a registry instance
    registry.unregister(unregisterToken)
```

## Promise.any()

Promise.any 方法和 Promise.race 类似——只要给定的迭代中的一个 promise 成功，就采用第一个 promise 的值作为它的返回值，但与 Promise.race 的不同之处在于——它会等到所有 promise 都失败之后，才返回失败的值：

``` JavaScript

    Promise.any(promises).then(
        (first) => {
            // Any of the promises was fulfilled.
        },
        (error) => {
            // All of the promises were rejected.
            // error instanceof AggregateError
        }
    )
```

## String.prototype.replaceAll

当前，如果不使用全局正则表达式，就无法替换字符串中子字符串的所有实例。与字符串参数一起使用时，String.prototype.replace仅影响首次出现。

String.prototype.replaceAll() 将为开发人员提供一种简单的方法来完成此常见的基本操作。

``` JavaScript

    '139****1234'.replaceAll('*', 'x') // '139xxxx1234'
    'abcabc'.replaceAll(/b/g, 'A') // 'aAcaAc'
```

## 参考文章

[数字分隔符](https://ageek.dev/js-numeric-separators)
[逻辑分配](https://ageek.dev/js-logical-assignment)
[Weak references and finalizers](https://github.com/tc39/proposal-weakrefs)
[Promose.any()](https://github.com/tc39/proposal-promise-any)
[String.prototype.replaceAll()](https://github.com/tc39/proposal-string-replaceall)