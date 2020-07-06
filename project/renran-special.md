## 荏苒资讯专题部分开发

对于用户个人，可以通过文集来收集分类整理文章。对于整个网站，作者可以讲文章发布到不同的专题。如果作者是专题管理员，那么可以直接发布；如果作者不是管理员，则需要审核通过后才能发布到专题中。

用户发布文章后，将会自动跳转至专题投稿页面。

### 数据准备

在 `models.py` 中创建文章投稿记录的模型，代码：

```python
class ArticlePost(BaseModel):
    """文章的投稿记录"""
    POST_STATUS = (
        (0,"审核中"),
        (1,"审核通过"),
        (2,"审核未通过"),
    )
    user = models.ForeignKey(User, on_delete=models.DO_NOTHING, verbose_name="投稿人")
    special = models.ForeignKey(Special, on_delete=models.CASCADE, verbose_name="专题")
    article = models.ForeignKey(Article, on_delete=models.CASCADE, verbose_name="文章")
    status = models.IntegerField(choices=POST_STATUS, default=0, verbose_name="审核状态")
    manager = models.IntegerField(default=None, null=True,blank=True, verbose_name="管理员")
    post_time = models.DateTimeField(default=None, null=True,blank=True, verbose_name="审核时间")

    class Meta:
        db_table = "rr_article_post"
        verbose_name = "文章的投稿记录"
        verbose_name_plural = verbose_name
```

数据迁移

```bash
python manage.py makemigrations
python manage.py migrate
```

把专题相关模型注册到 adminx.py 文件。

终端进入 MySQL 数据库，添加专题的测试数据：

```mysql
# 专题测试数据
INSERT INTO renran.rr_special (id, orders, is_show, is_delete, created_time, updated_time, name, image, notice, article_count, follow_count, collect_count, user_id) VALUES (1, 0, 1, 0, '2020-01-15 08:27:39.044094', '2020-01-15 08:27:39.044126', '相亲', 'group1/M00/00/00/wKjDgF4ezPuAO9nlAAB7habsNjs0280196', '相亲相爱', 0, 0, 0, 1);
INSERT INTO renran.rr_special (id, orders, is_show, is_delete, created_time, updated_time, name, image, notice, article_count, follow_count, collect_count, user_id) VALUES (2, 1, 1, 0, '2020-01-15 08:29:15.513292', '2020-01-15 08:29:15.513346', '相爱', 'group1/M00/00/00/wKjDgF4ezVuADywFAAB7habsNjs8789529', '相亲相爱', 0, 0, 0, 1);
INSERT INTO renran.rr_special (id, orders, is_show, is_delete, created_time, updated_time, name, image, notice, article_count, follow_count, collect_count, user_id) VALUES (3, 2, 1, 0, '2020-01-15 08:29:15.513292', '2020-01-15 08:29:15.513346', '篮球', 'group1/M00/00/00/wKjDgF4ezVuADywFAAB7habsNjs8789529', '篮球', null, null, null, 1);


# 专题管理员
INSERT INTO renran.rr_special_manager (id, orders, is_show, is_delete, created_time, updated_time, special_id, user_id) VALUES (1, 0, 1, 0, '2020-01-15 08:35:28.645931', '2020-01-15 08:35:28.645962', 1, 1);
INSERT INTO renran.rr_special_manager (id, orders, is_show, is_delete, created_time, updated_time, special_id, user_id) VALUES (2, 0, 1, 0, '2020-01-15 08:35:33.216625', '2020-01-15 08:35:33.216656', 2, 1);
INSERT INTO renran.rr_special_manager (id, orders, is_show, is_delete, created_time, updated_time, special_id, user_id) VALUES (3, 0, 1, 0, '2020-01-15 08:35:37.345065', '2020-01-15 08:35:37.345094', 3, 1);
```

### 专题收录文章页面

服务端提供我管理的专题列表，视图接口代码：

```python
class MySpecialListAPIView(ListAPIView):
    permission_classes = [IsAuthenticated]
    serializer_class = serializers.SpecialModelSerializer
    def get_queryset(self):
        user = self.request.user
        special_manager_list = models.SpecialManager.objects.filter(user=user)
        special_id_list = [special_manager.special.id for special_manager in special_manager_list]
        return models.Special.objects.filter(id__in=special_id_list)
```

