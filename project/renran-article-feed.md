# 荏苒资讯 Feed 流系统文章推送

## 作者发布文章后，推送 Feed 流

在前面实现了用户关注和取关以后，我们就可以在此基础上推送和拉取 Feed 流了。首先我们要实现的是推送 Feed 流。

推送 Feed 流需要在作者发布内容或者定时发布内容以后实现。

在文章发布状态切换时，获取当前作者的粉丝列表，`article/views.py`：

```python
class ArticlePublishAPIView(APIView):
    permission_classes = [IsAuthenticated]
    def put(self, request):
        """切换文章的发布状态"""
        user = request.user
        article_id = request.data.get('article_id')
        is_public = request.data.get('is_public')
        try:
            article = models.Article.objects.get(user=user, id=article_id)
        except models.Article.DoesNotExist:
            return Response({'error_msg': '要发布的文章不存在！'}, status=status.HTTP_400_BAD_REQUEST)
        article.is_public = is_public
        article.pub_date = None
        article.save()

        # 给粉丝推送/取消文章
        fans_list = self.get_fans_list(article.user.id)
        print(fans_list)
        if is_public:
            """在作者发布内容以后, 把feed推送粉丝"""
            pass
        else:
            """在作者取消发布以后, 把feed撤销回来"""
            pass
        return Response({'ok'})

    def get_fans_list(self, author_id):
        """获取作者的粉丝"""
        ts = TableStore()
        inclusive_start_primary_key = [
            ('user_id', author_id),
            ('follower_id', INF_MIN),
        ]
        exclusive_end_primary_key = [
            ('user_id', author_id),
            ('follower_id', INF_MAX),
        ]
        return ts.get_all('user_relation_table', inclusive_start_primary_key, exclusive_end_primary_key)
```

`utils/tablestore.py`，辅助工具类中提供获取多条数据的方法，代码：

```python
import tablestore as ts
from django.conf import settings


class TableStore:
    @property
    def client(self):
        return ts.OTSClient(settings.OTS_ENDPOINT, settings.OTS_ID, settings.OTS_SECRET, settings.OTS_INSTANCE)

    def get_one(self, table_name, primary_key, columns_to_get=[]):
        """根据主键获取一条数据"""
        try:
            consumed, return_row, next_token = self.client.get_row(table_name, primary_key, columns_to_get)
        except Exception:
            return {}
        if return_row is None:
            return {}
        else:
            # print(return_row.primary_key)    # [('user_id', 1), ('follower_id', 11)]
            # print(return_row.attribute_columns)    # [('follow_time', 1586700311.048392, 1586700311442)]
            return {item[0]: item[1] for item in (return_row.primary_key + return_row.attribute_columns)}

    def add_one(self, table_name, primary_key, attribute_columns):
        """添加一条数据"""
        row = ts.Row(primary_key, attribute_columns)
        condition = ts.Condition(ts.RowExistenceExpectation.IGNORE)
        try:
            # 调用put_row方法，如果没有指定ReturnType，则return_row为None。
            consumed, return_row = self.client.put_row(table_name, row, condition)
            # 客户端异常，一般为参数错误或者网络异常。
        except ts.OTSClientError as e:
            print(e.get_error_message())
            return False
            # 服务端异常，一般为参数错误或者流控错误。
        except ts.OTSServiceError as e:
            print("put row failed, http_status:%d, error_code:%s, error_message:%s, request_id:%s" % (
                e.get_http_status(), e.get_error_code(), e.get_error_message(), e.get_request_id()))
            return False
        return True

    def del_one(self, table_name, primary_key):
        row = ts.Row(primary_key)
        try:
            consumed, return_row = self.client.delete_row(table_name, row, None)
            # 客户端异常，一般为参数错误或者网络异常。
        except ts.OTSClientError as e:
            print("删除数据失败!, 状态吗:%d, 错误信息:%s" % (e.get_http_status(), e.get_error_message()))
            return False
        # 服务端异常，一般为参数错误或者流控错误。
        except ts.OTSServiceError as e:
            print("update row failed, http_status:%d, error_code:%s, error_message:%s, request_id:%s" % (
                e.get_http_status(), e.get_error_code(), e.get_error_message(), e.get_request_id()))
            return False
        return True

    def get_all(self, table_name, inclusive_start_primary_key, excluesive_end_primary_key,
                limit=None, columns_to_get=None, cond=None):
        consumed, next_start_primary_key, row_list, next_token = self.client.get_range(
            table_name,    # 操作表明
            ts.Direction.FORWARD,    # 范围的方向，字符串格式，取值包括'FORWARD'和'BACKWARD'
            inclusive_start_primary_key,    # 取值范围
            excluesive_end_primary_key,
            columns_to_get,    # 返回字段列
            limit,    # 结果数量
            column_filter=cond,    # 条件
            max_version=1,    # 返回版本数量
        )
        if len(row_list):
            data = []
            for row in row_list:
                result = row.primary_key + row.attribute_columns
                item_data = {}
                data.append(item_data)
                for item in result:
                    item_data[item[0]] = item[1]
            return data
        else:
            return []
```

在文章发布时，推送 Feed 流给粉丝列表.视图代码:

```python
from rest_framework.views import APIView
from rest_framework import status
import logging

class ArticlePublicStatusAPIView(APIView):
    """切换文章的发布状态"""
    permission_classes = [IsAuthenticated]
    def put(self,request,pk):
        try:
            article = Article.objects.get(pk=pk, user=request.user)
        except Article.DoesNotExist:
            return Response("对不起,当前文章不存在!", status=status.HTTP_400_BAD_REQUEST)

        is_public = request.data.get("is_public")
        article.is_public = not not is_public
        article.pub_date  = None
        article.save()

        # 获取当前作者的粉丝列表
        fans_list = self.get_fans_list(article.user.id)
        if is_public:
            """在作者发布内容以后, 把feed推送粉丝"""
            ret = self.push_feed(article.user.id, article.id, fans_list)
            if not ret:
                logging.error("推送Feed失败!")
        else:
            """在作者取消发布以后, 把feed撤销回来"""
            pass
        return Response("操作成功!")

    def push_feed(self, author_id, message_id, fans_list):
        """推送Feed流"""
        put_row_items = []
        ts = TableStore()
        for fans in fans_list:
            primary_key = [  # ('主键名', 值),
                ('user_id', fans),  # 接收Feed的用户ID
                ('sequence_id', PK_AUTO_INCR),  # 如果是自增主键，则值就是 PK_AUTO_INCR
                ("sender_id", author_id),  # 发布Feed的用户ID
                ("message_id", message_id),  # 文章ID
            ]
            attribute_columns = [('recevice_time', datetime.now().timestamp()), ('read_status', False)]
            put_row_items.append( ts.add_all(primary_key, attribute_columns) )

        return ts.do_all("user_message_table", put_row_items)


    def get_fans_list(self, author_id):
        """获取作者的粉丝"""
        ts = TableStore()
        inclusive_start_primary_key = [
            ('user_id', author_id),
            ('follow_user_id', INF_MIN),
        ]

        # 范围查询的结束主键
        exclusive_end_primary_key = [
            ('user_id', author_id),
            ('follow_user_id', INF_MAX),
        ]
        ret = ts.get_all("user_relation_table",inclusive_start_primary_key, exclusive_end_primary_key)
        fans_list = []
        if len(ret) > 0:
            for item in ret:
                fans_list.append( item["follow_user_id"] )
        return fans_list

```

utils/tablestore.py，代码：

