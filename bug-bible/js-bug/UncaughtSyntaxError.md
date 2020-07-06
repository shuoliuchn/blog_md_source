# Uncaught SyntaxError

## Function statements require a function name

### 错误1.1

报错信息：

```js
Uncaught SyntaxError: Function statements require a function name
```

错误代码：

```js
function(a,b){return a+b}
```

错误原因：

js 中的匿名函数如果没有指定变量是没有意义的，因为后续代码没有办法将其调用。所以要么指定一个函数名，要么给匿名函数分配一个变量名。总之，不能让其孤苦无依。

解决方法：

指定函数名，或给定参数

```js
function add(a,b){return a+b};
	// 或者
var add = function(a, b){return a+b};
```

## Identifier 'add' has already been declared at `<anonymous>:1:1`

### 错误2.1

报错信息：

```js
VM885:1 Uncaught SyntaxError: Identifier 'add' has already been declared at <anonymous>:1:1
```

错误代码：

```js
var add = function(a, b){return a+b};
let add = (a, b) => a+b;
```

错误原因：

在 JavaScript ES6 版本中，新的 let 声明变量的语法较为严格，不能重复声明相同变量。add 之前已经被声明，再次声明会报错。

解决方法：

修改变量名

```js
let add1 = (a, b) => a+b;
```

补充：

- let 特点：1. 局部作用域；2. 不存在变量提升；3. 不可重复声明变量（var 可以）- 

- const 特点：1. 局部作用域；2. 不存在变量提升；3. 不可重复声明；4. 不可修改

## Unexpected token '=>'

### 错误3.1

报错信息：

```js
Uncaught SyntaxError: Unexpected token '=>'
```

错误代码：

```js
let cc = a, b => a + b;
```

错误原因：

是用箭头函数时，若有多个参数，声明参数处的括号不可省略。

解决方法：

加上括号

```js
let cc = (a, b) => a + b;
```

