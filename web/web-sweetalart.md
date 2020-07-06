## SweetAlert 弹窗工具

可以在 https://github.com/t4t5/sweetalert 下载到 SweetAlert。

SweetAlert 是一个弹窗框架，其核心部件存放在 dist 文件夹中。导入方式与 bootstrap 类似。

```html
<script src="{% static 'jquery.js' %}"></script>
<script src="{% static 'bootstrap-3.3.7-dist/js/bootstrap.min.js' %}"></script>
<script src="{% static 'tankuang/dist/sweetalert.min.js' %}"></script>
<script>
    $(".delete_btn").on("click", function () {
        // console.log($(this).attr('xxoo'));
        var ths = $(this);
      swal({
        title: "are you sure?",
        text: "滚!",
        type: "warning",
        showCancelButton: true,
        confirmButtonClass: "btn-danger",
        confirmButtonText: "再见",
        cancelButtonText: "容我三思",
        closeOnConfirm: false
        },
        function () {
          // console.log($(this));
          var deleteId = ths.attr('xxoo'); // 要删除的记录的id
          console.log('>>>>>',deleteId);
          $.ajax({
            url: "/book/swal_delete/",
            type: "post",
            data: {"id": deleteId},
            success: function (data) {
                // data --  a = {'status':1,}    a.status  a['status']
              if (data.status === 1) {
                swal("删除成功!", "你可以准备跑路了！", "success");
                // location.reload();  // 刷新页面
                  ths.parent().parent().remove();
                  var tr_first_td = $('tr .first_child');
                  for (var i=0;i<tr_first_td.length;i++){
                      // i = i+1;
                      var a = i+1;
                      $('tr .first_child').eq(i).text(a);
                  }
              } else {
                swal("删除失败", "你可以再尝试一下！", "error");
              }
            }
          })
        });
    })
</script>
```

注意：this 指代的是调用函数的对象。

在 https://www.jq22.com/ 网站中，可以找到很多很好的 js 组件，可以直接使用。