```python
from tablestore import *
from django.conf import settings
class TableStore(object):
    """tablestore工具类"""
    def __init__(self):
        self.client = OTSClient(settings.OTS_ENDPOINT, settings.OTS_ID, settings.OTS_SECRET, settings.OTS_INSTANCE)

    def get_one(self,table_name, primary_key,columns_to_get = []):
        """根据主键获取一条数据"""
        try:
            consumed, return_row, next_token = self.client.get_row(table_name, primary_key, columns_to_get)
        except:
            return {}
        if return_row is None:
            return {}
        else:
            result = return_row.primary_key + return_row.attribute_columns
            data = {}
            for item in result:
                data[item[0]] = item[1]
            return data

    def add_one(self, table_name, primary_key, attribute_columns):
        """添加一条数据"""
        row = Row(primary_key, attribute_columns)
        condition = Condition(RowExistenceExpectation.IGNORE)
        try:
            # 调用put_row方法，如果没有指定ReturnType，则return_row为None。
            consumed, return_row = self.client.put_row(table_name, row, condition)
        # 客户端异常，一般为参数错误或者网络异常。
        except OTSClientError as e:
            print(e.get_error_message())
            return False
        # 服务端异常，一般为参数错误或者流控错误。
        except OTSServiceError as e:
            print("put row failed, http_status:%d, error_code:%s, error_message:%s, request_id:%s" % (
            e.get_http_status(), e.get_error_code(), e.get_error_message(), e.get_request_id()))
            return False
        return True

    def del_one(self, table_name, primary_key, ):
        """删除一条数据"""
        row = Row(primary_key)
        try:
            consumed, return_row = self.client.delete_row(table_name, row, None)
            # 客户端异常，一般为参数错误或者网络异常。
        except OTSClientError as e:
            print("删除数据失败!, 状态吗:%d, 错误信息:%s" % (e.get_http_status(), e.get_error_message()))
            return False
        # 服务端异常，一般为参数错误或者流控错误。
        except OTSServiceError as e:
            print("update row failed, http_status:%d, error_code:%s, error_message:%s, request_id:%s" % (
            e.get_http_status(), e.get_error_code(), e.get_error_message(), e.get_request_id()))
            return False
        return True

    def get_all(self, table_name, inclusive_start_primary_key, exclusive_end_primary_key, limit=None,columns_to_get = None, cond=None):
        """获取多条数据"""
        consumed, next_start_primary_key, row_list, next_token = self.client.get_range(
            table_name,  # 操作表明
            Direction.FORWARD,  # 范围的方向，字符串格式，取值包括'FORWARD'和'BACKWARD'。
            inclusive_start_primary_key, exclusive_end_primary_key,  # 取值范围
            columns_to_get,  # 返回字段列
            limit,  # 结果数量
            column_filter=cond, # 条件
            max_version=1  # 返回版本数量
        )

        if len(row_list) > 0:
            data = []
            for row in row_list:
                """
                """
                result = row.primary_key + row.attribute_columns
                item_data = {}
                data.append(item_data)
                for item in result:
                    item_data[item[0]] = item[1]
            return data
        else:
            return []

    def do_all(self,table_name,put_row_items=[]):
        """操作多条数据"""
        request = BatchWriteRowRequest()
        request.add(TableInBatchWriteRowItem(table_name, put_row_items))
        result = self.client.batch_write_row(request)
        return result.is_all_succeed()

    def add_all_items(self,primary_key,attribute_columns):
        """添加多条数据"""
        row = Row(primary_key, attribute_columns)
        condition = Condition(RowExistenceExpectation.IGNORE)
        item = PutRowItem(row, condition)
        return item
```





## 首页数据内容展示

首页数据在Feed流系统中, 需要考虑2大问题:

针对没有登录的游客显示内容的问题:

1. 纯粹的游客/没有登录
2. 已经注册但是没有关注过任何作者

```
解决:
   1. 从数据库查找到热门内容推送给用户[评论量高的, 赞赏量高的, 点赞量高的]
      如果是登录用户, 在查看了内容后, 针对用户ID保存查看记录, 哪一篇用户看过了就记录到tablestore里面
      如果是游客, 1. 在本地存储中,记录用户的浏览历史
                 2. 在tablestore里面记录当前IP的浏览历史
```



针对登录的用户显示内容的问题:

1. 用户关注了很多作者 
2. 用户关注了作者很少, 这些作者可能没有新的内容产生



```python
解决推送内容不足的情况, 接下来我们可以根据用户行为进行分析的实现基于物品的协同过滤算法来计算出用户的兴趣, 进行智能推荐.
```

综合上面所述,我们必须要显示首页的内容先然后对显示的内容进行一下步骤的过滤

```
1. 判断用户是否登录
   1.1. 用户登录了
        1.1.1 用户已经关注了其他作者
          1.1.1.1 用户关注作者中,有足够的内容展示给用户
          1.1.1.2 用户关注作者中,没有足够内容展示给用户
        1.1.2 用户没有关注任何的作者
          1.1.1.1 根据浏览历史查找内容进行热度推荐
   1.2. 用户未登录
        1.1.3 根据浏览历史查找内容进行热度推荐
```



### 客户端根据用户的登录状态发起请求获取数据

```vue
<template>
  <div id="home">
    <Header></Header>
    <div class="container">
      <div class="row">
        <div class="main">
          <!-- Banner -->
          <div class="banner">
            <el-carousel height="272px" indicator-position="none" interval="2000">
              <el-carousel-item v-for="item in 4" :key="item">
                <h3 class="small">{{ item }}</h3>
              </el-carousel-item>
            </el-carousel>
          </div>
          <div id="list-container">
            <!-- 文章列表模块 -->
            <ul class="note-list">
              <li class="">
                <div class="content">
                  <a class="title" target="_blank" href="">常做此运动，让你性福加倍</a>
                  <p class="abstract">运动，是人类在发展过程中有意识地对自己身体素质的培养的各种活动 运动的方式多种多样 不仅仅是我们常知的跑步，球类，游泳等 今天就为大家介绍一种男...</p>
                  <div class="meta">
                    <span class="jsd-meta">
                      <img src="/static/image/paid1.svg" alt=""> 4.8
                    </span>
                    <a class="nickname" target="_blank" href="">上班族也健身</a>
                    <a target="_blank" href="">
                      <img src="/static/image/comment.svg" alt=""> 4
                    </a>
                    <span><img src="/static/image/like.svg" alt=""> 31</span>
                  </div>
                </div>
              </li>
              <li class="have-img">
                <a class="wrap-img" href="" target="_blank">
                  <img class="img-blur-done" src="/static/image/10907624-107943365323e5b9.jpeg" />
                </a>
                <div class="content">
                  <a class="title" target="_blank" href="">“不耻下问”，正在毁掉你的人生</a>
                  <p class="abstract">
                    在过去，遇到不懂的问题，你不耻下问，找个人问问就行；在现在，如果你还这么干，多半会被认为是“搜商低”。 昨天，35岁的表姐把我拉黑了。 表姐是医...
                  </p>
                  <div class="meta">
                    <span class="jsd-meta">
                      <img src="/static/image/paid1.svg" alt=""> 6.7
                    </span>
                    <a class="nickname" target="_blank" href="">_飞鱼</a>
                    <a target="_blank" href="">
                      <img src="/static/image/comment.svg" alt=""> 33
                    </a>
                    <span><img src="/static/image/like.svg" alt=""> 113</span>
                    <span><img src="/static/image/shang.svg" alt=""> 2</span>
                  </div>
                </div>
              </li>
            </ul>
            <!-- 文章列表模块 -->
          </div>
        <a href="" class="load-more">阅读更多</a></div>
        <div class="aside">
          <!-- 推荐作者 -->
          <div class="recommended-author-wrap">
            <!---->
            <div class="recommended-authors">
              <div class="title">
                <span>推荐作者</span>
                <a class="page-change"><img class="icon-change" src="/static/image/exchange-rate.svg" alt="">换一批</a>
              </div>
              <ul class="list">
                <li>
                  <a href="" target="_blank" class="avatar">
                    <img src="/static/image/avatar.webp" />
                  </a>
                  <a class="follow" state="0"><img src="/static/image/follow.svg" alt="" />关注</a>
                  <a href="" target="_blank" class="name">董克平日记</a>
                  <p>写了807.1k字 · 2.5k喜欢</p>
                </li>
                <li>
                  <a href="" target="_blank" class="avatar">
                    <img src="/static/image/avatar.webp" />
                  </a>
                  <a class="follow" state="0"><img src="/static/image/follow.svg" alt="" />关注</a>
                  <a href="" target="_blank" class="name">董克平日记</a>
                  <p>写了807.1k字 · 2.5k喜欢</p>
                </li>

              </ul>
              <a href="" target="_blank" class="find-more">查看全部 ></a>
              <!---->
            </div>
          </div>
        </div>
      </div>
    </div>
    <Footer></Footer>
  </div>
</template>
<script>
  import Header from "./common/Header";
  import Footer from "./common/Footer";
  export default {
      name:"Home",
      data(){
          return {
            token:{},
            article_list:[]
          }
      },
      created(){
        this.token = this.get_login_user();
        this.get_article_list()
      },
      methods:{
          get_login_user(){
            // 获取登录用户
            return localStorage.user_token || sessionStorage.user_token;
          },
          get_article_list(){
            // 获取推送文章
            let headers = {};
            if(this.token){
              headers = {
                Authorization:"jwt " +this.token,
              }
            }
            this.$axios.get(`${this.$settings.Host}/home/article/`,{
              headers
            }).then(response=>{
              this.article_list = response.data;
            }).catch(eror=>{
              this.$message.error("获取推送文章失败！");
            });
          }
      },
      components:{
        Header,
        Footer,
      }
  }
</script>

```



### 服务端提供推送文章的内容

home/views.py,代码:

```python
from django.shortcuts import render

# Create your views here.
from rest_framework.generics import ListAPIView
from article.models import Article
from .serializers import ArticleListModelSerializer
from .paginations import HomeArticlePageNumberPagination
from django.db.models import QuerySet
class ArticleListAPIView(ListAPIView):
    serializer_class = ArticleListModelSerializer
    pagination_class = HomeArticlePageNumberPagination

    def get_queryset(self):
        queryset = Article.objects.exclude(pub_date=None,).order_by("-reward_count","-comment_count","-like_count","-id")
        return queryset   
```

home/paginations.py,代码:

```python
from rest_framework.pagination import PageNumberPagination
class HomeArticlePageNumberPagination(PageNumberPagination):
    """首页推送文章的分页器"""
    page_query_param = "page" # 地址上面代表页码的参数名
    max_page_size = 20 # 每一页显示的最大数据量
    page_size = 10     # 默认每一页显示的数据量
    page_size_query_param = "size" # 地址上面代表数据量的参数名
```

home/serializers.py,代码:

```python
from rest_framework import serializers
from article.models import Article
from users.models import User
class ArticleAuthorModelSerializer(serializers.ModelSerializer):
    class Meta:
        fields = ["id","nickname"]
        model = User
class ArticleListModelSerializer(serializers.ModelSerializer):
    user = ArticleAuthorModelSerializer()
    class Meta:
        model = Article
        fields = ["id","title","content","user","like_count","reward_count","comment_count"]
```

home/urls.py 子应用路由和总路由

```python
from django.urls import path
from . import views
urlpatterns = [
    path("article/", views.ArticleListAPIView.as_view() ),
]

# 总路由
    path('home/', include("home.urls")),
```



客户端展示内容 

```vue
<template>
  <div id="home">
    <Header></Header>
    <div class="container">
      <div class="row">
        <div class="main">
          <!-- Banner -->
          <div class="banner">
            <el-carousel height="272px" indicator-position="none" :interval="2000">
              <el-carousel-item v-for="item in 4" :key="item">
                <h3 class="small">{{ item }}</h3>
              </el-carousel-item>
            </el-carousel>
          </div>
          <div id="list-container">
            <!-- 文章列表模块 -->
            <ul class="note-list">
<!--              <li class="">-->
<!--                <div class="content">-->
<!--                  <a class="title" target="_blank" href="">常做此运动，让你性福加倍</a>-->
<!--                  <p class="abstract">运动，是人类在发展过程中有意识地对自己身体素质的培养的各种活动 运动的方式多种多样 不仅仅是我们常知的跑步，球类，游泳等 今天就为大家介绍一种男...</p>-->
<!--                  <div class="meta">-->
<!--                    <span class="jsd-meta">-->
<!--                      <img src="/static/image/paid1.svg" alt=""> 4.8-->
<!--                    </span>-->
<!--                    <a class="nickname" target="_blank" href="">上班族也健身</a>-->
<!--                    <a target="_blank" href="">-->
<!--                      <img src="/static/image/comment.svg" alt=""> 4-->
<!--                    </a>-->
<!--                    <span><img src="/static/image/like.svg" alt=""> 31</span>-->
<!--                  </div>-->
<!--                </div>-->
<!--              </li>-->
              <li :class="check_img(article.content)?'have-img':''" v-for="article in article_list">
                <a class="wrap-img" href="" target="_blank" v-if="check_img(article.content)">
                  <img class="img-blur-done" :src="check_img(article.content)" />
                </a>
                <div class="content">
                  <router-link class="title" target="_blank" :to="`/article/${article.id}`">{{article.title}}</router-link>
                  <p class="abstract"　v-html="subtext(article.content,120)">
                  </p>
                  <div class="meta">
                    <a class="nickname" target="_blank" href="">{{article.user.nickname}}</a>
                    <a target="_blank" href="">
                      <img src="/static/image/comment.svg" alt=""> {{article.comment_count}}
                    </a>
                    <span><img src="/static/image/like.svg" alt=""> {{article.like_count}}</span>
                    <span v-if="article.reward_count>0"><img src="/static/image/shang.svg" alt=""> {{article.reward_count}}</span>
                  </div>
                </div>
              </li>
            </ul>
            <!-- 文章列表模块 -->
          </div>
        <a href="" class="load-more">阅读更多</a></div>
        <div class="aside">
          <!-- 推荐作者 -->
          <div class="recommended-author-wrap">
            <!---->
            <div class="recommended-authors">
              <div class="title">
                <span>推荐作者</span>
                <a class="page-change"><img class="icon-change" src="/static/image/exchange-rate.svg" alt="">换一批</a>
              </div>
              <ul class="list">
                <li>
                  <a href="" target="_blank" class="avatar">
                    <img src="/static/image/avatar.webp" />
                  </a>
                  <a class="follow" state="0"><img src="/static/image/follow.svg" alt="" />关注</a>
                  <a href="" target="_blank" class="name">董克平日记</a>
                  <p>写了807.1k字 · 2.5k喜欢</p>
                </li>
                <li>
                  <a href="" target="_blank" class="avatar">
                    <img src="/static/image/avatar.webp" />
                  </a>
                  <a class="follow" state="0"><img src="/static/image/follow.svg" alt="" />关注</a>
                  <a href="" target="_blank" class="name">董克平日记</a>
                  <p>写了807.1k字 · 2.5k喜欢</p>
                </li>

              </ul>
              <a href="" target="_blank" class="find-more">查看全部 ></a>
              <!---->
            </div>
          </div>
        </div>
      </div>
    </div>
    <Footer></Footer>
  </div>
</template>
<script>
  import Header from "./common/Header";
  import Footer from "./common/Footer";
  export default {
      name:"Home",
      data(){
          return {
            token:{},
            article_list:[
              {
                "user":{

                }
              }
            ]
          }
      },
      created(){
        this.token = this.get_login_user();
        this.get_article_list()
      },
      methods:{
          subtext(content,len=100){
            // 如果文章开头有图片，则过滤图片
            if(content){
              while (content.search("<img") != -1){
                content = content.replace(/<img.*?src="(.*?)".*?>/,"")
              }
              return content.split("").slice(0,len).join("")+'...';
            }
            return "";
          },
          check_img(content){
            if(content){
              let ret = content.match(/<img.*?src="(.*?)".*?>/)
              if(ret){
                return ret[1];
              }
            }

            return false;

          },
          get_login_user(){
            // 获取登录用户
            return localStorage.user_token || sessionStorage.user_token;
          },
          get_article_list(){
            // 获取推送文章
            let headers = {};
            if(this.token){
              headers = {
                Authorization:"jwt " +this.token,
              }
            }
            this.$axios.get(`${this.$settings.Host}/home/article/`,{
              headers
            }).then(response=>{
              this.article_list = response.data.results;
              this.article_count = response.data.count;
              this.new_article_list = response.data.next;
            }).catch(eror=>{
              this.$message.error("获取推送文章失败！");
            });
          }
      },
      components:{
        Header,
        Footer,
      }
  }
</script>
```

使用函数节流, 在用户频繁点击ajax按钮时, 阻止点击

