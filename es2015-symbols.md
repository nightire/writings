Symbols 是 ES2015 引入了一个新的原生类型。它的主要用处在于扩展 `Object` 的功能
性同时维持其向后兼容性。

```javascript
const exampleSymbol = Symbol()
```

以上是“自定义符号”，通过内建工厂函数 `Symbol()` 创建。除此之外 `Symbol` 对象还
拥有一些内置的“常用符号”，这些符号用来与原生 JavaScript 代码交互或是修改它们的
行为。最常见的符号是 `Symbol.iterator`，用于给对象的值添加“迭代”的意义。

作为表达唯一性的原生类型，JavaScript 能使用 symbols 作为对象的属性名称来增加对
象的功能性并且不用担心会出现属性冲突，即使已经存在同名的字符串类型的属性也没关
系，因为 `Symbol` 和 `String` 是不同的而且 `Symbol` 是唯一的。

```javascript
Symbol() !== Symbol()
```

Symbols 允许开发者去扩展或是影响 JavaScript 引擎的行为。比如说，使用常用符号
`Symbol.iterator`（也用 `@@iterator` 来表示），一个对象可以描述当 `for...of` 或
是展开操作符作用自身是提供什么数据。

```javascript
class Data {
  constructor(...rest) {
    this._data = rest;
  }

  *[Symbol.iterator]() {
    for (let item of this._data) {
      yield item;
    }
  }
}

console.log([... new Data(1, 2, 3)]); // => [1, 2, 3]
```

在这个例子中，当 `new Data(1, 2, 3)` 的时候，事实上创建了一个 Iterable 对象：
`{"_data": [1, 2, 3]}`。我们再用 `[...new Data(1, 2, 3)]` 的语法就等于“展开”这
个 Iterable 对象，这时 JavaScript 引擎查找 `@@iterator` 的规则并应用于内部的解
释流程，于是我们得到了 `[1, 2, 3]` 的结果。 

这种针对行为的改变在我们工作于主要的语言功能时会更加显著。例如，`@@toStringTag`
是一个可以让你定义如何描述对象的符号。

```javascript
let arraylike = {
  0: 'zero',
  1: 'one',
  length: 2,
  [Symbol.toStringTag]: 'Array',
  *[Symbol.iterator]() {
    for (let i = 0; i < this.length; i++) {
      yield this[i]
    }
  }
}

console.log(arraylike.toString())  // [object Array]
```

还有 `@@replace` 可以让对象做到以前正则表达式都做不到的事情。

```javascript
const Summary = {
  maxLength: 30,
  defaultEnding: '...',
  [Symbol.replace](string, replacement) {
    if (string.length > this.maxLength) {
      replacement = replacement || this.defaultEnding
      const length = this.maxLength - replacement.length
      return string.substring(0, length) + replacement
    }
    return string
  }
}

const sentence = 'This sentence is way too long and will be cummarized!'

console.log(sentence.replace(Summary))  // This sentence is way too lo...
```