序列化器,代码:

```python
class SpecialModelSerializer(serializers.ModelSerializer):
    """获取我管理的专题列表"""
    class Meta:
        model = models.Special
        fields = ['id', 'name', 'image']
        read_only_fields = ['id']
```

路由,代码:

```python
urlpatterns = [
    ...
    path('special/list/', views.MySpecialListAPIView.as_view()),
]
```

修改发布文章以后的跳转页面代码,并修改路由中原来的Writed的路由地址。客户顿实现发布文章以后的投稿页面

```vue
<template>
  <div id="writed">
    <div class="_3PLTX">
      <div class="_2Mx1k">
        <div class="_3CoyQ">
          <router-link :to="`/article/${article_id}`" class="_2-ZiM">{{article_name}}</router-link>
          <br>
          <router-link :to="`/article/${article_id}`" class="_2ajaT">发布成功，点击查看文章</router-link>
        </div>
        <ul class="zelSB">
          <li class="_35P4c"><i class="fa fa-weibo"></i>微博</li>
          <li class="_1c1Eo"><i class="fa fa-wechat"></i>微信</li>
          <li class="_1HWk1"><i class="fa fa-link"></i>复制链接</li>
          <li class="_3FrjS _3Qjz6 _2_WAp _2po2r cRfUr" title="">
            <span class="">更多分享</span>
          </li>
        </ul>
      </div>
      <div class="_3Q2EW" @click="$router.back()">×</div>
      <div class="_1s967"></div>
      <div class="_2w_E5">
        <div class="_23R_O">
          <div class="_1WCUC">
            <i class="fa fa-search fa-2x"></i><input type="text" placeholder="搜索专题">
          </div>
          <div class="_3dZoJ">向专题投稿，让文章被更多人发现</div>
        </div>
        <div>
          <div class="_18jMg">
            <h3>我管理的专题<a href="">新建</a></h3>
            <ul class="_1hEmG">
              <li v-for="special in my_special_list">
                <a>收录</a>
                <img alt="png" :src="special.image">
                <span class="_1CN24" :title="special.name">{{special.name}}</span>
              </li>
            </ul>
          </div>
          <div class="_18jMg">
            <h3>最近投稿</h3>
            <ul class="_1hEmG">
              <li>
                <a data-collection-id="83" data-post-text="投稿" data-posting-text="投稿中" data-posted-text="等待审核">投稿</a>
                <img alt="png" src="https://upload.jianshu.io/collections/images/83/1.jpg">
                <span class="_1CN24" title="摄影">摄影</span>
              </li>
            </ul>
          </div>
          <div class="_18jMg">
            <h3>推荐专题</h3>
            <div>
              <ul class="_1hEmG">
                <li class="_3GDE6">
                  <img alt="png" src="https://upload.jianshu.io/collections/images/83/1.jpg">
                  <span class="_1CN24">摄影<em>154.8K篇文章，2575.1K人关注</em></span>
                  <a data-collection-id="83" data-post-text="投稿" data-posting-text="投稿中" data-posted-text="等待审核">投稿</a>
                </li>
                <li class="_3GDE6">
                  <img alt="png" src="https://upload.jianshu.io/collections/images/95/1.jpg">
                  <span class="_1CN24">故事<em>192.2K篇文章，1920.7K人关注</em></span>
                  <a data-collection-id="95" data-post-text="投稿" data-posting-text="投稿中" data-posted-text="等待审核">投稿</a>
                </li>
              </ul>
              <div class="_27pBl">没有更多了</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
    export default {
        name: "PostArticle",
        data() {
            return {
                my_special_list: [],
                token: "",
                article_id: 0,
                article_name: "",
            }
        },
        created() {
            this.token = this.$settings.check_login(this);
            // 从本地存储中提取当前文章的ID和标题
            this.article_id = sessionStorage.article_id;
            this.article_name = sessionStorage.article_name;
            if (!this.article_id || !this.article_name) {
                this.$router.push('/');
            }
            // 这两个信息用完即删，避免后面刷新了还能显示
            sessionStorage.removeItem('article_id');
            sessionStorage.removeItem('article_name');
        },
        methods: {
            
        }
    }
</script>
<style scoped>
  ::selection {
    color: #fff;
    background: #1890ff;
  }

  .writed {
    width: 100%;
    height: 100%;
    max-width: 100vw;
    max-height: 100vh;
    color: rgba(0, 0, 0, .65);
    font-size: 14px;
    font-variant: tabular-nums;
    line-height: 1.5;
  }

  .writed * {
    box-sizing: border-box;
  }

  ._3PLTX {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: #fff;
    z-index: 1010;
    overflow-y: auto;
  }

  ._3PLTX ._2Mx1k {
    background-color: #f2f2f2;
    padding-bottom: 110px;
  }

  ._3PLTX ._2Mx1k ._3CoyQ {
    padding: 80px 0 40px;
    text-align: center;
  }

  ._3PLTX ._2Mx1k ._3CoyQ ._2-ZiM {
    display: inline-block;
    height: 40px;
    font-size: 28px;
    font-weight: 500;
    color: #333;
    margin-bottom: 24px;
  }

  ._3PLTX ._2Mx1k ._3CoyQ ._2ajaT {
    font-size: 16px;
    font-weight: 600;
    color: #42c02e;
  }

  ._3PLTX ._2Mx1k ._3CoyQ ._2ajaT:before {
    content: "";
    display: inline-block;
    width: 18px;
    height: 10px;
    border: 3px solid #42c02e;
    border-width: 0 0 4px 4px;
    transform: rotate(-45deg);
    transition: 0s;
    position: relative;
    bottom: 4px;
    right: 8px;
  }

  ._3PLTX ._2Mx1k .zelSB {
    text-align: center;
    color: #fff;
    font-size: 14px;
  }

  ._3PLTX ._2Mx1k .zelSB > li {
    display: inline-block;
    width: 124px;
    line-height: 38px;
    border-radius: 100px;
    margin: 0 15px;
    cursor: pointer;
    padding: 0;
    text-align: center;
  }

  ._3PLTX ._2Mx1k .zelSB li._35P4c {
    background-color: #e05244;
  }

  ._3PLTX ._2Mx1k .zelSB > li i {
    margin-right: 5px;
  }

  ._3PLTX ._2Mx1k .zelSB li._1c1Eo {
    background-color: #07b225;
  }

  ._3PLTX ._2Mx1k .zelSB li._1HWk1 {
    background-color: #3194d0;
  }

  ._2po2r {
    padding: 10px 20px;
    line-height: 20px;
    white-space: nowrap;
    text-align: left;
    position: relative;
    border-bottom: 1px solid #d9d9d9;
  }

  .cRfUr {
    border-bottom: 1px solid #d9d9d9;
  }

  ._2po2r:last-child {
    border-radius: 0 0 4px 4px;
    border-bottom: 0;
  }

  ._3PLTX ._2Mx1k .zelSB > li {
    display: inline-block;
    width: 124px;
    line-height: 38px;
    border-radius: 100px;
    margin: 0 15px;
    cursor: pointer;
    padding: 0;
    text-align: center;
  }

  ._3PLTX ._2Mx1k .zelSB li._3Qjz6 {
    background-color: #a7a7a7;
    position: relative;
  }

  ._3PLTX ._3Q2EW {
    position: fixed;
    top: 50px;
    right: 100px;
    font-size: 30px;
    font-weight: 700;
    padding: 5px;
    cursor: pointer;
  }

  ._3PLTX ._1s967 {
    height: 40px;
    border-radius: 50% 50% 0 0/100% 100% 0 0;
    background-color: #fff;
    margin-top: -40px;
  }

  ._3PLTX ._2w_E5 {
    margin: 40px auto 0;
    width: 700px;
    font-size: 14px;
  }

  ._3PLTX ._2w_E5 ._23R_O {
    margin-bottom: 36px;
  }

  ._3PLTX ._2w_E5 ._23R_O ._1WCUC {
    float: right;
    border: 1px solid #d9d9d9;
    position: relative;
    width: 200px;
    height: 34px;
    border-radius: 17px;
    padding: 5px 20px 5px 30px;
  }

  ._3PLTX ._2w_E5 ._23R_O ._1WCUC i {
    position: absolute;
    left: 10px;
    top: 50%;
    margin-top: -8px;
    font-size: 16px;
    color: #ccc;
  }

  ._3PLTX ._2w_E5 ._23R_O ._1WCUC input {
    line-height: 24px;
    height: 24px;
    width: 100%;
    font-size: 14px;
    background-color: transparent;
  }

  ._3PLTX ._2w_E5 ._23R_O ._3dZoJ {
    font-size: 16px;
    font-weight: 500;
    line-height: 38px;
  }

  ._3PLTX ._2w_E5 ._18jMg {
    position: relative;
    margin-bottom: 50px;
  }

  ._3PLTX ._2w_E5 ._18jMg h3 {
    margin-bottom: 0;
    height: 40px;
    line-height: 40px;
    padding: 0 6px 0 14px;
    background-color: #f2f2f2;
    font-size: 14px;
  }

  ._3PLTX ._2w_E5 a {
    color: #42c02e;
  }

  ._3PLTX ._2w_E5 ._18jMg h3 a {
    margin-left: 15px;
  }

  ._3PLTX ._2w_E5 ._18jMg ._1hEmG {
    position: relative;
    margin: 1px 0 0 1px;
    zoom: 1;
    min-height: 72px;
  }

  ._3PLTX ._2w_E5 ._18jMg ._1hEmG:after, ._3PLTX ._2w_E5 ._18jMg ._1hEmG:before {
    content: " ";
    display: table;
  }

  ._3PLTX ._2w_E5 ._18jMg ._1hEmG li {
    float: left;
    width: 234px;
    border: 1px solid #f2f2f2;
    position: relative;
    margin: -1px 0 0 -1px;
    list-style-type: none;
  }

  ._3PLTX ._2w_E5 a {
    color: #42c02e;
  }

  ._3PLTX ._2w_E5 ._18jMg ._1hEmG li a {
    position: absolute;
    top: 24px;
    right: 15px;
  }

  img {
    vertical-align: middle;
    border-style: none;
  }

  ._3PLTX ._2w_E5 ._18jMg ._1hEmG li img {
    position: absolute;
    left: 15px;
    height: 40px;
    width: 40px;
    top: 15px;
    border-radius: 10%;
  }

  ._3PLTX ._2w_E5 ._18jMg ._1hEmG li ._1CN24 {
    display: block;
    font-weight: 700;
    color: #595959;
    width: 100%;
    padding: 0 60px 0 75px;
    line-height: 70px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  ._3PLTX ._2w_E5 ._23R_O ._1WCUC input {
    line-height: 24px;
    height: 24px;
    width: 100%;
    font-size: 14px;
    background-color: transparent;
    outline: none;
    border: 0;
  }

  ._3PLTX ._2w_E5 ._23R_O ._1WCUC i {
    position: absolute;
    left: 10px;
    top: 50%;
    margin-top: -8px;
    font-size: 16px;
    color: #ccc;
  }

  ._3PLTX ._2w_E5 ._23R_O ._1WCUC {
    float: right;
    border: 1px solid #d9d9d9;
    position: relative;
    width: 200px;
    height: 34px;
    border-radius: 17px;
    padding: 5px 20px 5px 30px;
  }

  ._3PLTX ._2w_E5 ._18jMg ._1hEmG ._3GDE6 {
    width: 49.85666666%;
  }

  ._3PLTX ._2w_E5 ._18jMg ._1hEmG li img {
    position: absolute;
    left: 15px;
    height: 40px;
    width: 40px;
    top: 15px;
    border-radius: 10%;
  }

  img {
    vertical-align: middle;
    border-style: none;
  }

  ._3PLTX ._2w_E5 ._18jMg ._1hEmG li ._1CN24 {
    display: block;
    font-weight: 700;
    color: #595959;
    width: 100%;
    padding: 0 60px 0 75px;
    line-height: 70px;
    overflow: hidden;
    -o-text-overflow: ellipsis;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  ._1hEmG a {
    cursor: pointer;
  }

  ._3PLTX ._2w_E5 ._18jMg ._1hEmG ._3GDE6 ._1CN24 {
    display: block;
    padding: 18px 65px 16px;
    line-height: 1;
  }

  ._3PLTX ._2w_E5 ._18jMg ._1hEmG ._3GDE6 ._1CN24 em {
    font-weight: 400;
    font-style: normal;
    color: #999;
    display: block;
    margin-top: 8px;
    font-size: 12px;
  }

  ._3PLTX ._2w_E5 a {
    color: #42c02e;
  }

  ._3PLTX ._2w_E5 ._18jMg ._1hEmG li a {
    position: absolute;
    top: 24px;
    right: 15px;
  }

  ._3PLTX ._2w_E5 ._18jMg ._1hEmG:after {
    clear: both;
    visibility: hidden;
    font-size: 0;
    height: 0;
  }

  ._3PLTX ._27pBl {
    padding: 30px 0;
    text-align: center;
  }

  ._3PLTX ._2w_E5 ._18jMg ._1hEmG ._3GDE6 ._1CN24 {
    display: block;
    padding: 18px 65px 16px;
    line-height: 1;
  }
</style>
```