```vue
<template>
  <div id="home">
    <Header></Header>
    <div class="container">
      <div class="row">
        <div class="main">
          <!-- Banner -->
          <div class="banner">
            <el-carousel height="272px" indicator-position="none" :interval="2000">
              <el-carousel-item v-for="item in 4" :key="item">
                <h3 class="small">{{ item }}</h3>
              </el-carousel-item>
            </el-carousel>
          </div>
          <div id="list-container">
            <!-- 文章列表模块 -->
            <ul class="note-list">
<!--              <li class="">-->
<!--                <div class="content">-->
<!--                  <a class="title" target="_blank" href="">常做此运动，让你性福加倍</a>-->
<!--                  <p class="abstract">运动，是人类在发展过程中有意识地对自己身体素质的培养的各种活动 运动的方式多种多样 不仅仅是我们常知的跑步，球类，游泳等 今天就为大家介绍一种男...</p>-->
<!--                  <div class="meta">-->
<!--                    <span class="jsd-meta">-->
<!--                      <img src="/static/image/paid1.svg" alt=""> 4.8-->
<!--                    </span>-->
<!--                    <a class="nickname" target="_blank" href="">上班族也健身</a>-->
<!--                    <a target="_blank" href="">-->
<!--                      <img src="/static/image/comment.svg" alt=""> 4-->
<!--                    </a>-->
<!--                    <span><img src="/static/image/like.svg" alt=""> 31</span>-->
<!--                  </div>-->
<!--                </div>-->
<!--              </li>-->
              <li :class="check_img(article.content)?'have-img':''" v-for="article in article_list">
                <a class="wrap-img" href="" target="_blank" v-if="check_img(article.content)">
                  <img class="img-blur-done" :src="check_img(article.content)" />
                </a>
                <div class="content">
                  <router-link class="title" target="_blank" :to="`/article/${article.id}`">{{article.title}}</router-link>
                  <p class="abstract"　v-html="subtext(article.content,120)">
                  </p>
                  <div class="meta">
                    <a class="nickname" target="_blank" href="">{{article.user.nickname}}</a>
                    <a target="_blank" href="">
                      <img src="/static/image/comment.svg" alt=""> {{article.comment_count}}
                    </a>
                    <span><img src="/static/image/like.svg" alt=""> {{article.like_count}}</span>
                    <span v-if="article.reward_count>0"><img src="/static/image/shang.svg" alt=""> {{article.reward_count}}</span>
                  </div>
                </div>
              </li>
            </ul>
            <!-- 文章列表模块 -->
          </div>
        <a @click="get_next_article" class="load-more" v-if="new_article_list">阅读更多</a></div>
        <div class="aside">
          <!-- 推荐作者 -->
          <div class="recommended-author-wrap">
            <!---->
            <div class="recommended-authors">
              <div class="title">
                <span>推荐作者</span>
                <a class="page-change"><img class="icon-change" src="/static/image/exchange-rate.svg" alt="">换一批</a>
              </div>
              <ul class="list">
                <li>
                  <a href="" target="_blank" class="avatar">
                    <img src="/static/image/avatar.webp" />
                  </a>
                  <a class="follow" state="0"><img src="/static/image/follow.svg" alt="" />关注</a>
                  <a href="" target="_blank" class="name">董克平日记</a>
                  <p>写了807.1k字 · 2.5k喜欢</p>
                </li>
                <li>
                  <a href="" target="_blank" class="avatar">
                    <img src="/static/image/avatar.webp" />
                  </a>
                  <a class="follow" state="0"><img src="/static/image/follow.svg" alt="" />关注</a>
                  <a href="" target="_blank" class="name">董克平日记</a>
                  <p>写了807.1k字 · 2.5k喜欢</p>
                </li>

              </ul>
              <a href="" target="_blank" class="find-more">查看全部 ></a>
              <!---->
            </div>
          </div>
        </div>
      </div>
    </div>
    <Footer></Footer>
  </div>
</template>
<script>
  import Header from "./common/Header";
  import Footer from "./common/Footer";
  export default {
      name:"Home",
      data(){
          return {
            get_article_url:"",
            new_article_list:"",
            is_send_get_article_request:false, // 函数节流，判断当前ajax是否执行过程中
            token:{},
            article_list:[
              {
                "user":{

                }
              }
            ]
          }
      },
      created(){
        this.token = this.get_login_user();
        this.get_article_url = `${this.$settings.Host}/home/article/`;
        this.get_article_list()
      },
      methods:{
          subtext(content,len=100){
            // 如果文章开头有图片，则过滤图片
            if(content){
              while (content.search("<img") != -1){
                content = content.replace(/<img.*?src="(.*?)".*?>/,"")
              }
              return content.split("").slice(0,len).join("")+'...';
            }
            return "";
          },
          check_img(content){
            if(content){
              let ret = content.match(/<img.*?src="(.*?)".*?>/)
              if(ret){
                return ret[1];
              }
            }

            return false;

          },
          get_login_user(){
            // 获取登录用户
            return localStorage.user_token || sessionStorage.user_token;
          },
          get_article_list(){

            // 判断当前ajax是否正在执行
            if(this.is_send_get_article_request){
              this.$message.error("点击过于频繁！");
              return false;
            }

            this.is_send_get_article_request = true;

            // 获取推送文章
            let headers = {};
            if(this.token){
              headers = {
                Authorization:"jwt " +this.token,
              }
            }
            this.$axios.get(this.get_article_url,{
              headers
            }).then(response=>{
              if(!this.new_article_list){
                this.article_list = response.data.results;
              }else{
                this.article_list = this.article_list.concat(response.data.results);
              }
              this.article_count = response.data.count;
              this.new_article_list = response.data.next;

              // 开启再次执行ajax的状态
              this.is_send_get_article_request = false;
            }).catch(eror=>{
              this.$message.error("获取推送文章失败！");
            });
          },
          get_next_article(){
            this.get_article_url = this.new_article_list;
            this.get_article_list();
          }
      },
      components:{
        Header,
        Footer,
      }
  }
</script>

```

```
1. 判断用户是否登录
   1.1. 用户登录了
        1.1.1 用户已经关注了其他作者
          1.1.1.1 用户关注作者中,有足够的内容展示给用户
          1.1.1.2 用户关注作者中,没有足够内容展示给用户
        1.1.2 用户没有关注任何的作者
          1.1.1.1 根据浏览历史查找内容进行热度推荐
   1.2. 用户未登录
        1.1.3 根据浏览历史查找内容进行热度推荐
```

因为用户查看首页时, 显示的文章是不能重复的,所以接下来我们需要在每次推送文章给用户的时候,必须要进行记录:

## 实现用户和Feed内容的日志记录

用户和文章的推送日志

表格: user_message_log_table

| 第一主键 | (第二主键) | 属性列                                          |
| -------- | ---------- | ----------------------------------------------- |
| user_id  | message_id | is_push, is_read, is_like,is_reward, is_comment |

在home/management/commands/tablestore.py,自定义命令中,新增创建日志的命令:

```python
from django.core.management import BaseCommand
from tablestore import *
from django.conf import settings
class Command(BaseCommand):
    help = """表格存储命令必须接收而且只接收1个命令参数，如下：
    create  表示创建项目使用的表格
    delete  表示删除项目使用的表格
    """

    def add_arguments(self,parser):
        """参数设置"""
        parser.add_argument("argument",nargs="*", help="操作类型") # 位置参数

    def handle(self, *args, **options):
        """表格存储的初始化"""
        argument = options.get("argument")
        if len(argument)==1:
            if argument[0] == "create":
                """创建表格"""
                self.create_table()

            elif argument[0] == "delete":
                """删除表格"""
                self.delete_table()
            else:
                self.stdout.write(self.help)
        else:
            self.stdout.write(self.help)

    @property
    def client(self):
        return OTSClient(settings.OTS_ENDPOINT, settings.OTS_ID, settings.OTS_SECRET, settings.OTS_INSTANCE)

    def set_table(self,table_name,schema_of_primary_key,time_to_live=-1):
        # 设置表的元信息
        table_meta = TableMeta(table_name, schema_of_primary_key)
        # 设置数据的有效型
        table_option = TableOptions(time_to_live=time_to_live, max_version=5)
        # 设置数据的预留读写吞吐量
        reserved_throughput = ReservedThroughput(CapacityUnit(0, 0))
        # 创建数据
        self.client.create_table(table_meta, table_option, reserved_throughput)

    def create_table(self):
        """创建表格"""
        # 创建存储库
        table_name = "user_message_table"
        schema_of_primary_key = [ # 主键列
            ('user_id', 'INTEGER'),
            ('sequence_id', 'INTEGER',PK_AUTO_INCR),
            ("sender_id",'INTEGER'),
            ("message_id",'INTEGER'),
        ]

        self.set_table(table_name,schema_of_primary_key,time_to_live=7*86400)
        self.stdout.write("创建表格%s完成" % table_name)

        #　关系库
        table_name = "user_relation_table"
        # 主键列
        schema_of_primary_key = [
            ('user_id', 'INTEGER'),
            ("follow_user_id", 'INTEGER'),
        ]
        self.set_table(table_name, schema_of_primary_key)
        self.stdout.write("创建表格%s完成" % table_name)

        # 未读池
        table_name = "user_message_session_table"
        # 主键列
        schema_of_primary_key = [
            ('user_id', 'INTEGER'),
            ("last_sequence_id", 'INTEGER'),
        ]
        self.set_table(table_name, schema_of_primary_key)
        self.stdout.write("创建表格%s完成" % table_name)


        # 用户对文章的访问操作日志
        table_name = "user_message_log_table"
        schema_of_primary_key = [ # 主键列
            ('user_id', 'INTEGER'),
            ("message_id",'INTEGER'),
        ]
        self.set_table(table_name, schema_of_primary_key)
        self.stdout.write("创建表格%s完成" % table_name)

    def delete_table(self):
        """删除表"""
        table_list = self.client.list_table()
        for table in table_list:
            self.client.delete_table(table)
            self.stdout.write("删除%s完成" % table)
```



在首页获取推送内容时, 直接查询推送内容:

```python
from django.shortcuts import render

# Create your views here.
from rest_framework.generics import ListAPIView
from article.models import Article
from .serializers import ArticleListModelSerializer
from .paginations import HomeArticlePageNumberPagination
from tablestore import *
from django.conf import settings
from users.models import User
from datetime import datetime
class ArticleListAPIView(ListAPIView):
    serializer_class = ArticleListModelSerializer
    pagination_class = HomeArticlePageNumberPagination

    @property
    def client(self):
        return OTSClient(settings.OTS_ENDPOINT, settings.OTS_ID, settings.OTS_SECRET, settings.OTS_INSTANCE)

    def get_queryset(self):
        week_timestamp = datetime.now().timestamp() - 7 * 86400
        week_date = datetime.fromtimestamp(week_timestamp) # 获取一周前的时间对象
        queryset = Article.objects.filter(pub_date__gte=week_date).exclude(pub_date=None,).order_by("-reward_count","-comment_count","-like_count","-id")

        # 记录本次给用户推送文章的记录
        user = self.request.user

        if isinstance(user, User):
            # 判断tablestore中是否曾经推送过当前当前文章给用户
            queryset = self.check_user_message_log(user, queryset)

            if len(queryset)>0:
                article_id_list = []
                for item in queryset:
                    article_id_list.append(item.id)
                self.push_log(user.id, article_id_list)

        return queryset

    def check_user_message_log(self, user, queryset):
        """判断系统是否曾经推送过文章给用户"""
        columns_to_get = []
        rows_to_get = []
        for article in queryset:
            primary_key = [('user_id', user.id), ('message_id', article.id)]
            rows_to_get.append(primary_key)
        request = BatchGetRowRequest()
        table_name = "user_message_log_table"

        cond = CompositeColumnCondition(LogicalOperator.OR)
        cond.add_sub_condition(SingleColumnCondition("is_read", True, ComparatorType.EQUAL))
        cond.add_sub_condition(SingleColumnCondition("is_like", True, ComparatorType.EQUAL))

        request.add(TableInBatchGetRowItem(table_name, rows_to_get, columns_to_get,column_filter=cond, max_version=1))
        result = self.client.batch_get_row(request)
        table_result = result.get_result_by_table(table_name)
        push_id_list = []
        for item in table_result:
            if item.row is not None:
                push_id_list.append(item.row.primary_key[1][1])

        return queryset.exclude(id__in=push_id_list)

    def push_log(self, user_id, article_id_list):
        """推送文章给用户的记录"""
        table_name = "user_message_log_table"

        put_row_items = []

        for i in article_id_list:
            # 主键列
            primary_key = [
                ('user_id', user_id),  # 用户ID
                ("message_id", i),  # 文章ID
            ]

            attribute_columns = [('is_push', True), ('is_read', False), ('is_like', False), ('is_reward',False), ('is_comment',False)]

            row = Row(primary_key, attribute_columns)
            condition = Condition(RowExistenceExpectation.IGNORE)
            item = PutRowItem(row, condition)
            put_row_items.append(item)

        request = BatchWriteRowRequest()
        request.add(TableInBatchWriteRowItem(table_name, put_row_items))
        result = self.client.batch_write_row(request)
        return result.is_all_succeed()
```



在用户点击阅读文章以后, 更新阅读记录

