# JavaScript 的使用和基本数据类型



## JavaScript 简单认识

JavaScript 简称 js，目前由 ECMA 机构维护，上一个经典版本未 ECMAscript5，也就是 ES 5，是这里主要讨论的。

最新版本的 JavaScript ECMAscript 6，很多流行的 web 框架，如 vue.js、react 等都是基于最新版的 ES 6 开发的。

JavaScript 由三个部分组成

1. ECMAscript 5 的核心，js 语言
2. BOM，浏览器对象模型，js 操作浏览器，做出对应的一些效果
3. DOM，文档对象模型，也就是操作 HTML 文件

## js 代码引入方式

与 css 类似，有三种引入 js 的方式：

1. head 标签的 script 标签里面 `alert('xx'), confirm('xx')`

2. body 标签的 script 标签里面

3. 外部文件引入的方式来使用

   1. 创建一个 `.js` 结尾的文件，写上咱们的 js 代码：

      ```js
      alert('are you ok?');
      ```

   2. 在想使用这个 js 代码的 html 文件中，head 标签或者 body 标签下面或者上面写下面的内容：

      ```html
      <script src="01test.js"></script>
      ```

## js 语言基础

在浏览器中按 `F12`，可以打开调试台。也可以通过右键 --> 检查，来打开调试台。

![1573029016467](https://gitee.com/oldboy-python-full-stack-26/19083026021/raw/master/week10/day40/day40%20%E4%BD%9C%E4%B8%9A%E5%92%8Cjs.assets/1573029016467.png)

点击调试台中的 `console`，就可以测试我们的 JavaScript 代码了。

![1573029751069](https://gitee.com/oldboy-python-full-stack-26/19083026021/raw/master/week10/day40/day40%20%E4%BD%9C%E4%B8%9A%E5%92%8Cjs.assets/1573029751069.png)

### 变量

```javascript
var a = 10;  变量定义 var 变量名;
```

js 中，以 `;` 表示一个语句的终止，不要求缩进。

但是在写代码时，还是要合理使用缩进，增加代码的可读性。

### 数据类型

#### number 类型(整数，浮点数)

```javascript
var n = 11;
var n2 = 11.11;
js代码注释  // 注释语句

查看数据类型 typeof 变量名;
	typeof n; -- number类型
	
变量声明,但没有赋值的时候,变量的值为undefined
```

#### string 类型（字符串）

```javascript
示例:
	var a = 'alexsb';
	var a = new String('ss');  typeof a; -- "string"
字符串的操作方式
	var s = '诱色可餐徐茂洁';
索引取值:  s[1] -- '色'
移除两端空格: s.trim();
	s.trimLeft();    --    s.strimStart();    // 去除左侧/开头空格
	s.trimRight();    --    s.strimEnd();    // 去除右侧/结尾空格

var value = name.charAt(index) 			// 根据索引获取字符
	示例: var s = 'hello'; -- s.charAt(4); -- 'o'
var value = name.substring(start,end) 	// 根据索引获取子序列,切片
	示例: s.substring(1,3); -- "el"
```

#### 布尔类型（boolean 类型）

```javascript
var a = true;
var b = false;
```

#### undefined 和 null 类型

- undefined：变量声明了，但是没有赋值，此时这个变量是 undefined 类型
- null：变量不用了，就可以给变量赋值为null，它是 object 类型

#### 数组（array）

数组的**增、删、改、拼接和位置转换**等操作，都是**在原数组上进行**的，

数组的**拼接**操作不会改变元素组，而是会**生成新的字符串或数组**。

数组的定义：

```javascript
var name = [11,22,33];
```

数组常用方法：

```javascript
names[0]						// 索引,索引也是从0开始的

// 数组的增加和删除
names.push(ele)     			// 尾部追加元素
	示例:a.push('xx'); --  [11, 22, 33, "xx"]
var ele = names.obj.pop()     	// 尾部移除一个元素
	示例:a.pop(); -- [11, 22, 33]
names.unshift(ele)  			// 头部插入元素
	示例:a.unshift('ssss'); --  ["ssss", 11, 22, 33]
var ele = obj.shift()         	// 头部移除一个元素
	示例:a.shift(); --  [11, 22, 33]

// 数组的修改
// splice(start, ?deleteCount, ...items)
names.splice(index,0,ele) 		// 在指定索引位置插入元素
names.splice(从哪删(索引),删几个(个数),删除位置替换的新元素(可不写,可写多个)) 
names.splice(index,1,ele) 		// 指定索引位置替换元素
names.splice(index,1)     		// 指定位置删除元素
	示例: a.splice(1,2) --  [11, 22, 33]
		a.splice(1,1,'xx','oo','asdf'); -- [11, "xx", "oo", "asdf", 33]

// 数组内容的查找
names.slice(start,end)        	// 切片
	示例:a.slice(1,3);--  [22, 33]
	
// 数组的调序、拼接操作
names.reverse()      			// 原数组反转
	示例:a.reverse(); -- [44, 33, 22, 11]
names.join(sep)       			// 将数组元素连接起来以构建一个字符串
	示例: var a = ['ni','hao','ma',18]
		a.join('+'); -- "ni+hao+ma+18"
names.concat(val,..)  			// 连接数组
	示例: var a = [11,22]; var b = ['aa','bb']
	var c = a.concat(b); c -- [11, 22, "aa", "bb"]
names.sort()         			// 对原数组进行排序
	很尬!
	需要自己定义规则:
		function compare(a,b){
           return a - b;  当大于0时,两个数据换位置
        };
        使用: a.sort(compare)，升序排列
```

如果不指定数组排序的规则函数，默认对数组按照字符串的顺序排序。如果想要实现降序，可以将返回值替换成 `b - a`。

![1573031908562](https://gitee.com/oldboy-python-full-stack-26/19083026021/raw/master/week10/day40/day40%20%E4%BD%9C%E4%B8%9A%E5%92%8Cjs.assets/1573031908562.png)

上面例子中的数组和规则函数为：

```javascript
a = [32, 23, 4, 2, 89, 98, 100];

// 升序规则函数
function compare(a,b){
	return a - b};

// 降序规则函数
function compare_reverse(a,b){
	return b - a};
```

#### 自定义对象（dict）

```javascript
// 声明
info = {
    name:'武沛齐',
    "age":18
}

var a = {username:'xx',password:'123'}; //可以不加引号
typeof info;
"object"

// 常用方法
var val = info['name']		// 获取,通过键取值必须加引号,
info.name 也是可以的
info['age'] = 20			// 修改
info['gender'] = 'male'		// 新增
delet'age']			// 删除
```