前端指定路由，代码：

```javascript
import Vue from 'vue'

import PostArticle from "@/components/PostArticle";
Vue.use(Router);

export default new Router({
  mode: "history",
  routes: [
    ,{
      path: "/post",
      name: 'PostArticle',
      component: PostArticle,
    },
  ]
})
```

### 实现专题收录文章的功能

修改发布文章以后的跳转页面代码,并修改路由中原来的 Writed 的路由地址

`Write.vue` 组件中发布文章以后跳转到投稿页面，代码：

```javascript
publish_article(is_public) {
    if (!this.article_name) {
        this.$message.error('文章标题不能为空！');
        return
    }
    this.$axios.put(`${this.$settings.Host}/article/article/publish/`, {
        'article_id': this.article_list[this.article_key].id,
        'is_public': is_public,
    }, {
        headers: {
            Authorization: `jwt ${this.token}`
        }
    }).then(response => {
        this.article_list[this.article_key].is_public = is_public;
        if (is_public) {
            this.jump_to_writed();
        } else {
            this.$message.success('成功设为私密！')
        }
    }).catch(errors => {
        this.$message.error('发布失败，请稍后再试！')
    })
},
move_article(key) {
    this.$axios.put(`${this.$settings.Host}/article/article/move/`, {
        'collection_id': this.collections[key].id,
        'article_id': this.article_list[this.article_key].id,
    }, {
        headers: {
            Authorization: `jwt ${this.token}`
        }
    }).then(response => {
        this.article_list.splice(this.article_key, 1);
        this.article_key = 0;
        this.$message.success('文章移动成功！');
        this.show_editor = !!this.article_list.length;
    }).catch(errors => {
        this.$message.error('文章移动失败，请稍后重试！')
    })
},
    schedule_publish_article() {
        if (!this.article_name) {
            this.$message.error('文章标题不能为空！');
            return
        }
        this.$axios.put(`${this.$settings.Host}/article/schedule/publish/`, {
            pub_date: this.schedule_datetime,
            article_id: this.article_list[this.article_key].id,
        }, {
            headers: {
                Authorization: `jwt ${this.token}`
            }
        }).then(response=>{
            this.jump_to_writed()
        }).catch(errors=>{
            this.$message.error('定时发送失败，请稍后重试！')
        });
        this.time_dialog_visible = false
    },
        jump_to_writed () {
            sessionStorage.setItem('article_id', this.article_list[this.article_key].id);
            sessionStorage.setItem('article_name', this.article_list[this.article_key].name);
            this.$router.push('/writed');
        },
```

