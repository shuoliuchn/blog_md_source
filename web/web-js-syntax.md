## JavaScript 基本语法

### if 判断

```javascript
if (a == 1){    // 判断条件写在小括号里面，大括号里面写条件判断成功后的代码内容
	console.log('1111');
}
else{
   console.log('222');
}
```

### 多条件判断

```javascript
    var a = 0;
    if(a > 1){
        // console.log('1111');
        // var hhhh = document.getElementById('d1');
        // hhhh.innerText = '彭于晏';
    }else if(a<1){

        console.log('2222');
    }else {
        console.log('3333');
    }
```

### 运算符

#### 比较运算

```javascript
// > < == !=  >=  <=   ===  !==

var a = 2;
var b = '2';
a == b;    // true  弱等于
a === b;    // false  强等于
a != b;    // false
a !== b;    // true
```

#### 算术运算

```javascript
// +  -  * / %   ++  --  
++ 自增 1  
-- 自减 1

var a = 2;
a++  先执行逻辑，再 + 1
++a  先 + 1，再执行逻辑

简单示例：
	if (++a === 4){
        console.log('xxx');
    }
    else{
        console.log('ooo');
    };
```

#### 逻辑运算

```javascript
与 &&
或 ||
非 !
```

### switch 判断

如果 case 后面不加 break，一旦条件满足，后面的代码全都会被执行，即便后面条件不成立。

```javascript
var num = 200;
switch(num++){
    case 10:
        console.log('未成年');
        break;
    case 18:
        console.log('成年');
        break;
    case 35:
        console.log('油腻老男人');
        break;
    case 100:
        console.log('....');
        break;
    default:
        console.log('太大了');
};
```

### 异常捕获

将会捕获所有异常。JavaScript 是一门很难报错的编程语言。

```javascript
try{
	console.log(xx);
}catch(e){
	console.log(e);
}
finally{
	console.log('sssss');
}
```

### 循环

#### for 循环

```javascript
for (var i=0; i<100; ++i){
    console.log(i);  
};

// 循环数组
var d = [11, 22, 33];
for (var i in d){
    if (d[i] === 22) {
        continue;
        // break;
    }
    console.log(i, d[i]);    // 使用in，获取的i是索引
}
for (var i of d) {
    console.log(i)    // 使用of，获取的i是元素
}
for (var i=0; i<d.length; i++){
    console.log(i,d[i]);
};
// 循环自定义对象 -- python 字典
var o = {a: 2, b: 3}
for (var i in o) {
    console.log(i, o[i]);  // 不要用 d.i 来取值
}
```

#### while 循环

```javascript
var a = 0;
while(a < 5){
	a++;
	if (a === 2){
      continue;
	}
	console.log(a);
}
```

js 的基础数据类型都有布尔值属性：`[]`、`0`、`{}`、`''`、`undefined`、`null`、`NaN` 都代表 `false`。

```javascript
//字符串转数字:
var a = '11';
parseInt(a);

var a = '23abc';
parseInt(a);   23

var a = 'asdfabc';
parseInt(a);    // NAN  -- not a number
typeof NaN;    // "number"
NaN === NaN;    // false
NaN == NaN;    // false
```

### 函数

#### 普通函数

```javascript
function f1(a, b){
	return a + b;
}
// 执行
f1(1, 2)    // 3
function f1(a, b){
	return a, b;
};

f1(1, 2);
// 不能返回多个值，只会返回最后一个值：2
```

#### 匿名函数

```javascript
var a = function (a,b){
	console.log('xxx');
}

var d = {'xx':'oo','f':function (a,b){
	console.log('xxx');
}};
// 执行：d.f(1, 2);
```

#### 自执行函数

```javascript
(function () {
    alert('自执行函数!')
})()
```

### 序列化

```javascript
var d = {'a':'aa','b':18};
// 序列化：
	var d_json = JSON.stringify(d);    // python：json.dumps(d);
// 反序列化：
	d_json = "{"a": "aa", "b": 18}"
	var reverse_json = JSON.parse(d_json);
```

### 格式化

```javascript
var website = 'https://www.baidu.com'
var web_name = '百度'
var tag = `<a href='${website}'>${web_name}</a>`    // 待格式化的字符串用反引号括起来
```