# CSS 引入方式和 CSS 选择器

css 也称为层叠样式表（Cascading Style Sheet），用来控制 HTML 文档的样式。

## css 样式引入方式

### head 标签中引入

在日常，我们常用这个方法进行调试。这样可以避免经常切换文件。

```html
<style>
    /* 选择器{css 属性名称: 属性值; css 属性名称: 属性值;} */
    div{
        /* css注释 */
        width: 200px;
        height: 200px;
        background-color: red;
    }
</style>
```

### 外部文件引入

这是我们工作中常用的一种 css 样式引入方式。

将 css 样式和 html 内容分开写，有利于后期的维护和修改。

首先，我们需要创建一个 css 文件，也就是 stylesheet 文件，比如 `test.css` 文件

然后，我们在 css 文件中写入 css 样式：

```css
div{
    /* css注释 */
    width: 200px;
    height: 200px;
    background-color: red;
}
```

在想使用这些 css 样式的 html 文件的 head 标签中写上下面的内容：

```html
<link rel="stylesheet" href="test.css">
```

 href 对应的是文件路径。

### 内联样式

直接把样式写到标签中。这种写法不推荐，但有时能方便地解决一些问题。

```html
<div style="background-color: red; height: 100px; width: 100px;"></div>
```

## css 选择器

### 基本选择器

#### 元素选择器

```css
div {width: 100px;}
标签名称{css 属性: 值}
```

#### id 选择器

```css
html示例代码:
	<div id="d1">

    </div>
css写法:
    #d1{
        background-color: green;
        width: 100px;
        height: 100px;
    }
```

#### 类选择器

```css
html代码:
<div id="d1" class="c1">
    小豪
</div>

<div id="d2" class="c2">
    小李
</div>

<div id="d3" class="c1">
    老张
</div>

css写法
.c1{
    background-color: green;
    width: 100px;
    height: 100px;
}
```

#### 属性选择器

```css
HTML代码
<div id="d1" class="c1" xx="ss">
    小豪
</div>

<div id="d2" class="c2" xx="kk">
    小李
</div>

css写法:
[xx]{  属性查找
    background-color: green;
    width: 100px;
    height: 200px;
}

[xx='ss']{ 属性带属性值查找
    background-color: green;
    width: 100px;
    height: 200px;
}
```

### 后代选择器（空格链接）

其基本格式为：

```css
父级选择器 后代选择器{样式}
```

父级下所有后代的样式都会被设置，例如：

```css
html 代码示例:
	<div id="d1" class="c1" xx="ss">
        <span>
            <a href="http://www.xiaohao.com">小豪</a>
        </span>
    </div>

    <div id="d2" class="c2" xx="kk">
        <a href="http://www.xiaoli.com">小李</a>

    </div>

    <div id="d3" class="c1">
        老张
    </div>
    <a href="http://www.xxxxxxx.com">xxxxxxx</a>
css代码:
    div a{	
        color:orange; /* 字体颜色 */
    }
```

### 组合选择器（逗号连接）

```css
html 代码
    <div id="d1" class="c1" xx="ss">
        <span>
            <a href="http://www.xiaohao.com">小豪</a>
        </span>
        <span>
            <span>xxx222</span>
        </span>
    </div>

    <div id="d2" class="c2" xx="kk">
        <a href="http://www.xiaoli.com">小李</a>

    </div>

    <div id="d3" class="c1">
        <a href="">老张</a>
    </div>
    <a href="http://www.xxxxxxx.com">xxxxxxx</a>

    <span>官人，你好！</span>
    
css 代码: 注意：a 标签字体颜色设置，必须找到 a 标签才能设置
	#d1 a, #d3 a{
        background-color: pink;
        color:yellow;
    }
```