`router/index.js` 代码：

```javascript
import Vue from 'vue'
import Router from 'vue-router'

import PostArticle from "@/components/PostArticle";
Vue.use(Router);

export default new Router({
  mode: "history",
  routes: [
    // ...
    {
      path: '/writed',
      name: 'Writed',
      component: Writed,
    },
  ]
})
```

服务端提供收录文章到我管理的专题中的视图接口：

```python
class ArticlePostAPIView(CreateAPIView):
    permission_classes = [IsAuthenticated]
    serializer_class = serializers.ArticlePostModelSerializer
    queryset = models.ArticlePost
```

序列化器：

```python
class ArticlePostModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = models.ArticlePost
        fields = ['special', 'article', 'status']
        read_only_fields = ['status']

    def create(self, validated_data):
        """需要分两种情况考虑：自己是管理员和不是管理员，还要判断文章是否在审核中"""
        user = self.context['request'].user
        article = validated_data.get('article')
        special = validated_data.get('special')
        manager = models.SpecialManager.objects.get(special=special).user
        # 首先检查是否已经投稿过，且在审核中或审核已经通过，如果是，不需要创建新的数据
        try:
            article_post = self.Meta.model.objects.get(article=article,
                                                       special=special,
                                                       status__in=[0, 1])
            return article_post
        except self.Meta.model.DoesNotExist:
            pass
        # 使用父类的保存数据方法，将除了审核状态之外的数据保存，并接收返回回来的数据对象
        validated_data['user'] = user
        validated_data['post_time'] = timezone.now()
        validated_data['manager'] = manager.id
        article_post = super().create(validated_data)
        # 然后检查用户是否是管理员，如果是，直接审核通过，否则设置为审核中状态
        if manager.id == user.id:
            article_post.status = 1
            models.SpecialArticle.objects.create(
                special=special,
                article=article,
                orders=0,
            )
        else:
            article_post.status = 0
        article_post.save()
        
        return article_post
```

