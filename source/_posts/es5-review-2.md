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

##### 数组原型对象的属性

###### Array.prototype.constructor

Array.prototype.constructor 的初始值是标准内置 Array 构造器。

###### Array.prototype.toString ( )

###### Array.prototype.toLocaleString ( )

先用数组元素的 toLocaleString 方法，将他们转换成字符串。然后将这些字符串串联，用一个分隔符分割，这里的分隔符字符串是与特定语言环境相关，由实现定义的方式得到的。调用这个函数的结果除了与特定语言环境关联之外，与 toString 的结果类似。

###### Array.prototype.concat ( [ item1 [ , item2 [ , … ] ] ] )

当以零个或更多个参数 item1、item2，等，调用 concat 方法时，会返回一个数组。 返回的数组包含 调用对象的数组元素和随后顺序每个参数的数组元素。

###### Array.prototype.join (separator)

数组元素先被转换为字符串，再将这些字符串用 separator 分割连接在一起。如果没提供分隔符，将一个逗号用作分隔符。

###### Array.prototype.pop ( )

删除并返回数组的最后一个元素。

###### Array.prototype.push ( [ item1 [ , item2 [ , … ] ] ] )

将参数以他们出现的顺序追加到数组末尾。数组的新 length 属性值会作为调用的结果返回。

###### Array.prototype.reverse ( )

重新排列数组元素，以翻转它们的顺序。对象会被当做调用的结果返回。

###### Array.prototype.shift ( )

删除并返回数组的第一个元素。

###### Array.prototype.slice (start, end)

slice 方法需要 start 和 end 两个参数，返回一个数组，这个数组包含从下标为 start 的元素到下标为 end（不含 end）的元素（或如果 end 是 undefined 就到数组末尾）。如果 start 为负，它会被当做是 length + start，这里的 length 是数组长度。如果 end 为负，它会被当做是 length + end，这里的 length 是数组长度。

###### Array.prototype.sort (comparefn)

给 this 数组的元素排序。排序不一定是稳定的（相等的元素们不一定按照他们原来的顺序排列）。如果 comparefn 不是 undefined，它就必须是个函数，这个函数接受两个参数 x 和 y，如果 x < y 返回一个负值，如果 x = y 返回零，如果 x > y 返回一个正值。

###### Array.prototype.splice (start, deleteCount [ , item1 [ , item2 [ , … ] ] ] )

当以两个或更多参数 start、deleteCount 和（可选的）item1、item2, 等，调用 splice 方法，从数组索引 start 开始的 deleteCount 个数组元素会被替换为参数 item1、item2, 等。返回一个包含参数元素（如果有）的数组。

###### Array.prototype.unshift ( [ item1 [ , item2 [ , … ] ] ] )

将参数们插入到数组的开始位置，它们在数组中的顺序与它们出现在参数列表中的顺序相同。

###### Array.prototype.indexOf ( searchElement [ , fromIndex ] )

indexOf 按照索引的升序比较 searchElement 和数组里的元素们，它使用内部的严格相等比较算法（11.9.6），如果找到一个或更多这样的位置，返回这些位置中第一个索引；否则返回 -1。

###### Array.prototype.lastIndexOf ( searchElement [ , fromIndex ] )

###### Array.prototype.every ( callbackfn [ , thisArg ] )

###### Array.prototype.some ( callbackfn [ , thisArg ] )

###### Array.prototype.forEach ( callbackfn [ , thisArg ] )

callbackfn 应该是个函数，它接受三个参数。forEach 按照索引的升序，对数组里存在的每个元素调用一次 callbackfn。callbackfn 只被实际存在的数组元素调用；它不会被缺少的数组元素调用。

如果提供了一个 thisArg 参数，它会被当作 this 值传给每个 callbackfn 调用。如果没提供它，用 undefined 替代。

调用 callbackfn 时将传入三个参数：元素的值，元素的索引，和遍历的对象。

对 forEach 的调用不直接更改对象，但是对 callbackfn 的调用可能更改对象。

forEach 处理的元素范围是在首次调用 callbackfn 之前设定的。在 forEach 调用开始后追加到数组里的元素们不会被 callbackfn 访问。如果更改以存在数组元素，forEach 访问这些元素时的值会传给 callbackfn；在 forEach 调用开始后删除的和之前被访问过的元素们是不访问的。

###### Array.prototype.map ( callbackfn [ , thisArg ] )

callbackfn 应该是个函数，它接受三个参数。map 按照索引的升序，对数组里存在的每个元素调用一次 callbackfn，并用结果构造一个新数组。callbackfn 只被实际存在的数组元素调用；它不会被缺少的数组元素调用。 V8.png

如果提供了一个 thisArg 参数，它会被当作 this 值传给每个 callbackfn 调用。如果没提供它，用 undefined 替代。

调用 callbackfn 时将传入三个参数：元素的值，元素的索引，和遍历的对象。

对 map 的调用不直接更改对象，但是对 callbackfn 的调用可能更改对象。

map 处理的元素范围是在首次调用 callbackfn 之前设定的。在 map 调用开始后追加到数组里的元素们不会被 callbackfn 访问。如果更改以存在数组元素，map 访问这些元素时的值会传给 callbackfn；在 map 调用开始后删除的和之前被访问过的元素们是不访问的。

