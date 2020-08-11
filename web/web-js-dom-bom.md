## JavaScript 操作 BOM 和 DOM 对象

### BOM 对象

BOM 对象，即浏览器对象模型。js 操作 BOM 就是通过 js 代码控制浏览器，让浏览器进行一些操作。

#### 弹框

```javascript
alert('xx');
confirm('are you sure?')
```

#### location 对象

```javascript
location.href;    // 获取当前页面的地址
location.href = 'http://www.baidu.com';    // 跳转到这个网址上
location.reload();    // 刷新当前页面
location.search    // 获取当前 url 中的查询字符串，返回结果如：?a=2&b=3
location.search = "a=2&b=3"    // 将当前页面的查询字符串设置为这个
```

#### 计时器

```javascript
第一种：一段时间之后执行某个任务
	设置: var t = setTimeout(function(){confirm('你满18岁了吗?')},5000);
		 var t = setTimeout("console.log('xxx')",1000);
		t 就是浏览器用来记录你的计时器的标识数字
	清除: clearTimeout(t)
第二种：每隔一段时间执行某个任务
	设置: var t = setInterval(function(){confirm('弹个框!!')},3000);
	清除: clearInterval(t);
```

### DOM 对象

用来直接操作 HTML 文档（`.html` 文件）

#### 直接查找选择器

```html
html 代码
	<div class="c1" id="d1"></div>
	<div class="c1 c2" id="d2"></div>
css 代码
	   .c1{
            background-color: green;
            height: 100px;
            width: 100px;
        }
        .c2{
            background-color: red;
            /*height: 100px;*/
            /*width: 100px;*/
            color:red;
        }

按标签名查找: var divEle = document.getElementsByTagName('div');
按 id 值查找:  var d1 = document.getElementById('d1');
			示例: d1.style.height = '600px';
按类值查找:var a = document.getElementsByClassName('c1');
```

#### 间接查找选择器

```javascript
var div1 = document.getElementsByClassName('c1')[0]; 
div1.nextElementSibling.style.color = 'red';    // 找下一个兄弟标签,并改了色
div1.previousElementSibling;    // 找上一个兄弟
div1.firstElementChild;    // 找第一个儿子
div1.lastElementChild;    // 找最后一个儿子
div1.children;    // 找所有儿子,是一个数组
div1.parentElement;    // 找到自己的父级标签
```

#### 文本操作

```javascript
innerText
	获取文本：
		var a = document.getElementById('jd')
		a.innerText;    // 只获取文本内容
    设置文本：
    	a.innerText = '<a href="">校花</a>';    // 不能识别标签,单纯的文本内容显示
innerHTML
	获取文本：	
		var d = document.getElementsByClassName('c1')[0];
		d.innerHTML;    // 获取的内容包含标签
	设置文本：
		d2.innerHTML = '<a href="">校花</a>';    // 能够识别标签,生成标签效果
```

#### value 值操作

```javascript
input 标签
	html：
		<input type="text" name="username" id="username" >
	示例：
		var inp = document.getElementById('username');    // 找到标签
		inp.value;    // 获取值
		inp.value = '200块!';    // 修改值
```

#### class 类值操作

```javascript
var div1 = document.getElementById('d1');
div1.classList;    // 获取标签类值
div1.classList.add('c2');    // 添加类值
div1.classList.remove('c3');    // 删除类值
div1.classList.toggle('c3');    // 有就删除，没有就添加
var t = setInterval("div1.classList.toggle('c3')",1000);    // 定时器添加
```