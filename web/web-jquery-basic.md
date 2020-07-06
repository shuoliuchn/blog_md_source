## jQuery 基础和选择器

### jQuery 引入

```html
外部网址引入
	<script src="https://cdn.bootcss.com/jQuery/3.4.1/jQuery.js"></script>

本地文件引入
	<script src="jQuery.js"></script>  <!-- jQuery.js本地文件路径 -->
```

### jQuery 初识

```javascript
var d1 = $('#d1'); -- jQuery对象  -- jQuery.fn.init [div#d1]
var d = document.getElementById('d1');  -- 原生dom对象 -- <div id='d1'></div>
```

jQuery 对象和 dom 对象之前不能调用互相的方法。

jQuery 对象和 dom 对象可以互相转换。

```
j1[0] --> dom对象
$(d)  --> jQuery对象
```

### 选择器

jQuery 的一大优势就是，它的选择器的使用方法与 CSS 选择器极其类似。

#### id选择器

```javascript
$('#d1')  // 同css
```

#### 类选择器

```javascript
$('.c1') 
```

#### 元素选择器

```javascript
$('标签名称') -- $('span')  
```

#### 组合选择器

```css
$('#d1,.c2')
示例:
	html代码
		<div id="d1"></div>
        <div class="c2">
            这是c2
        </div>
    css代码:
        #d1,.c2{
            background-color: red;
            height: 100px;
            width: 100px;
            border-bottom: 1px solid black;
        }
  
  jQuery代码:
  	$('#d1,.c2').css('background-color','green');
  	$('#d1,.c2')[1];  -- 索引为1的dom对象
  	$('#d1,.c2').eq(1); -- 索引为1 的jQuery对象
```

#### 层级选择器（后代选择器）

```javascript
$("form input")
```

#### 属性选择器

```javascript
html代码:
    <div class="c1" xx="oo">
        这是啥!
    </div>
css代码:
    [xx]{
    	color:red;
    }

input标签:  type='xx'   [type='xx']--对应的input标签
$('[xx]').css('color','green');
$('[xx="oo"]').css('color','pink');
```

#### 表单对象属性选择器

- :checked  找到被选中的 input 标签
- :selected  找被选中的 select 标签中的 option 标签
- :disabled  不可操作的标签 
- :enabled   可操作的标签

示例:

```js
html代码:
    用户名:<input type="text" id="username" disabled>
    密码: <input type="text" id="password">
            
jquery代码:
    $(':enabled');  
    $(':disabled');
```

#### 表单选择器

```javascript
$(":text") // 找到所有input且type=text的标签
// $(":input") 找到所有input标签
// $(":password") 找到所有input且type=password的标签
// $(":radio") 找到所有input且type=radio的标签
// $(":checkbox") 找到所有input且type=checkbox的标签

html代码:
	<form action="">
        <input type="text" id="username">
        <input type="text" id="username2">
        <input type="password" id="pwd">

        <input type="submit">
    </form>
jQuery代码:找到所有的type=text的input标签
	$(':text');
```

#### 筛选器方法

```html
html代码
    <ul>
        <li>谢一峰</li>
        <li class="c1">王子宇</li>
        <li>孙波</li>
        <li class="c2">石淦</li>
        <li>
            <span>白雪冰</span>
        </li>
        <li>方伯仁</li>
    </ul>
```

##### parent()

```javascript
var c = $('.c1');
c.parent();
c.parents();  直系的祖先辈
c.parentsUntil('body');  往上找,直到找到body标签为止,不包含body标签
```

##### children()

```javascript
var u = $('ul');
u.children();  找到所有儿子标签
u.children('.c1'); 找到符合.c1选择器的儿子标签
```

##### next() 

```javascript
var c = $('.c1');
c.next();
nextAll();  下面所有兄弟
c.nextUntil('.c2');  下面到某个兄弟为止,不包含那个兄弟
```

##### prev()

```javascript
var c = $('.c1');
c.prev();
c.prevAll(); 上面所有兄弟,注意顺序都是反的
c.prevUntil('.c1'); 上面到某个兄弟为止,不包含那个兄弟,注意顺序都是反的
```

##### siblings()

```javascript
c.siblings();  找到不包含自己的所有兄弟
c.siblings('.c1');  筛选兄弟中有class类值为c1的标签
```

##### find() 找后代

```javascript
$('li').find('span'); 等同于css的 li span选择器
```

##### first()和last() 和eq(索引值)

```javascript
$('li').first();  找所有li标签中的第一个
$('li').last(); 找所有li标签中的最后一个
$('li').eq(0);  按照索引取对应的那个标签,索引从0开始
$('li').eq(-1);  最后一个
```