`urls.py` 路由：

```python
urlpatterns = [
    ...
    path('post/', views.ArticlePostAPIView.as_view()),
]
```

前端发送 ajax，请求投稿，代码：

```javascript
article_post (key) {
    this.$axios.post(`${this.$settings.Host}/article/post/`, {
        article: this.article_id,
        special: this.my_special_list[key].id,
    }, {
        headers: {
            Authorization: `jwt ${this.token}`
        }
    }).then(response=>{
        this.my_special_list[key].status = response.data.status;
    }).catch(errors=>{
        this.$message.error('专题收录失败！')
    })
},
```

### 在专题列表中展示当前文章的收录状态

视图代码：

```python
class MySpecialListAPIView(ListAPIView):
    """获取我管理的专题列表"""
    permission_classes = [IsAuthenticated]
    serializer_class = serializers.SpecialModelSerializer
    def get_queryset(self):
        user = self.request.user
        special_manager_list = models.SpecialManager.objects.filter(user=user)
        special_id_list = [special_manager.special.id for special_manager in special_manager_list]
        return models.Special.objects.filter(id__in=special_id_list)

    def list(self, request, *args, **kwargs):
        queryset = self.filter_queryset(self.get_queryset())
        serializer = self.get_serializer(queryset, many=True)
        data_list = serializer.data
        article_id = self.request.query_params.get('article_id')
        for data in data_list:
            try:
                article_post = models.ArticlePost.objects.get(special_id=data.get('id'),
                                                              article_id=article_id,
                                                              status__in=[0, 1])
                data['status'] = article_post.status
            except models.ArticlePost.DoesNotExist:
                data['status'] = -1
        return Response(serializer.data)
```

