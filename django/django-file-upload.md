## Django 通过 form 表单和 ajax 上传文件

在默认情况下，form 表单是无法传输文件的。即便我们在 HTML 中写了 type 类型为 file 的 input 标签。这是因为 HTML 默认的表单传输方法为 `application/x-www-form-urlencoded`。但是这种方法是不能传输文件的。用这种方法传输文件时，我们只能在后端看到文件名，而得不到整个文件。

Views 视图函数尝试打印获取到的文件和 POST 数据时，结果如下：

```python
<MultiValueDict: {}>
<QueryDict: {'csrfmiddlewaretoken': ['jEKPQOvvCeD4q96ET9zVU5xBTdlgmbgPQb7c5EhvNsrYdT8L4KBw8IuBTmlFOUwj'], 'avata': ['1571311850334.png']}>
```

这时，我们就要将 HTML 代码中的 form 表单标签加上 `enctype="multipart/form-data"` 的属性才能上传文件，其本质时修改 content-type 请求头中的携带数据的消息格式：

```html
<form action="" method="post" enctype="multipart/form-data">
    {% csrf_token %}
    用户名:<input type="text" name="username">
    头像: <input type="file" name="file_obj">
    <input type="submit">
</form>
```

views 视图函数写法：

```python
def upload(request):
    if request.method == 'GET':
        return render(request,'upload.html')
    else:
        print(request.POST)
        print(request.FILES)
        file_obj = request.FILES.get('file_obj')    # FILES 是一个类似于字典的对象，file_obj（也就是HTML文件input标签中的name属性值）对应的值才是文件对象
        print(file_obj.name)
        with open(file_obj.name,'wb') as f:
            # for i in file_obj:
            #     f.write(i)
            for i in file_obj.chunks():  # 65536字节
                f.write(i)
                return HttpResponse('ok')
```

当设置好 form 标签的 enctype 属性后，文件就会顺利传到视图函数中了：

```python
<MultiValueDict: {'avata': [<InMemoryUploadedFile: 1571311850334.png (image/png)>]}>
<QueryDict: {'csrfmiddlewaretoken': ['QTtPqw8y7nldamyW4uZzY6m5yzHglvGMnqQcFmUyiB97X6A3f51acJj5yIHFNeWg']}>
```

得到的 `file_obj` 是一个文件对象，与文件句柄类似。我们可以通过直接 for 循环文件句柄的方式，将文件逐行写入本地。但是如果文件的每一行都比较长，比如图片之类的媒体文件，或许只有一行，如果我们还使用逐行写入的话，会占用很多的内存资源。于是更推荐使用 for 循环 `file_obj.chunks()` ，这样每次只会循环 65536 个字节的内容，从而缓解内存的压力。

### ajax 上传文件

ajax 主要是 js 代码，对 HTML 的改变不大，只是实当增加一些 id 属性，以便更容易找到目标标签：

```html
{% csrf_token %}
用户名:<input type="text" name="username">
头像: <input type="file" name="file_obj">
<input type="submit" id="btn">
```

使用 ajax 上传文件时，需要修改一些 js 配置。首先，文件数据不能以普通的自定义对象保存，而是需要使用 `new FormData` 语句创建一个 form data 对象。使用 append 命令将一个个键值对插入到对象中。此外，还需要将 processData 和 contentType 的值设为 false。这是使用 ajax 上传文件的固定搭配，提示 ajax 不要对数据进行加工操作：

```js
$('#btn').click(function () {
    var formdata = new FormData();
    var uname = $('[name="username"]').val();
    // var file_obj = $('[name="file_obj"]').val(); //"C:\fakepath\0.jpg" 拿到的文件的本地路径
    var f_obj = $('[name="file_obj"]')[0].files[0] ; // 这是文件对象，注意是files而不是file

    formdata.append('username',uname);    // 将数据添加到formdata对象中
    formdata.append('file_obj',f_obj);
    formdata.append('csrfmiddlewaretoken',$('[name="csrfmiddlewaretoken"]').val());
    $.ajax({
        url:'/upload/',
        type:'post',
        // 上传文件时的固定搭配 formdata
        processData:false,
        contentType:false,
        data:formdata,
        // data:{uname:uname,file_obj:f_obj,'csrfmiddlewaretoken':$('[name="csrfmiddlewaretoken"]').val()},
        success:function (res) {
        console.log(res)
        }

    })
})
```

至于视图函数，如果参数没有变化，可以完全不需要修改。