```python
    # Create your views here.

    from .models import ArticleImage
    from rest_framework.generics import CreateAPIView
    from .serializers import ArticleImageModelSerializer
    class ImageAPIView(CreateAPIView):
        queryset = ArticleImage.objects.all()
        serializer_class = ArticleImageModelSerializer


    from .models import ArticleCollection
    from .serializers import ArticleCollectionModelSerializer
    from rest_framework.generics import ListAPIView
    from rest_framework.permissions import IsAuthenticated
    from rest_framework.response import Response

    class CollecionAPIView(CreateAPIView, ListAPIView):
        """文集的视图接口"""
        queryset = ArticleCollection.objects.all()
        serializer_class = ArticleCollectionModelSerializer
        permission_classes = [IsAuthenticated]

        def list(self, request, *args, **kwargs):
            user = request.user
            queryset = self.filter_queryset(self.get_queryset().filter(user=user))

            page = self.paginate_queryset(queryset)
            if page is not None:
                serializer = self.get_serializer(page, many=True)
                return self.get_paginated_response(serializer.data)

            serializer = self.get_serializer(queryset, many=True)
            return Response(serializer.data)

    from rest_framework.generics import UpdateAPIView
    from .serializers import ArticleCollectionDetailModelSerializer
    class CollecionDetailAPIView(UpdateAPIView):
        """文集的视图接口"""
        queryset = ArticleCollection.objects.all()
        serializer_class = ArticleCollectionDetailModelSerializer
        permission_classes = [IsAuthenticated]


    from rest_framework.viewsets import ModelViewSet
    from .models import Article
    from .serializers import ArticleModelSerializer
    from rest_framework.decorators import action
    from rest_framework import status
    from django_redis import get_redis_connection
    from datetime import datetime
    from django.db import transaction

    class ArticleAPIView(ModelViewSet):
        """文章的视图集接口"""
        queryset = Article.objects.all()
        serializer_class = ArticleModelSerializer
        permission_classes = [IsAuthenticated]

        @property
        def client(self):
            return OTSClient(settings.OTS_ENDPOINT, settings.OTS_ID, settings.OTS_SECRET, settings.OTS_INSTANCE)

        @action(methods=["PUT"], detail=True)
        def save_article(self,request,pk):
            # 接收文章内容，标题，编辑次数，文章ID
            content = request.data.get("content")
            title = request.data.get("title")
            save_id = int( request.data.get("save_id") )
            collection_id = request.data.get("collection_id")
            user = request.user
            if save_id is None:
                save_id = 1
            else:
                save_id += 1

            # 验证文章是否存在
            try:
                article = Article.objects.get(pk=pk)
            except Article.DoesNotExist:
                return Response({"message":"当前文章不存在！"}, status=status.HTTP_400_BAD_REQUEST)

            # 写入到redis中[先配置redis库]
            redis_conn = get_redis_connection("article")
            """
            article_<user_id>_<article>_<save_id>:{
                "title":   title,
                "content": content,
                "update_time": 1929293,
                "collection_id":collection_id,
            }
            """
            new_timestamp = datetime.now().timestamp()
            data = {
                "title": title,
                "content": content,
                "updated_time": new_timestamp,
                "collection_id": collection_id,
            }
            redis_conn.hmset("article_%s_%s_%s" % (user.id, pk, save_id), data)
            # 把用户针对当前文章的最新编辑记录ID保存起来
            redis_conn.hset("article_history_%s" % (user.id), pk, save_id )
            # 实现查看当前文章的编辑历史的思路：
            # article_edit_history = redis_conn.keys("article_%s_%s*" % (user.id, pk) )
            # data_list = []
            # for item in article_edit_history:
            #     ret = redis_conn.hgetall(item)
            #     data_list.append({
            #         "title": ret.get("title".encode()).decode(),
            #         "content": ret.get("content".encode()).decode(),
            #         "updated_time": ret.get("updated_time".encode()).decode(),
            #     })
            # print(data_list)
            # 返回结果
            return Response({"message":"保存成功！","save_id": save_id})

        def list(self, request, *args, **kwargs):
            user = request.user
            collection_id = request.query_params.get("collection")
            try:
                ArticleCollection.objects.get(pk=collection_id)
            except ArticleCollection.DoesNotExist:
                return Response({"message":"对不起，当前文集不存在！"})

            # 先到redis中查询
            redis_conn = get_redis_connection("article")
            history_dist = redis_conn.hgetall("article_history_%s" % (user.id) )
            data = []
            exclude_id = []
            if history_dist is not None:
                for article_id, save_id in history_dist.items():
                    article_id = article_id.decode()
                    save_id = save_id.decode()
                    article_data_byte = redis_conn.hgetall("article_%s_%s_%s" % (user.id, article_id, save_id) )
                    if article_data_byte["collection_id".encode()].decode() == collection_id:
                        data.append({
                            "id": article_id,
                            "title": article_data_byte["title".encode()].decode(),
                            "content": article_data_byte["content".encode()].decode(),
                            "save_id": save_id,
                            "collection": collection_id,
                        })
                        exclude_id.append(article_id)

            # 然后把redis中已经编辑过的内容结果排除出来，然后到MySQL中查询

            queryset = self.filter_queryset(self.get_queryset().filter(user=user, collection_id=collection_id).exclude(id__in=exclude_id) )

            page = self.paginate_queryset(queryset)
            if page is not None:
                serializer = self.get_serializer(page, many=True)
                return self.get_paginated_response(serializer.data)

            serializer = self.get_serializer(queryset, many=True)

            data +=serializer.data

            return Response(data)

        @action(methods=["patch"],detail=True)
        def pub_article(self,request,pk):
            """发布文章"""
            user    = request.user
            status  = request.data.get("is_pub")

            with transaction.atomic():
                save_id = transaction.savepoint()
                try:
                    article = Article.objects.get(user=user, pk=pk)
                except:
                    transaction.savepoint_rollback(save_id)
                    return Response({"message":"当前文章不存在或者您没有修改的权限！"})

                if status:
                    """发布文章"""
                    article.pub_date = datetime.now()

                    # 先查询到当前作者的粉丝 user_relation_table中查询　
                    fens_list = self.get_fens(user.id)

                    # 循环结果，把Feed进行推送
                    if len(fens_list) > 0:
                        ret = self.push_feed(fens_list, user.id, article.id)
                        if not ret:
                            transaction.savepoint_rollback(save_id)
                            message = {"message": "发布文章失败！"}
                        else:
                            message = {"message": "发布文章成功！"}
                    else:
                        message = {"message":"发布文章成功"}
                else:
                    """私密文章，取消发布"""
                    article.pub_date = None
                    message = {"message":"取消发布成功"}

                # 从redis的编辑记录中提取当前文章的最新记录
                redis_conn = get_redis_connection("article")
                user_history_dist = redis_conn.hgetall("article_history_%s" % user.id)
                save_id = user_history_dist.get(pk.encode()).decode()
                article_dict = redis_conn.hgetall("article_%s_%s_%s" % (user.id, pk, save_id) )
                if article_dict is not None:
                    article.title = article_dict["title".encode()].decode()
                    article.content = article_dict["content".encode()].decode()
                    timestamp = datetime.fromtimestamp(int(float(article_dict["updated_time".encode()].decode())))
                    article.updated_time = timestamp
                    article.save_id = save_id
                article.save()

                return Response(message)

        def push_feed(self, fens_list,author_id, article_id):
            """推送Feed给粉丝"""
            table_name = "user_message_table"

            put_row_items = []

            for i in fens_list:
                # 主键列
                primary_key = [  # ('主键名', 值),
                    ('user_id', i),  # 接收Feed的用户ID
                    ('sequence_id', PK_AUTO_INCR),  # 如果是自增主键，则值就是 PK_AUTO_INCR
                    ("sender_id", author_id),  # 发布Feed的用户ID
                    ("message_id", article_id),  # 文章ID
                ]

                attribute_columns = [('recevice_time', datetime.now().timestamp()), ('read_status', False)]
                row = Row(primary_key, attribute_columns)
                condition = Condition(RowExistenceExpectation.IGNORE)
                item = PutRowItem(row, condition)
                put_row_items.append(item)

            request = BatchWriteRowRequest()
            request.add(TableInBatchWriteRowItem(table_name, put_row_items))
            result = self.client.batch_write_row(request)

            return result.is_all_succeed()

        def get_fens(self, user_id):
            """获取当前用户的所有粉丝，后面自己整理下这个方法到工具库中"""
            table_name = "user_relation_table"

            # 范围查询的起始主键
            inclusive_start_primary_key = [
                ('user_id', user_id),
                ('follow_user_id', INF_MIN)
            ]

            # 范围查询的结束主键
            exclusive_end_primary_key = [
                ('user_id', user_id),
                ('follow_user_id', INF_MAX)
            ]

            # 查询所有列
            columns_to_get = [] # 表示返回所有列

            # 范围查询接口
            consumed, next_start_primary_key, row_list, next_token = self.client.get_range(
                table_name,  # 操作表明
                Direction.FORWARD,  # 范围的方向，字符串格式，取值包括'FORWARD'和'BACKWARD'。
                inclusive_start_primary_key, exclusive_end_primary_key,  # 取值范围
                columns_to_get,  # 返回字段列
                max_version=1  # 返回版本数量
            )

            fens_list = []
            for row in row_list:
                fens_list.append( row.primary_key[1][1] )

            return fens_list

        @action(methods=["patch"], detail=True)
        def change_collection(self, request, pk):
            """切换当前文章的文集ID"""
            user = request.user
            collection_id = request.data.get("collection_id")
            try:
                article = Article.objects.get(user=user, pk=pk)
            except:
                return Response({"message": "当前文章不存在或者您没有修改的权限！"})

            try:
                ArticleCollection.objects.get(user=user, pk=collection_id)
            except:
                return Response({"message": "当前文集不存在或者您没有修改的权限！"})

            # 当前文章如果之前有曾经被编辑，则需要修改redis中的缓存
            redis_conn = get_redis_connection("article")
            save_id_bytes = redis_conn.hget("article_history_%s" % (user.id),pk)
            if save_id_bytes is not None:
                save_id = save_id_bytes.decode()
                redis_conn.hset("article_%s_%s_%s" % (user.id, pk, save_id ), "collection_id", collection_id )
            article.collection_id = collection_id
            article.save()

            return Response({"message":"切换文章的文集成功！"})

    from .models import Special,SpecialArticle
    from .serializers import SpecialModelSerializer
    class SpecialListAPIView(ListAPIView):
        queryset = Special.objects.all()
        serializer_class = SpecialModelSerializer
        permission_classes = [IsAuthenticated]

        def list(self, request, *args, **kwargs):
            user = request.user
            ret = self.get_queryset().filter(mymanager__user=user)
            article_id = request.query_params.get("article_id")
            # 验证文章

            queryset = self.filter_queryset(ret)
            page = self.paginate_queryset(queryset)
            if page is not None:
                serializer = self.get_serializer(page, many=True)
                return self.get_paginated_response(serializer.data)

            serializer = self.get_serializer(queryset, many=True)
            # 返回专题对于当前文章的收录状态
            data = []
            for special in serializer.data:
                try:
                    SpecialArticle.objects.get(article_id=article_id, special_id=special.get("id"))
                    special["post_status"] = True # 表示当前文章已经被专题收录了
                except SpecialArticle.DoesNotExist:
                    special["post_status"] = False  # 表示当前文章已经被专题收录了
                data.append(special)
            return Response(data)

    class ArticlePostSpecialAPIView(CreateAPIView):
        permission_classes = [IsAuthenticated]
        def post(self,request):
            """收录到我管理的专题"""
            article_id = request.data.get("article_id")
            special_id = request.data.get("special_id")
            user = request.user
            try:
                Article.objects.get(user=user, pk=article_id)
            except Article.DoesNotExist:
                return Response({"message": "当前文章不存在或者您没有操作的权限！"})

            try:
                Special.objects.get(mymanager__user=user, pk=special_id)
            except Article.DoesNotExist:
                return Response({"message": "当前专题不存在或者您没有操作的权限！"})

            SpecialArticle.objects.create(article_id=article_id,special_id=special_id)

            return Response({"message":"收录成功！"})

    from rest_framework.generics import RetrieveAPIView
    from .serializers import ArticleInfoModelSerializer
    from users.models import User
    from tablestore import *
    from django.conf import settings

    class ArticleInfoAPIView(RetrieveAPIView):
        """文章详情"""
        serializer_class = ArticleInfoModelSerializer
        queryset = Article.objects.exclude(pub_date=None)
        @property
        def client(self):
            return OTSClient(settings.OTS_ENDPOINT, settings.OTS_ID, settings.OTS_SECRET, settings.OTS_INSTANCE)

        def retrieve(self, request, *args, **kwargs):
            response = super().retrieve(request, *args, **kwargs)
            if isinstance(request.user, User):
                """用户登录了"""
                user = request.user                                    # 访问者
                author_id = response.data.get("user").get("id")        # 文章作者

                # 用户对文章的阅读记录
                article_id = kwargs.get("pk")
                self.read_log(user.id, article_id)

                if author_id != user.id:
                    # 到tablestore里面查询当前访问者是否关注了文章作者
                    table_name = "user_relation_table"

                    primary_key = [('user_id', author_id), ('follow_user_id', user.id)]

                    columns_to_get = []

                    consumed, return_row, next_token = self.client.get_row(table_name, primary_key, columns_to_get)

                    if return_row is None:
                        """没有关注"""
                        is_follow = 1
                    else:
                        """已经关注了"""
                        is_follow = 2

                else:
                    is_follow = 3 # 当前用户就是作者

            else:
                """用户未登录"""
                is_follow = 0  # 当前访问者未登录

            response.data["is_follow"] = is_follow
            return response

        def read_log(self,user_id, article_id):
            """更新用户对文章的阅读记录"""
            table_name = "user_message_log_table"
            primary_key = [('user_id', int(user_id)), ('message_id', int(article_id))]
            update_of_attribute_columns = {
                'PUT': [('is_read', True)],
            }
            row = Row(primary_key, update_of_attribute_columns)
            condition = Condition(RowExistenceExpectation.IGNORE,
                                  SingleColumnCondition("is_read", False, ComparatorType.EQUAL))  # update row on\
            consumed, return_row = self.client.update_row(table_name, row, condition)

```

在首页中展示 用户关注推送Feed内容

