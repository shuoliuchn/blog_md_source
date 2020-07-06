## JavaScript ES 6 简单常用语法

[TOC]

Vue 是基于最新版本的 JavaScript ECMAScript 6 开发的，很多语法遵循的都是 ES 6 中 新出现的语法。因此，在学习 Vue 之前，有必要先补充一点 ES 6 的简单语法。

这里的语法不是很全面，但都是 Vue 中常会用到的。

### ES 6 的变量声明语法

在 ES 5 版本的 JS 中，变量是通过关键字 var 声明的：

```js
var a = 'xx';
```

但是这种声明变量的方式极混乱。变量 a 全局都可以调用，且存在变量提升的情况，而且可以重复声明。

为了避免这些混乱的问题，ES 6 版本的 JS 引入了两个新的声明变量的方式，let 和 const：

```js
let a = 'ss';
const b = 'oo';
```

使用 let 声明变量的特点为：

1. a是局部作用域的
2. 不存在变量提升
3. 不能重复声明（var 可以重复声明）

使用 const 声明的特点为：

1. 局部作用域
2. 不存在变量提升
3. 不能重复声明
4. 一般声明不可变的量

### 模板字符串

类似于 Python 中的 [f-strings](..\python-basic\f-strings.md) ，JS 也有类似的字符串格式化方式，称为模板字符串。其用法为：使用 tab 键上面的反引号包裹，通过 `${变量名}` 的形式来插入值，例如：

```js
let bb = 'jj';
var ss = `你好${bb}`;
```

### ES 5 和 ES 6 的函数对比

ES 5 中，普通函数的写法为：

```js
function func(x){
    return x
}
func(5);
```

匿名函数的写法为：

```js
var func = function(x){
    return x
}
```

ES 6 中的匿名函数也可以这样写，不过建议使用 let 声明变量：

```js
let func = function(x){
    return x
}
add(5);
```

ES 6 中的匿名函数可以简写成箭头函数的形式：

```js
let func = (x) => {
    return x
}
```

箭头函数与普通的匿名函数还是有些区别，主要是 this 的指向问题，在稍后的 ES 6 的类中会有所提及。

上面的箭头函数可以进一步省略括号简写成：

```js
let func = x => x;
console.log(func(5));
```

写法很简单，但是牺牲了可读性。

而且如果有多个参数的时候必须加括号，函数返回值还是只能有一个，没有参数的，必须写一个`()`：

```js
let add = (x, y) => x + y;
let foo = () => '略略略';
```

### 自定义对象中属性的简写

若自定义对象中的值与键的变量名相同，可以把冒号和值省略，只写键，例如：

```js
let hello = 'Hello World!'
// 正常写法为：
    let d = {hello: hello}
// 可简写为：
    led d = {hello}
```

### 自定义对象中封装函数的写法

ES 5 自定义对象中封装函数的写法：

```js
var person1 = {
    name: "Sure",
    age: 18,
    func: function(){    // 自定义的对象中放函数的方法：匿名函数
        console.log(this);    // this指向的是当前的对象，{name: "Sure", age: 18, func: ƒ}
        console.log(this.name);    // Sure
    }
}
person1.func()    // 通过自定义对象来使用函数
```

ES 6 中自定义对象中来封装箭头函数的写法，这里面会涉及到箭头函数中的 this 指向问题：

```js
let person2 = {
    name: "Sure",
    age: 18,
    func: () => {    // 箭头函数
        console.log(this);    // 箭头函数的this指向的不是当前对象，而是person2的父级对象（称为上下文）。而此时的父级对象是Window对象，故而这里打印的是：Window {parent: Window, opener: null, top: Window, length: 2, frames: Window, …}
        console.log(window);   // Window对象，全局浏览器对象，打印结果同前面的完全一样：Window {parent: Window, opener: null, top: Window, length: 2, frames: Window, …}
        console.log(this.name);    // 找不到，没有，所以是：undefined
    }
}
person2.func()
```

这种让 this 指向上下文的情况虽然不多见，但是在 Vue 的学习中还是会遇到的，在实际应用时要灵活使用。

当然，更多的时候，我们还是希望 this 指向 person 对象，而不是 Window 对象，所以 ES 6 还有下面这种对象的**单体模式**写法，简化了自定义对象中封装函数的语法：

```js
let person3 = {
    name: "Sure",
    age: 18,
    func(){    // 相当于f1:function(){},只是一种简写方式,称为对象的单体模式写法，写起来也简单，vue里面会看用到这种方法
        console.log(this);    // this指向的是当前的对象，{name: "Sure", age: 18, func: ƒ}
        console.log(this.name);    // Sure
    }
}
person3.func()
```

### ES 5 和 ES 6 的类写法对比（了解）

ES 5 写类的方式：

```js
function Person(name, age){    // 函数名大写作为类
    // 属性直接封装
    this.name = name;
    this.age = age;
}
// 通过原型链封装方法
Person.prototype.f1 = function(){
    console.log(this.name);    // this指代实例化的Person对象，结果：Sure
}
// 这里也可以封装箭头函数方法
Person.prototype.f2 = () => {
    console.log(this);    // this指代上下文，即Window对象：Window {parent: Window, opener: null, top: Window, length: 2, frames: Window, …}
}
var p1 = new Person('Sure', 18);    // 实例化Person对象
p1.f1();    // 调用实例方法
p1.f2();
```

ES 5 的类是可以继承的，继承之后，可以通过子类实例化的对象调用父类的方法。仅作了解，日后可深入研究。

ES 6 的类则这样写：

```js
class Person2{
    constructor(name, age){    // 对象里的单体模式，类似上面自定义对象封装函数的单体模式。这个方法是类似于Python的__init__()构造方法，写参数的时候也可以写关键字参数 constructor(name='Sure',age=18)
        this.name = name;
        this.age = age;
    }  //注意这是类不是自定义对象，这里不能写逗号
    showname(){   //封装方法
        console.log(this.name);
    }  //不能写逗号
    showage(){
        console.log(this.age);
    }
}
let p2 = new Person2('Sure', 18);    // 实例化Person2对象
p2.showname()    // 调用方法：Sure
```

ES 6 的类当然也是可以继承的，这里咱们就不讨论了。将来需要的时候，就去学一下吧。大约是用的 extends 和 super。

