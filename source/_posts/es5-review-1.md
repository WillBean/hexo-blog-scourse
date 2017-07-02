---
title: ES5规范回顾系列(一)——术语定义
date: 2017-07-02 14:01:58
tags:
- ES5
- Specification
---

接触前端也有几个年头了，却发现自己从来没有好好的看看ECMAScript规范，闲来无事，就从ECMAScript2015看起吧\~摘录部分内容到自己的博客，以备忘\~

英文原版在[这](http://www.ecma-international.org/ecma-262/6.0/)

中文版在[这](https://www.w3.org/html/ig/zh/wiki/ES5)

下文摘自[ES5/概述](https://www.w3.org/html/ig/zh/wiki/ES5/overview)

### 原始值

在本规范的 [第8章](https://www.w3.org/html/ig/zh/wiki/ES5/types) 定义的 Undefined、Null、Boolean、Number、String 类型之一的成员。
*注： 原始值可以直接表示语言实现的最底层数据。

### 对象

对象类型的成员。
*注： 对象是属性的集合，并有一个原型对象。原型可以是空值。

### 构造器

创建和初始化对象的函数对象。
*注： 构造器的“prototype”属性值是一个原型对象，它用来实现继承和共享属性。

### 原型

为其他对象提供共享属性的对象。 
*注： 当构造器创建一个对象时，为了解决对象的属性引用，该对象会隐式引用构造器的“prototype”属性。通过程序表达式 constructor.prototype 可以引用到构造器的“prototype”属性。并且，添加到对象原型里的属性会通过继承的方式与所有共享此原型的对象共享。另外，可使用 Object.create 内置函数，通过明确指定原型来创建一个新对象。

### 原生对象

ECMAScript 实现中完全由本规范定义其语义而不掺入任何宿主环境定义的对象。
*注： 标准的原生对象由本规范定义。一些原生对象是内置的，其他的可在 ECMAScript 程序执行过程中构建。
 
### 内置对象
 
由 ECMAScript 实现提供，独立于宿主环境的对象，ECMAScript 程序开始执行时就存在。
*注： 标准的内置对象由本规范定义，ECMAScript 实现可以指定和定义其他的。所有内置对象都是原生对象。内置构造器是个内置对象，同时也是个构造器。
 
### 宿主对象

 由宿主环境提供的对象，用于完善 ECMAScript 执行环境。 
 *注： 任何不是原生对象的对象就是宿主对象。

----------

### String 值

原始值，它是零个或多个16位无符号整数组成的有限有序序列。
*注： String 值是 String 类型的成员。通常序列中的每个整数值代表 UTF-16 文本的单个16位单元。然而，对于其值，ECMAScript 只要求必须是16位无符号整数，除此之外没有任何限制或要求。

### String 类型

所有可能的 String 值的集合。

### String 对象

Object 类型的成员，它是标准内置构造器 String 的实例。
*注： 通过使用 new 表达式，以一个 String 值为参数调用 String 构造器来创建 String 对象。由此产生的对象包含一个值为此 String 值的内部属性。将 String 构造器作为一个函数来调用，可将一个 String 对象强制转换为一个 String 值（15.5.1）。

### Number 值

原始值，对应一个64位双精度二进制 IEEE754 值。
*注： Number 值是 Number 类型的成员，是一个数字的明确表示法

### Number 类型

所有可能的数字值的集合，包括特殊的“Not-a-Number”(NaN) 值、正无穷、负无穷。

### Number 对象

对象类型的成员，它是标准内置构造器 Number 的一个实例。
*注： 通过使用 new 表达式，以一个数字值为参数调用 Number 构造器来创建数字对象。由此产生的对象包含一个值为此数字值的内部属性。将 Number 构造器作为一个函数来调用，可将一个 Number 对象强制转换为一个数字值（15.7.1）。

### Infinity

正无穷 Number 值。Note.png

### NaN

值为 IEEE 754“Not-a-Number”的 Number 值。