```python
from django.shortcuts import render

# Create your views here.
from rest_framework.generics import ListAPIView
from article.models import Article
from .serializers import ArticleListModelSerializer
from .paginations import HomeArticlePageNumberPagination
from tablestore import *
from django.conf import settings
from users.models import User
from datetime import datetime
class ArticleListAPIView(ListAPIView):
    serializer_class = ArticleListModelSerializer
    pagination_class = HomeArticlePageNumberPagination

    @property
    def client(self):
        return OTSClient(settings.OTS_ENDPOINT, settings.OTS_ID, settings.OTS_SECRET, settings.OTS_INSTANCE)

    def get_queryset(self):
        week_timestamp = datetime.now().timestamp() - 7 * 86400
        week_date = datetime.fromtimestamp(week_timestamp) # 获取一周前的时间对象
        queryset = Article.objects.filter(pub_date__gte=week_date).exclude(pub_date=None,).order_by("-reward_count","-comment_count","-like_count","-id")

        # 记录本次给用户推送文章的记录
        user = self.request.user

        if isinstance(user, User):

            # 查询当前用户关注的作者是否有新的推送[查询同步库]
            # 先到未读池中提取当前访问用户上一次读取Feed的最大主键
            start_sequence_id = self.get_start_sequence_id(user.id)
            # 然后根据主键到同步库中查看数据
            message_id_list = self.get_feed_message(user.id, start_sequence_id)
            if len(message_id_list)>=10:
                """如果未读池存在10条以上的推送内容"""
                queryset = queryset.filter(id__in=message_id_list)

            else:
                # 基于物品进行协同过滤推荐文章
                message_id_list = self.get_message_by_itemCF(user.id)

                # 判断tablestore中是否曾经推送过当前当前文章给用户
                queryset = self.check_user_message_log(user, queryset)

            # 更新推送日志
            if len(queryset)>0:
                article_id_list = []
                for item in queryset:
                    article_id_list.append(item.id)
                self.push_log(user.id, article_id_list)

        return queryset

    def get_message_by_itemCF(self,user_id):
        """基于物品的协同过滤获取Feed内容"""
        pass

    def get_feed_message(self,user_id, start_sequence_id):
        """获取同步库中的数据"""
        table_name = "user_message_table"

        # 范围查询的起始主键
        inclusive_start_primary_key = [
            ('user_id', user_id),
            ('sequence_id', start_sequence_id),
            ('sender_id', INF_MIN),
            ('message_id', INF_MIN)
        ]

        # 范围查询的结束主键
        exclusive_end_primary_key = [
            ('user_id', user_id),
            ('sequence_id', INF_MAX),
            ('sender_id', INF_MAX),
            ('message_id', INF_MAX),
        ]

        columns_to_get = [] # 表示返回所有列
        limit = 10

        consumed, next_start_primary_key, row_list, next_token = self.client.get_range(
            table_name, # 操作表明
            Direction.FORWARD, # 范围的方向，字符串格式，取值包括'FORWARD'和'BACKWARD'。
            inclusive_start_primary_key, exclusive_end_primary_key, # 取值范围
            columns_to_get, # 返回字段列
            limit, #　结果数量
            max_version=1         # 返回版本数量
        )

        message_id_list = []
        for item in row_list:
            message_id_list.append( item.primary_key[3][1] )

        # 下一次读取同步库的开始主键
        try:
            self.set_start_sequence_id(user_id, start_sequence_id, next_start_primary_key[1][1])
        except:
            pass

        return message_id_list

    def set_start_sequence_id(self, user_id,old_start_sequence_id, next_start_primary_key):
        table_name = "user_message_session_table"
        try:
            primary_key = [('user_id', user_id), ('last_sequence_id', old_start_sequence_id)]
            row = Row(primary_key)
            consumed, return_row = self.client.delete_row(table_name, row, None)
        except:
            pass

        primary_key = [('user_id', user_id), ('last_sequence_id', next_start_primary_key)]
        attribute_columns = []
        row = Row(primary_key, attribute_columns)
        consumed, return_row = self.client.put_row(table_name, row)
        print(return_row)
        return return_row

    def get_start_sequence_id(self,user_id):
        """获取最后读取的Feed流id"""
        table_name = "user_message_session_table"

        # 范围查询的起始主键
        inclusive_start_primary_key = [
            ('user_id', user_id),
            ('last_sequence_id', INF_MIN)
        ]

        # 范围查询的结束主键
        exclusive_end_primary_key = [
            ('user_id', user_id),
            ('last_sequence_id', INF_MAX)
        ]

        columns_to_get = [] # 表示返回所有列
        limit = 1

        consumed, next_start_primary_key, row_list, next_token = self.client.get_range(
            table_name, # 操作表明
            Direction.FORWARD, # 范围的方向，字符串格式，取值包括'FORWARD'和'BACKWARD'。
            inclusive_start_primary_key, exclusive_end_primary_key, # 取值范围
            columns_to_get, # 返回字段列
            limit, #　结果数量
            # column_filter=cond, # 条件
            max_version=1         # 返回版本数量
        )

        if len(row_list) < 1:
            # 之前没有读取过推送内容
            return INF_MIN
        else:
            return row_list[0].primary_key[1][1]


    def check_user_message_log(self, user, queryset):
        """判断系统是否曾经推送过文章给用户"""
        columns_to_get = []
        rows_to_get = []
        for article in queryset:
            primary_key = [('user_id', user.id), ('message_id', article.id)]
            rows_to_get.append(primary_key)
        request = BatchGetRowRequest()
        table_name = "user_message_log_table"

        cond = CompositeColumnCondition(LogicalOperator.OR)
        cond.add_sub_condition(SingleColumnCondition("is_read", True, ComparatorType.EQUAL))
        cond.add_sub_condition(SingleColumnCondition("is_like", True, ComparatorType.EQUAL))

        request.add(TableInBatchGetRowItem(table_name, rows_to_get, columns_to_get,column_filter=cond, max_version=1))
        result = self.client.batch_get_row(request)
        table_result = result.get_result_by_table(table_name)
        push_id_list = []
        for item in table_result:
            if item.row is not None:
                push_id_list.append(item.row.primary_key[1][1])

        return queryset.exclude(id__in=push_id_list)

    def push_log(self, user_id, article_id_list):
        """推送文章给用户的记录"""
        table_name = "user_message_log_table"

        put_row_items = []

        for i in article_id_list:
            # 主键列
            primary_key = [
                ('user_id', user_id),  # 用户ID
                ("message_id", i),  # 文章ID
            ]

            attribute_columns = [('is_push', True), ('is_read', False), ('is_like', False), ('is_reward',False), ('is_comment',False)]

            row = Row(primary_key, attribute_columns)
            condition = Condition(RowExistenceExpectation.IGNORE)
            item = PutRowItem(row, condition)
            put_row_items.append(item)

        request = BatchWriteRowRequest()
        request.add(TableInBatchWriteRowItem(table_name, put_row_items))
        result = self.client.batch_write_row(request)
        return result.is_all_succeed()

```



在`user_message_log_table`表中, 用户和文章之间的关联形成一张巨型的矩阵.

|       | 文章1 | 文章2 | 文章3 | 文章4 |      |
| ----- | ----- | ----- | ----- | ----- | ---- |
| 用户1 | 1     | 0     | 1     | 0     |      |
| 用户2 | 1     | 1     | 0     | 0     |      |
| 用户3 | 0     | 1     | 1     | 1     |      |
| 用户4 | 1     | 1     | 0     | 0     |      |
| 用户5 | 1     | 0     | 0     | 0     |      |

现在推荐用户5去看文章: 文章2, 文章3

上面就是 根据用户行为进行分析的基于物品的协同过滤 ItemCF

```python
pip install numpy

```

