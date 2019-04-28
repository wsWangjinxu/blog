---
title: JavaScript类型的细节
date: 2019-04-27 09:04:46
category: JavaScript
tags: 类型
---

## Undefined 与 Null

Undefined 类型表示未定义，它的类型只有一个值，就是 Undefined。任何变量在赋值前都是 Undefined 类型、值为 undefined，一般可以用全局变量 undefined 来表达这个值，或者用 void 运算将任意一个表达式变成 undefined 值。

因为 JavaScript 中 undefined 是一个变量，而并非一个关键字，这是 JavaScript 语言公认的设计失误之一，所以，为了避免无意中被错改，建议使用`void 0`来获取 undefined 值。虽然现代浏览器都对这个变量的修饰符做了限定，不能修改，但是还是需要注意。

Null 类型也是只有一个值，就是 null，它的语义表示空值，**表示定义了但是为空**。与 undefined 不同，null 是 JavaScript 关键字，可以放心使用。

## String

JavaScript 中的字符是永远无法变更的，字符串一旦被构造出来，无法用任何方式改变字符串的内容，所以字符串具有值类型的特征。String 用于表示文本数据，String 有最大长度是`2^53 - 1`，这个在一般开发中是够用的，但是这个所谓的最大长度并不是我们理解中的字符数。因为 String 是字符串的 UTF-16 编码，我们字符串的操作`charAt`、`charCodeAt`、`length` 等方法针对的都是 UTF16 编码，所以，字符串的最大长度，实际上是受字符串的编码长度影响的。

> 现行的字符集国际标准是以 Unicode 的方式表示的，每一个 Unicode 的码点表示一个字符。UTF 是 Unicode 的编码方式，规定了码点在计算机中的表示方法，常见的有 UTF16 和 UTF8。Unicode 的码点通常用 U+码点 来表示，其中码点是十六进制的码点值。0-65535的码点被称为基本字符区域(BMP)。

JavaScript 字符串把每个 UTF16 单元当作一个字符处理，所以超出 BMP 的字符时，应该格外小心。

## Number

Number 类型基本符合 IEEE 754-2008 规定的双精度浮点数规则，但是为了表达几个额外的语言场景，规定了几个例外情况：

+ NaN，占用了 9007199254740990，这原本是符合 IEEE 规则的数字
+ Infinity，无穷大
+ -Infinity，负无穷大

另外，值得注意的是 +0 和 -0， 在加法运算中它们没有区别，但是除法的场合则需要特别留意区分，区分 +0 或 -0 的方式是检测 `1/x` 的结果是 Infinity 还是 -Infinity。

根绝双精度浮点数的定义，Number 类型中有效的整数范围是 -0x1fffffffffffff 至 0x1fffffffffffff，所以 Number 无法精确表示此范围外的整数。非整数的 Number 类型无法用 == 和 === 比较大小。这是浮点运算的特点，浮点运算的精度问题导致等式左右的结果并不是严格相等，而是相差了微小的值。

```
// 错误的比较方法
0.1 + 0.2 == 0.3 // false

// 正确的比较方法
Math.abs(0.1 + 0.2 -0.3) <= Number.EPSILON // true 表示相等
```

## Symbol

Symbol 是 ES6 中引入的新类型，它是一切字符串的对象key的集合，在 ES6 规范中国，整个对象系统被用 Symbol 重塑。这里只关注类型本身。

Symbol 可以具有字符串类型的描述，但是即使描述是相同的，Symbol 也不相等。

```
// 使用全局 Symbol 函数创建 Symbol
var mySymbol = Symbol("symbol description");

// 使用 Symbol.iterator 来定义 for...of 在对象上的行为
var o = new Object
o[Symbol.iterator] = function () {
    var v = 0
    return {
        next: function () {
            return { value: v++, done: v > 10 }
        }
    }
};

for (var v of o) {
    console.log(v); // 0, 1 ... 9
}

代码中定义了 iterator 之后，用 for (var v of o) 就可以调用这个函数，然后可以根据函数的行为，产生一个 for ... of 的行为。
```

## Object

Object 是 JavaScript 中最复杂的类型，也是 JavaScript 的核心机制之一。在 JavaScript 中，对象的定义是“属性的集合”。属性分为数据属性和访问器属性，二者都是 key-value 的结构，key 可以是字符串或者 Symbol 类型。

JavaScript 中的几个基本类型的构造器：

+ Number
+ String
+ Boolean
+ Symbol

前三个是两用的，当跟 new 搭配的时候，产生对象，当直接调用时，它们表示强制类型转换。

Symbol 函数比较特殊，直接用 new 调用它会抛出错误，但它仍然是 Symbol 对象的构造器。

JavaScript 语言设计上试图模糊对象和基本类型之间的关系，日常中可以把对象的方法在基本类型上使用，甚至在原型上添加的方法，都可以应用于基本类型。

```
// 在基本类型上调用方法
console.log("abc".charAt(0)); // a

// 在 Symbol 原型上添加方法，在任何 Symbol 类型变量都可以调用
Symbol.prototype.hello = () => console.log("hello");

var a = Symbol("a");
console.log(typeof a); // symbol，a 并非对象
a.hello(); // hello，有效
```

`.`运算符提供了装箱操作，它会根据基础类型构造一个临时的包装对象，使得我们能在基础类型上调用对应对象的方法。

## 类型转换

因为 JS 是弱类型语言，所以类型转换发生非常频繁，大部分我们熟悉的运算符会先进行类型转换。大部分类型转换符合直觉，但是如果不去理解类型转换的严格定义，很容易造成一些代码中的判断失误。

