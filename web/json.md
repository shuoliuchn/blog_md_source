## JSON

JSON 是  JavaScript Object Notation 的首字母缩写，单词的意思是 JavaScript 对象表示法。这里说的 JSON 指的是类似于 JavaScript 对象的一种数据格式。

JSON 的作用：在不同的系统平台，或不同编程语言之间传递数据。

### JSON 数据的语法

JSON 数据对象类似于 JavaScript 中的对象，但是它的键对应的值里面是没有函数方法的。值可以是普通变量，不支持undefined，值还可以是数组或者 JSON 对象。

例如，原生的 JavaScript 对象这样表示，类似于 Python 中的字典：

```javascript
var obj = {
  age:10,
  sex: '女',
  work:function(){
    return "好好学习",
  }
}
```

JSON 数据的对象格式，是没有方法的，只有属性：

```json
{
    "name":"tom",
    "age":18
}
```

JSON 数据也可以有数组格式：

```json
["tom",18,"programmer"]
```

复杂的 JSON 格式数据可以包含对象和数组的写法（除了例子中的自定义对象结构，数组结构也可以作为 JSON 传输数据）：

```json
{
  "name":"小明",
  "age":200,
  "is_delete": false,
  "fav":["code","eat","swim","read"],    
  "son":{
    "name":"小小明",
    "age":100,
    "lve":["code","eat"]
  }
}
```

JSON 数据可以保存在 `.json` 文件中。一般一个 `.json` 文件里面只有一个 JSON 对象。

关于 JSON 的总结：

1. JSON 文件的后缀是 `.json`

2. JSON 文件一般保存一个单一的 JSON 数据

3. JSON 数据的属性不能是方法或者 undefined，属性值只能：数值（整数、小数、布尔值）、字符串、JSON 和数组

4. JSON 数据只使用双引号、每一个属性成员之间使用逗号隔开，并且最后一个成员没有逗号。

   ```json
   {
       "name":"小明",
       "age":200,
       "fav":["code","eat","swim","read"],
       "son":{
           "name":"小小明",
           "age":100
       }
   }
   ```

工具：postman 可以用于测试开发的数据接口。



### js 中提供的 JSON 数据转换方法

JavaScript 提供了一个 JSON 对象来操作 JSON 数据的数据转换.

|   方法    |   参数    |  返回值   | 描述                                 |
| :-------: | :-------: | :-------: | ------------------------------------ |
| stringify | JSON 对象 |  字符串   | JSON 对象转成字符串                  |
|   parse   |  字符串   | JSON 对象 | 字符串格式的 JSON 数据转成 JSON 对象 |

具体实例如下：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<script>
    // JSON语法
    let humen = {
        "username":"sure",
        "password":"1234567",
        "age":20    // 自定义对象这里可以有收尾的逗号，但JSON数据是不可以有这个逗号的
    };

    console.log(humen);    // {username: "sure", password: "1234567", age: 20}
    console.log(typeof humen);    // object

    // JSON对象提供对JSON格式数据的转换功能
    // stringify(JSON对象)  # 用于把JSON转换成字符串
    let result = JSON.stringify(humen);
    console.log(result);  // {"username":"sure","password":"1234567","age":20}
    console.log(typeof result);   // string

    // parse(字符串类型的JSON数据)  # 用于把字符串转成JSON对象
    let json_str = '{"password":"1123","age":20,"name":"xiaobai"}';
    console.log(json_str)    // {"password":"1123","age":20,"name":"xiaobai"}
    console.log(typeof json_str)   // string

    let json_obj = JSON.parse(json_str);
    console.log(json_obj);  // {password: "1123", age: 20, name: "xiaobai"}
    console.log(typeof json_obj)   // object
    console.log(json_obj.age)    // 20
</script>
</body>
</html>
```