```python
from django.shortcuts import render

# Create your views here.
from rest_framework.generics import ListAPIView
from article.models import Article
from .serializers import ArticleListModelSerializer
from .paginations import HomeArticlePageNumberPagination
from tablestore import *
from django.conf import settings
from users.models import User
from datetime import datetime
from renranapi.utils.ItemCF import ItemCF
class ArticleListAPIView(ListAPIView):
    serializer_class = ArticleListModelSerializer
    pagination_class = HomeArticlePageNumberPagination

    @property
    def client(self):
        return OTSClient(settings.OTS_ENDPOINT, settings.OTS_ID, settings.OTS_SECRET, settings.OTS_INSTANCE)

    def get_queryset(self):
        week_timestamp = datetime.now().timestamp() - 7 * 86400
        week_date = datetime.fromtimestamp(week_timestamp) # 获取一周前的时间对象
        queryset = Article.objects.filter(pub_date__gte=week_date).exclude(pub_date=None,).order_by("-reward_count","-comment_count","-like_count","-id")

        # 记录本次给用户推送文章的记录
        user = self.request.user

        if isinstance(user, User):
            queryset1 = []
            # 查询当前用户关注的作者是否有新的推送[查询同步库]
            # 先到未读池中提取当前访问用户上一次读取Feed的最大主键
            start_sequence_id = self.get_start_sequence_id(user.id)
            # 然后根据主键到同步库中查看数据
            message_id_list = self.get_feed_message(user.id, start_sequence_id)
            if len(message_id_list) >= 10: # 同步库中有数据
                queryset = queryset.filter(id__in=message_id_list)
                limit_num = 0
            else:
                # 基于物品进行协同过滤推荐文章
                message_id_list = self.get_message_by_itemCF(user.id)
                if len(message_id_list) > 0:
                    queryset1 = queryset.filter(id__in=message_id_list)
                    queryset1 = list(queryset1)

            # 判断tablestore中是否曾经推送过当前当前文章给用户
            queryset2 = self.check_user_message_log(user, queryset)
            queryset2 = list(queryset2)
            queryset = queryset1 + queryset2
            queryset = list(set(queryset))[:10]
            # 更新推送日志
            if len(queryset)>0:
                article_id_list = []
                for item in queryset:
                    article_id_list.append(item.id)
                self.push_log(user.id, article_id_list)

        return queryset

    def get_user_read_history(self,user_id):
        """查找用户最近１个月的浏览历史记录"""
        table_name = "user_message_log_table"
        # 范围查询的起始主键
        inclusive_start_primary_key = [
            ('user_id', user_id),
            ('message_id', INF_MIN)
        ]

        # 范围查询的结束主键
        exclusive_end_primary_key = [
            ('user_id', user_id),
            ('message_id', INF_MAX)
        ]

        # 查询所有列
        columns_to_get = [] # 表示返回所有列

        # 设置多条件
        cond = CompositeColumnCondition(LogicalOperator.AND) # 逻辑条件

        # 多条件下的子条件
        cond.add_sub_condition(SingleColumnCondition("is_push", True, ComparatorType.EQUAL)) #  比较运算符:　等于
        cond.add_sub_condition(SingleColumnCondition("is_read", True, ComparatorType.EQUAL)) #  比较运算符:　等于

        consumed, next_start_primary_key, row_list, next_token = self.client.get_range(
            table_name, # 操作表明
            Direction.FORWARD, # 范围的方向，字符串格式，取值包括'FORWARD'和'BACKWARD'。
            inclusive_start_primary_key, exclusive_end_primary_key, # 取值范围
            columns_to_get,     # 返回字段列
            column_filter=cond, # 条件
            max_version=1       # 返回版本数量
        )

        data = []
        if len(row_list) == 0:
            return data

        for row in row_list:
            data.append(row.primary_key[1][1])
        return data

    def get_user_by_message_id(self,message_id):
        """根据文章ID获取用户列表"""
        table_name = "user_message_log_table"
        # 范围查询的起始主键
        inclusive_start_primary_key = [
            ('user_id', INF_MIN),
            ('message_id', message_id)
        ]

        # 范围查询的结束主键
        exclusive_end_primary_key = [
            ('user_id', INF_MAX),
            ('message_id', message_id)
        ]

        # 查询所有列
        columns_to_get = []  # 表示返回所有列

        # 设置多条件
        cond = CompositeColumnCondition(LogicalOperator.AND)  # 逻辑条件

        # 多条件下的子条件
        cond.add_sub_condition(SingleColumnCondition("is_push", True, ComparatorType.EQUAL))  # 比较运算符:　等于
        cond.add_sub_condition(SingleColumnCondition("is_read", True, ComparatorType.EQUAL))  # 比较运算符:　等于

        consumed, next_start_primary_key, row_list, next_token = self.client.get_range(
            table_name, # 操作表明
            Direction.FORWARD, # 范围的方向，字符串格式，取值包括'FORWARD'和'BACKWARD'。
            inclusive_start_primary_key, exclusive_end_primary_key, # 取值范围
            columns_to_get,     # 返回字段列
            column_filter=cond, # 条件
            max_version=1       # 返回版本数量
        )

        data = set()
        if len(row_list) == 0:
            return list(data)

        for row in row_list:
            data.add(row.primary_key[0][1])
        return list(data)

    def find_user_by_history(self, message_list):
        """根据阅读文章列表的记录查询出阅读这些文章的用户"""
        users =[]
        for message_id in message_list:
            users += self.get_user_by_message_id(message_id)
        users = list(set(users))
        return users

    def find_message_by_user(self, users):
        allUserItemsStarList=[]  # 用户和文章之间的矩阵关系
        messages = []  # 文章列表
        users_message = [] # 所有用户读取过的文章列表
        for user_id in users:
            ret = self.get_user_read_history(user_id)
            messages += ret
            users_message.append(ret)
        messages = list(set(messages))
        for item_user_message in users_message:
            message_list = []
            for msg in messages:
                message_list.append( 1 if(msg in item_user_message) else 0)
            allUserItemsStarList.append(message_list)

        return allUserItemsStarList, messages

    def get_message_by_itemCF(self, user_id):
        """基于物品的协同过滤获取Feed内容"""
        # 查询出当前用户的曾经浏览记录
        current_user_history_list = self.get_user_read_history(user_id)

        if len(current_user_history_list) == 0:
            return []

        # 根据浏览历史记录查到曾经浏览过文章的所有用户
        users = self.find_user_by_history(current_user_history_list)
        if len(users) == 0:
            return []

        # 用户和物品的关系[点赞,赞赏,阅读,收藏], 文章id列表
        allUserItemsStarList, messages = self.find_message_by_user(users)
        if len(allUserItemsStarList) < 1 or len(messages) < 1:
            return []

        cf = ItemCF(users, messages, allUserItemsStarList)

        return cf.calrecommendMoive(user_id)

    def get_feed_message(self,user_id, start_sequence_id):
        """获取同步库中的数据"""
        table_name = "user_message_table"

        # 范围查询的起始主键
        inclusive_start_primary_key = [
            ('user_id', user_id),
            ('sequence_id', start_sequence_id),
            ('sender_id', INF_MIN),
            ('message_id', INF_MIN)
        ]

        # 范围查询的结束主键
        exclusive_end_primary_key = [
            ('user_id', user_id),
            ('sequence_id', INF_MAX),
            ('sender_id', INF_MAX),
            ('message_id', INF_MAX),
        ]

        columns_to_get = [] # 表示返回所有列
        limit = 10

        consumed, next_start_primary_key, row_list, next_token = self.client.get_range(
            table_name, # 操作表明
            Direction.FORWARD, # 范围的方向，字符串格式，取值包括'FORWARD'和'BACKWARD'。
            inclusive_start_primary_key, exclusive_end_primary_key, # 取值范围
            columns_to_get, # 返回字段列
            limit, #　结果数量
            max_version=1         # 返回版本数量
        )

        message_id_list = []
        for item in row_list:
            message_id_list.append( item.primary_key[3][1] )

        # 下一次读取同步库的开始主键
        try:
            self.set_start_sequence_id(user_id, start_sequence_id, next_start_primary_key[1][1])
        except:
            pass

        return message_id_list

    def set_start_sequence_id(self, user_id,old_start_sequence_id, next_start_primary_key):
        table_name = "user_message_session_table"
        try:
            primary_key = [('user_id', user_id), ('last_sequence_id', old_start_sequence_id)]
            row = Row(primary_key)
            consumed, return_row = self.client.delete_row(table_name, row, None)
        except:
            pass

        primary_key = [('user_id', user_id), ('last_sequence_id', next_start_primary_key)]
        attribute_columns = []
        row = Row(primary_key, attribute_columns)
        consumed, return_row = self.client.put_row(table_name, row)
        print(return_row)
        return return_row

    def get_start_sequence_id(self,user_id):
        """获取最后读取的Feed流id"""
        table_name = "user_message_session_table"

        # 范围查询的起始主键
        inclusive_start_primary_key = [
            ('user_id', user_id),
            ('last_sequence_id', INF_MIN)
        ]

        # 范围查询的结束主键
        exclusive_end_primary_key = [
            ('user_id', user_id),
            ('last_sequence_id', INF_MAX)
        ]

        columns_to_get = [] # 表示返回所有列
        limit = 1

        consumed, next_start_primary_key, row_list, next_token = self.client.get_range(
            table_name, # 操作表明
            Direction.FORWARD, # 范围的方向，字符串格式，取值包括'FORWARD'和'BACKWARD'。
            inclusive_start_primary_key, exclusive_end_primary_key, # 取值范围
            columns_to_get, # 返回字段列
            limit, #　结果数量
            # column_filter=cond, # 条件
            max_version=1         # 返回版本数量
        )

        if len(row_list) < 1:
            # 之前没有读取过推送内容
            return INF_MIN
        else:
            return row_list[0].primary_key[1][1]


    def check_user_message_log(self, user, queryset):
        """判断系统是否曾经推送过文章给用户"""
        columns_to_get = []
        rows_to_get = []
        for article in queryset:
            primary_key = [('user_id', user.id), ('message_id', article.id)]
            rows_to_get.append(primary_key)
        request = BatchGetRowRequest()
        table_name = "user_message_log_table"

        cond = CompositeColumnCondition(LogicalOperator.OR)
        cond.add_sub_condition(SingleColumnCondition("is_read", True, ComparatorType.EQUAL))
        cond.add_sub_condition(SingleColumnCondition("is_like", True, ComparatorType.EQUAL))

        request.add(TableInBatchGetRowItem(table_name, rows_to_get, columns_to_get,column_filter=cond, max_version=1))
        result = self.client.batch_get_row(request)
        table_result = result.get_result_by_table(table_name)
        push_id_list = []
        for item in table_result:
            if item.row is not None:
                push_id_list.append(item.row.primary_key[1][1])

        return queryset.exclude(id__in=push_id_list)

    def push_log(self, user_id, article_id_list):
        """推送文章给用户的记录"""
        table_name = "user_message_log_table"

        put_row_items = []

        for i in article_id_list:
            # 主键列
            primary_key = [
                ('user_id', user_id),  # 用户ID
                ("message_id", i),  # 文章ID
            ]

            attribute_columns = [('is_push', True), ('is_read', False), ('is_like', False), ('is_reward',False), ('is_comment',False)]

            row = Row(primary_key, attribute_columns)
            condition = Condition(RowExistenceExpectation.IGNORE)
            item = PutRowItem(row, condition)
            put_row_items.append(item)

        request = BatchWriteRowRequest()
        request.add(TableInBatchWriteRowItem(table_name, put_row_items))
        result = self.client.batch_write_row(request)
        return result.is_all_succeed()
```