类型转换规则表：

```
|         |    Null   |  Undefined  | Boolean(True) | Boolean(False) |      Number    |     String     |  Symbol   | Object |
| ------- | --------- | ----------- | ------------- | -------------- | -------------- | -------------- | --------- | ------ |
| Boolean |   FALSE   |    FALSE    |       -       |        -       |  0/NaN-false   |    ""-fase     |   TRUE    |  TRUE  |
|  Number |     0     |      NaN    |       1       |        0       |       -        | StringToNumber | TypeError | 拆箱转换 |
|  String |  "null"   | "undefined" |     TRUE      |      FALSE     | NumberToString |       -        | TypeError | 拆箱转换 |
|  Object | TypeError |  TypeError  |    装箱转换    |      装箱转换    |     装箱转换    |     装箱转换    |  装箱转换   |   -    |
```


在上面的表格中，较为复杂的是 Number 和 String 之间的转换，以及对象跟基本类型之间的转换。

### StringToNumber

字符串到数字的类型转换，存在一个语法结构，类型转换支持支持十进制、二进制、八进制和十六进制，比如：

+ 30
+ 0b111
+ 0o13
+ 0xFF

此外，JavaScript 支持的字符串语法还包括科学计数法，可以使用大写或者小写 e 来表示：

+ 1e3
+ -1e-2

需要注意的是，在 StringToNumber 的情况下，parseInt 和 parseFloat 在不传入参数的情况下，只支持 16 进制前缀“0x”，而且会忽略非数字字符，也不支持科学计数法。在一些古老的浏览器环境中，parseInt 还支持 0 开头 的数字作为 8 进制前缀，这是很多错误的涞源。所以，在任何环境下，都建议传入 parseInt 的第二个参数，而 parseFloat 则直接把原字符串作为十进制来解析，它不会引入任何的其他进制。多数情况下，Number 是比 parseInt 和 parseFloat 更好的选择。

### NumnberToString

在较小的范围内，数字到字符串的转换是完全符合直觉的十进制表示。当 Number 绝对值较大或者较小时，字符串表示则是使用科学计数法表示，保证了产生的字符串不会过长。

### 装箱转换

每一种基本类型 Number、String、Boolean、Symbol 在对象中都有对应的类，所谓的装箱转换，正是把基本类型转换为对应一个对象。全局的 Symbol 函数无法使用 new 来调用，但我们仍可以利用装箱机制来得到一个 Symbol 对象。

```
// 利用 call 方法来强制产生装箱
var symbolObject = (function() {return this;}).call(Symbol("a"));

console.log(typeof symbolObject) // object
console.log(symbolObject instanceof Symbol) // true
console.log(symbolObject.constructor == Symbol) // true
```

装箱机制会频繁产生临时对象，在一些性能要求较高的场景下，应该尽量避免对基本类型做装箱转换。

使用内置的 Object 函数，可以在 JavaScript 代码中显式调用装箱能力。

```
var symbolObject = Object(Symbol("a"));

console.log(Object.prototype.toString.call(symbolObject)); // [Object Symbol]
```

在 JavaScript 中，没有任何方法可以更改私有的 Class 属性，因此 Object.prototype.toString 是可以准确识别对象对应的基本类型的方法，它比 instanceof 更加准确。需要注意的是，call 本身会产生装箱操作，所以需要配合 typeof 来区分基本类型还是对象类型。

### 拆箱转换

在 JavaScript 标准中，规定了 ToPrimitive 函数，它是对象类型到基本类型的转换（即，拆箱转换）。对象到 String 和 Number 的转换都是把对象变成基本类型，再从基本类型转换为对应的 String 或者 Number。

拆箱转换都会尝试调用 valueOf 和 toString 来获得拆箱后的基本类型。如果 valueOf 和 toString 都不存在，或者没有返回基本类型，则会产生类型错误 TypeError。

```
// 到 Number 的拆箱转换
var o = {
    valueOf: () => {console.log("valueOf"); return {}},
    toString: () => {console.log("toString"); return {}}
}

0 * 2

// valueOf
// toString
// TypeError

// 到 String 的拆箱转换

String(o)

// toString
// valueOf
// TypeError
```

这里调用 toString 与 调用 valueOf 的顺序，与规范中的内部实现有关。规范指出，类型转换的内部实现是通过`ToPrimitive (input [, PreferredType])`方法进行转换的，这个方法的作用就是将 input 转换成一个非对象类型。参数 preferredType 进来，默认的是“number”，如果值是“string”，那就先执行“toString”，后执行“valueOf”，如果参数是“string”，则调换顺序。

```
// 规范中加法操作没有传递值，所以默认参数“number”起效
o + "string"

// valueOf
// toString
// TypeError
```

在 ES6 之后，允许对象通过显示指定 @@toPrimitive Symbol 来覆盖原有的行为。

```
var o = {
    valueOf: () => {console.log("valueOf"); return {}},
    toString: () => {console.log("toString"); return {}}
}

o[Symbol.toPrimitive] = () => {console.log("toPrimitive"); return "hello"}

console.log(o + "")

// toPrimitive
// hello
```

### 运行时类型

运行时类型(标准中的规定)与 typeof 运算返回的操作数的类型比较

| 示例表达式 | typeof 结果 | 运行时类型 |
| -------- | ----------- | --------- |
| null | object | Null |
| {} | object | Object |
| (function(){}) | function | Object |
| 3 | number | Number |
| "ok" | string | String |
| true | boolean | Boolean |
| void 0 | undefined | Undefined |
| Symbol("a") | symbol | Symbol |




