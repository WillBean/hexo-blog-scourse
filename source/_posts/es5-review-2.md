---
title: ES5规范回顾系列(二)——内置对象
date: 2017-07-02 14:36:03
tags:
- ES5
- Specification
---

下文摘自[ES5/标准 ECMAScript 内置对象](https://www.w3.org/html/ig/zh/wiki/ES5/builtins)

## 全局对象

唯一的全局对象在控制进入任何执行环境前被创建。

除非另外指明，全局对象的标准内置属性拥有特性 {[[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: true}。

全局对象没有 [[Construct]] 内部属性 ; 全局对象不可能当做构造器用 new 运算符调用。

全局对象没有 [[Call]] 内部属性，全局对象不可能当做函数来调用。

全局对象的 [[Prototype]] 和 [[Class]] 内部属性值是依赖于实现的。

除了本规范定义的属性之外，全局对象还可以拥有额外的宿主定义的属性。全局对象可包含一个值是全局对象自身的属性；例如，在 HTML 文档对象模型中全局对象的 window 属性是全局对象自身。 

### 全局对象的值属性

#### NaN

NaN 的值是 NaN。这个属性拥有特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }。

#### Infinity

Infinity 的值是 +∞。这个属性拥有特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }。

#### undefined

undefined 的值是 undefined。这个属性拥有特性 <{ [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }。

### 全局对象的函数属性

#### eval (x)

#### parseInt (string, radix)

#### parseFloat (string)

#### isNaN (number)

#### isFinite (number)

### 处理 URI 的函数属性

#### decodeURI (encodedURI)

decodeURI 函数计算出一个新版 URI，将 URI 中可能是 encodeURI 函数引入的每个转义序列和 UTF-8 编码组替换为代表它们的字符。不是 encodeURI 导入的转义序列不会被替换。 

#### decodeURIComponent (encodedURIComponent)

decodeURIComponent 函数计算出一个新版 URI，将 URI 中可能是 encodeURIComponent 函数引入的每个转义序列和 UTF-8 编码组替换为代表它们的字符。 

#### encodeURI (uri)

encodeURI 函数计算出一个新版 URI，将 URI 中某些字符的每个实例替换为代表这些字符 UTF-8 编码的一个，两个或三个转义序列。

#### encodeURIComponent (uriComponent)

encodeURIComponent 函数计算出一个新版 URI，将 URI 中某些字符的每个实例替换为代表这些字符 UTF-8 编码的一个，两个或三个转义序列。 

*encodeURI 和 decodeURI 函数操作的是完整的 URI；这俩函数假定 URI 中的任何保留字符都有特殊意义，所有不会编码它们。encodeURIComponent 和 decodeURIComponent 函数操作的是组成 URI 的个别组件；这俩函数假定任何保留字符都代表普通文本，所以必须编码它们，所以它们出现在组成一个完整 URI 的组件里面时不会解释成保留字符了。

### 全局对象的构造器属性 

#### Object ( . . . )

##### 作为函数调用 Object 构造器

当把 Object 当做一个函数来调用，而不是一个构造器，它会执行一个类型转换。 

###### Object ( [ value ] )

当以一个参数 value 或者无参数调用 Object 函数，采用如下步骤：

1. 如果 value 是 null、undefined 或未指定，则创建并返回一个新 Object 对象，这个对象与仿佛用相同参数调用标准内置的 Object 构造器 的结果一样。
2. 返回 ToObject(value)。

##### Object 构造器

当 Object 是 new 表达式调用的一部分时，它是一个构造器，可创建一个对象。

###### new Object ( [ value ] )

当以一个参数 value 或者无参数调用 Object 构造器，采用如下步骤：

    1. 如果提供了 value，则
		1. 如果 Type(value) 是 Object，则
			1. 如果 value 是个原生 ECMAScript 对象，不创建新对象，简单的返回 value。
			2. 如果 value 是宿主对象，则采取动作和返回依赖实现的结果的方式可以使依赖于宿主对象的。
		2. 如果 Type(value) 是 String，返回 ToObject(value)。
        3. 如果 Type(value) 是 Boolean，返回 ToObject(value)。
        4. 如果 Type(value) 是 Number，返回 ToObject(value)。
    2. 断言：未提供参数 value 或其类型是 Null 或 Undefined。
    3. 令 obj 为一个新创建的原生 ECMAScript 对象。
    4. 设定 obj 的 [[Prototype]] 内部属性为标准内置的 Object 的 prototype 对象 (15.2.4)。
    5. 设定 obj 的 [[Class]] 内部属性为 "Object"。
    6. 设定 obj 的 [[Extensible]] 内部属性为 true。
    7. 设定 obj 的 8.12 指定的所有内部方法
    8. 返回 obj。

##### Object 构造器的属性

Object 构造器的 [[Prototype]] 内部属性值是标准内置 Function 的 prototype 对象。

除了内部属性和 length 属性（其值是 1）之外，Object 构造器拥有以下属性： 

###### Object.prototype

Object.prototype 的初始值是标准内置 Object 的 prototype 对象
这个属性包含特性 {[[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false } 

###### Object.getPrototypeOf ( O )

当以参数 O 调用 getPrototypeOf 函数，采用如下步骤： 

	1. 如果 Type(O) 不是 Object，则抛出一个 TypeError 异常。
	2. 返回 O 的 [[Prototype]] 内部属性的值。

###### Object.getOwnPropertyDescriptor ( O, P )

当调用 getOwnPropertyDescriptor 函数，采用如下步骤：

    1. 如果 Type(O) 不是 Object，则抛出一个 TypeError 异常。
    2. 令 name 为 ToString(P)。
    3. 令 desc 为以参数 name 调用 O 的 [[GetOwnProperty]] 内部方法的结果。
    4. 返回调用 FromPropertyDescriptor(desc) 的结果。

###### Object.getOwnPropertyNames ( O )

###### Object.create ( O [, Properties] )

create 函数按照指定的原型创建一个新对象。当调用 create 函数，采用如下步骤： 

    1. 如果 Type(O) 不是 Object 或 Null，则抛出一个 TypeError 异常。
    2. 令 obj 为仿佛是用表达式 new Object() 创建新对象的结果，这里的 Object 是标准内置构造器名。 
    3. 设定 obj 的 [[Prototype]] 内部属性为 O。
    4. 如果传入了 Properties 参数并且不是 undefined，则仿佛是用 obj 和 Properties 当作参数调用标准内置函数 Object.defineProperties 一样给 obj 添加自身属性。
    5. 返回 obj。

###### Object.defineProperty ( O, P, Attributes )

defineProperty 函数用于给一个对象添加一个自身属性以及更新现有自身属性的特性。

###### Object.defineProperties ( O, Properties )

defineProperties 函数用于给一个对象添加一些自身属性以及更新现有的一些自身属性的特性。

###### Object.seal ( O )

当调用 seal 函数，采用如下步骤： 

    1. 如果 Type(O) 不是 Object，则抛出一个 TypeError 异常。
    2. 对 O 的每个命名自身属性名 P,
        1. 令 desc 为以参数 P 调用 O 的 [[GetOwnProperty]] 内部方法的结果。
        2. 如果 desc.[[Configurable]] 是 true，设定 desc.[[Configurable]] 为 false。
        3. 以 P、desc、true 为参数调用 O 的 [[DefineOwnProperty]] 内部方法。
    3. 设定 O 的 [[Extensible]] 内部属性为 false。
    4. 返回 O。

###### Object.freeze ( O )

当调用 freeze 函数，采用如下步骤：

    1. 如果 Type(O) 不是 Object，则抛出一个 TypeError 异常。
    2. 对 O 的每个命名自身属性名 P,
        1. 令 desc 为以参数 P 调用 O 的 [[GetOwnProperty]] 内部方法的结果。
        2. 如果 IsDataDescriptor(desc) 是 true，则
            1. 如果 desc.[[Writable]] 是 true，设定 desc。[[Writable]] 为 false.
        3. 如果 desc.[[Configurable]] 是 true，设定 desc。[[Configurable]] 为 false。
        4. 以 P、desc、true 作为参数调用 O 的 [[DefineOwnProperty]] 内部方法。
    3. 设定 O 的 [[Extensible]] 内部属性为 false。
    4. 返回 O。

###### Object.preventExtensions ( O )

当调用 preventExtensions 函数，采用如下步骤：

    1. 如果 Type(O) 不是 Object，则抛出一个 TypeError 异常 .
    2. 设定 O 的 [[Extensible]] 内部属性为 false。
    3. 返回 O。

###### Object.isSealed ( O )

###### Object.isFrozen ( O )

###### Object.isExtensible ( O )

###### Object.keys ( O )

##### Object 的 prototype 对象的属性

Object 的 prototype 对象的 [[Prototype]] 内部属性的值是 null ，[[Class]] 内部属性的值是 "Object"，[[Extensible]] 内部属性的初始值是 true。 

###### Object.prototype.constructor

Object.prototype.constructor 的初始值是标准内置的 Object 构造器。 

###### Object.prototype.toString ( )
当调用 toString 方法，采用如下步骤：

    1. 如果 this 的值是 undefined，返回 "[object Undefined]"。
    2. 如果 this 的值是 null，返回 "[object Null]"。
    3. 令 O 为以 this 作为参数调用 ToObject 的结果。
    4. 令 class 为 O 的 [[Class]] 内部属性的值。
    5. 返回三个字符串 "[object "、class 和 "]" 连起来的字符串。

###### Object.prototype.toLocaleString ( )

*toLocaleString 方法返回一个 String 对象，这个对象中包含了用当前区域设置的默认格式表示的日期。

###### Object.prototype.valueOf ( )

###### Object.prototype.hasOwnProperty (V)

###### Object.prototype.isPrototypeOf (V)

*Object.prototype == V.constructor.prototype

###### Object.prototype.propertyIsEnumerable (V)

*属性V是否可枚举

#### Function ( . . . )

#### Array ( . . . )

#### String ( . . . )

#### Boolean ( . . . )

#### Number ( . . . )

#### Date ( . . . )

#### RegExp ( . . . )

#### Error ( . . . )

#### EvalError ( . . . )

#### RangeError ( . . . )

#### ReferenceError ( . . . )

#### SyntaxError ( . . . )

#### TypeError ( . . . )

#### URIError ( . . . )

### 全局对象的其他属性 

#### Math

#### JSON