###### Array.prototype.filter ( callbackfn [ , thisArg ] )

callbackfn 应该是个函数，它接受三个参数并返回一个可转换为布尔值 true 和 false 的值。filter 按照索引的升序，对数组里存在的每个元素调用一次 callbackfn，并用使 callbackfn 返回 true 的所有值构造一个新数组。callbackfn 只被实际存在的数组元素调用；它不会被缺少的数组元素调用。

如果提供了一个 thisArg 参数，它会被当作 this 值传给每个 callbackfn 调用。如果没提供它，用 undefined 替代。

调用 callbackfn 时将传入三个参数：元素的值，元素的索引，和遍历的对象。

对 filter 的调用不直接更改对象，但是对 callbackfn 的调用可能更改对象。

filter 处理的元素范围是在首次调用 callbackfn 之前设定的。在 filter 调用开始后追加到数组里的元素们不会被 callbackfn 访问。如果更改以存在数组元素，filter 访问这些元素时的值会传给 callbackfn；在 filter 调用开始后删除的和之前被访问过的元素们是不访问的。

###### Array.prototype.reduce ( callbackfn [ , initialValue ] )

callbackfn 应该是个函数，它需要四个参数。reduce 按照索引的升序，对数组里存在的每个元素，将 callbackfn 作为回调函数调用一次。

调用 callbackfn 时将传入四个参数：previousValue（initialValue 的值或上次调用 callbackfn 的返回值）、currentValue（当前元素值）、currentIndex 和遍历的对象。第一次调用回调函数时，previousValue 和 currentValue 的取值可以是下面两种情况之一。如果为 reduce 调用提供了一个 initialValue，则 previousValue 将等于 initialValue 并且 currentValue 将等于数组的首个元素值。如果没提供 initialValue，则 previousValue 将等于数组的首个元素值并且 currentValue 将等于数组的第二个元素值。如果数组里没有元素并且没有提供 initialValue，则抛出一个 TypeError 异常。

对 reduce 的调用不直接更改对象，但是对 callbackfn 的调用可能更改对象。

reduce 处理的元素范围是在首次调用 callbackfn 之前设定的。在 reduce 调用开始后追加到数组里的元素们不会被 callbackfn 访问。如果更改以存在数组元素，reduce 访问这些元素时的值会传给 callbackfn；在 reduce 调用开始后删除的和之前被访问过的元素们是不访问的。

###### Array.prototype.reduceRight ( callbackfn [ , initialValue ] )

#### String ( . . . )

##### String 构造器的属性

###### String.prototype

String.prototype 的初始值是标准的内置 String 原型对象。

###### String.fromCharCode ( [ char0 [ , char1 [ , … ] ] ] )

返回一个字符串值，包含的字符数与参数数目相同。每个参数指定返回字符串中的一个字符，也就是说第一个参数第一个字符，以此类推（从左到右）。一个参数转换为一个字符，通过先应用 ToUint16 操作，再将返回的16位整数看作字符的代码单元值。如果没提供参数，返回空字符串。

##### 字符串原型对象的属性

###### String.prototype.charCodeAt (pos)

将 this 对象转换为一个字符串，返回一个代表这个字符串 pos 位置字符的代码单元值的数字（小于 216 的非负整数）。如果那个位置没有字符，返回 NaN。

###### String.prototype.localeCompare (that)

当以一个参数 that 来调用 localeCompare 方法，它返回一个非 NaN 数字值，这个数字值反应了对 this 值（转换为字符串）和 that 值（转换为字符串）进行语言环境敏感的字符串比较的结果。两个字符串 S 和 That 用实现定义的一种方式进行比较。比较结果是按照系统默认语言环境指定的顺序来排列字符串。根据这三种情况：S 在 That 前面、两字符串相同、S 在 That 后面，分别返回：负数、零、正数。

###### String.prototype.search (regexp)

###### String.prototype.trim ( )

#### Boolean ( . . . )

#### Number ( . . . )

##### Number 构造器的属性

###### Number.MAX_VALUE

Number.MAX_VALUE 的值是 Number 类型的最大正有限值，约为 1.7976931348623157×10308。

###### Number.MIN_VALUE

Number.MIN_VALUE 的值是 Number 类型的最小正有限值，约为 5×10-324。

###### Number.NEGATIVE_INFINITY

###### Number.POSITIVE_INFINITY

##### Number 原型对象的属性

###### Number.prototype.toFixed (fractionDigits)

返回一个包含了 代表 this Number 值的留有小数点后 fractionDigits 个数字的十进制固定小数点记法 的字符串。如果 fractionDigits 是 undefined，就认为是 0。

###### Number.prototype.toExponential (fractionDigits)

返回一个代表 this Number 值 的科学计数法的字符串，它的有效数字的小数点前有一个数字，有效数字的小数点后有 fractionDigits 个数字。如果 fractionDigits 是 undefined，包括指定唯一 Number 值需要的尽可能多的有效数字（就像 ToString，但在这里总是以科学计数法输出）。

###### Number.prototype.toPrecision (precision)

返回一个字符串，它代表 this Number 值 的科学计数法（有效数字的小数点前有一个数字，有效数字的小数点后有 precision - 1 个数字）或十进制固定计数法（precision 个有效数字）。如果 precision 是 undefined，用 ToString 调用代替。

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

