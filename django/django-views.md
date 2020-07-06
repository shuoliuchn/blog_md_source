## Django 视图 view

### CBV 和 FBV

- FBV，function based view：基于函数的视图逻辑
- CBV，class based view：基于类的视图逻辑

CBV 中 url 写法

```python
# url(r'^login/', views.login),
url(r'^login/', views.LoginView.as_view()),
```

视图写法：

```python
from django.views import View

class LoginView(View):

    def get(self,request):
        return render(request, 'login.html')

    def post(self,request):
        uname = request.POST.get('username')
        pwd = request.POST.get('password')
        if uname == 'alex' and pwd == 'dsb':
            return redirect('/home/')
        else:
            return redirect('/login/')
```

源码重点。当调用到 as_view 方法时，会调用 dispatch 路由分发方法。通过反射的方式，调用请求的同名方法。

```python
def dispatch(self, request, *args, **kwargs):  #根据请求方法去分发对应的类发放来执行
    # Try to dispatch to the right method; if a method doesn't exist,
    # defer to the error handler. Also defer to the error handler if the
    # request method isn't on the approved list.
    if request.method.lower() in self.http_method_names:  
    	handler = getattr(self, request.method.lower(), self.http_method_not_allowed)  #反射!!!!
    else:
    	handler = self.http_method_not_allowed
    return handler(request, *args, **kwargs)
```

重写 dispatch 方法

```python
class LoginView(View):
    def dispatch(self, request, *args, **kwargs):
        print(11111)
        # print(request.META)  #http所有相关请求头信息
        ret = super().dispatch(request, *args, **kwargs) #render(request, 'login.html')
        print(2222)
        return ret
    def get(self,request):
        print('this is get method!!!')
        return render(request, 'login.html')
    def post(self,request):
        uname = request.POST.get('username')
        pwd = request.POST.get('password')
        if uname == 'alex' and pwd == 'dsb':
            return redirect('/home/')
        else:
            return redirect('/login/')
```

### CBV 和 FBV 的装饰器

```python
def func(f):
    def foo(request):
        print(1111)
        ret = f(request)
        print(2222)
        return ret
    return foo

# FBV模式下,和普通函数加装饰器是一样的写法
@func   
def home(request):
    print('home')
    return HttpResponse('你好,老了老弟,进来玩会!')

# CBV加装饰的三个姿势:
from django.utils.decorators import method_decorator
    # @method_decorator(func,name='get') # 位置3
    class LoginView(View):
        # @method_decorator(func) # 位置2
        def dispatch(self, request, *args, **kwargs):
            print('aaaa')
            ret = super().dispatch(request, *args, **kwargs) #render(request, 'login.html')
            print('bbbb')
            return ret
        @method_decorator(func)  # 位置1
        def get(self,request):
            print('this is get method!!!')
            return render(request, 'login.html')

        def post(self,request):
            uname = request.POST.get('username')
            pwd = request.POST.get('password')
            if uname == 'alex' and pwd == 'dsb':
                return redirect('/home/')
            else:
                return redirect('/login/')
```

### request 对象

request 是视图函数或方法接受的参数，里面封装了所有请求相关的信息，最常用的数据有：

```python
request.path  #request.path当前请求路径
request.method #当前请求方法(get,post...)

request.GET # 获取所有get请求携带过来的数据
request.POST # 获取所有post请求携带过来的数据
request.body # 获取所有post请求携带过来的数据的原始格式
```

### response对象

每一个视图函数或方法的返回值都必须是一个 response 对象，目前我们接触到的有：

- render  回复html页面

- redirect  重定向

- HttpResponse  回复字符串