前端组件中在原有代码中增加is_post的判断显示

```vue
<template>
  <div id="writed">
    <div class="_3PLTX">
      <div class="_2Mx1k">
        <div class="_3CoyQ">
          <router-link :to="`/article/${article_id}`" class="_2-ZiM">{{article_name}}</router-link>
          <br>
          <router-link :to="`/article/${article_id}`" class="_2ajaT">发布成功，点击查看文章</router-link>
        </div>
        <ul class="zelSB">
          <li class="_35P4c"><i class="fa fa-weibo"></i>微博</li>
          <li class="_1c1Eo"><i class="fa fa-wechat"></i>微信</li>
          <li class="_1HWk1"><i class="fa fa-link"></i>复制链接</li>
          <li class="_3FrjS _3Qjz6 _2_WAp _2po2r cRfUr" title="">
            <span class="">更多分享</span>
          </li>
        </ul>
      </div>
      <div class="_3Q2EW" @click="$router.back()">×</div>
      <div class="_1s967"></div>
      <div class="_2w_E5">
        <div class="_23R_O">
          <div class="_1WCUC">
            <i class="fa fa-search fa-2x"></i><input type="text" placeholder="搜索专题">
          </div>
          <div class="_3dZoJ">向专题投稿，让文章被更多人发现</div>
        </div>
        <div>
          <div class="_18jMg">
            <h3>我管理的专题<a href="">新建</a></h3>
            <ul class="_1hEmG">
              <li v-for="special,key in my_special_list" :key="key">
                <a @click="article_post(key)">{{special.status|get_status_str}}</a>
                <img alt="png" :src="special.image">
                <span class="_1CN24" :title="special.name">{{special.name}}</span>
              </li>
            </ul>
          </div>
          <div class="_18jMg">
            <h3>最近投稿</h3>
            <ul class="_1hEmG">
              <li>
                <a data-collection-id="83" data-post-text="投稿" data-posting-text="投稿中" data-posted-text="等待审核">投稿</a>
                <img alt="png" src="https://upload.jianshu.io/collections/images/83/1.jpg">
                <span class="_1CN24" title="摄影">摄影</span>
              </li>
            </ul>
          </div>
          <div class="_18jMg">
            <h3>推荐专题</h3>
            <div>
              <ul class="_1hEmG">
                <li class="_3GDE6">
                  <img alt="png" src="https://upload.jianshu.io/collections/images/83/1.jpg">
                  <span class="_1CN24">摄影<em>154.8K篇文章，2575.1K人关注</em></span>
                  <a data-collection-id="83" data-post-text="投稿" data-posting-text="投稿中" data-posted-text="等待审核">投稿</a>
                </li>
                <li class="_3GDE6">
                  <img alt="png" src="https://upload.jianshu.io/collections/images/95/1.jpg">
                  <span class="_1CN24">故事<em>192.2K篇文章，1920.7K人关注</em></span>
                  <a data-collection-id="95" data-post-text="投稿" data-posting-text="投稿中" data-posted-text="等待审核">投稿</a>
                </li>
              </ul>
              <div class="_27pBl">没有更多了</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
    export default {
        name: "PostArticle",
        data() {
            return {
                my_special_list: [],
                token: "",
                article_id: 0,
                article_name: "",
            }
        },
        filters: {
            get_status_str (status) {
                let status_obj = {
                    '-2': '投稿',
                    '-1': '收录',
                    '0': '等待审核',
                    '1': '审核通过',
                    '2': '审核未通过'
                };
                return status_obj[status]
            },
        },
        created() {
            this.token = this.$settings.check_login(this);
            // 从本地存储中提取当前文章的ID和标题
            this.article_id = sessionStorage.article_id;
            this.article_name = sessionStorage.article_name;
            if (!this.article_id || !this.article_name) {
                this.$router.push('/');
            }
            // 这两个信息用完即删，避免后面刷新了还能显示
            sessionStorage.removeItem('article_id');
            sessionStorage.removeItem('article_name');
            this.get_my_special_list();
        },
        methods: {
            get_my_special_list () {
                // 获取我管理的专题列表
                this.$axios.get(`${this.$settings.Host}/article/special/list/`, {
                    params: {
                        article_id: this.article_id,
                    },
                    headers: {
                        Authorization: "jwt " + this.token,
                    }
                }).then(response => {
                    this.my_special_list = response.data;
                }).catch(error => {
                    this.$message.error("对不起，获取专题列表失败！");
                });
            },
            article_post (key) {
                this.$axios.post(`${this.$settings.Host}/article/post/`, {
                    article: this.article_id,
                    special: this.my_special_list[key].id,
                }, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response=>{
                    this.my_special_list[key].status = response.data.status;
                }).catch(errors=>{
                    this.$message.error('专题收录失败！')
                })
            },
        }
    }
</script>
```