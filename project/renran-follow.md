## 荏苒资讯用户关注功能

### 判断访问者是否关注了作者

在文章详情页中判断当前用户是否关注了文章作者

`article/views.py` 代码：

```python
class ArticleDetailAPIView(RetrieveAPIView):
    queryset = models.Article.objects.filter(is_public=True)
    serializer_class = serializers.ArticleDetailModelSerializer

    def retrieve(self, request, *args, **kwargs):
        response = super().retrieve(request, *args, **kwargs)
        data = response.data
        user = request.user
        # 使用fllow_status标识关注状态：0未登录，1登录且关注，2登录未关注，-1当前登录用户就是作者
        if isinstance(user, User):
            # 访问者已经登陆
            if user.id == data.get('user').get('id'):
                # 如果访问者和文章作者是同一个人
                follow_status = -1
            else:
                # 判断访问者是否曾经关注过作者
                ts = TableStore()
                ret = ts.get_one(
                    'user_relation_table',
                    [('user_id', data.get('user').get('id')), ('follower_id', user.id)],
                )
                if ret:
                    # 已经登陆并且关注
                    follow_status = 1
                else:
                    # 已经登陆，但没有关注
                    follow_status = 2
        else:
            # 访问者没有登陆
            follow_status = 0
        data['follow_status'] = follow_status
        return Response(data)
```

在 renranapi 根目录中的 utils 目录下创建一个 `tablestore.py` 文件，用来存放操作表格存储的方法：

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
```

在客户端中根据返回的关注状态`is_follow`来判断显示关注按钮

```vue
<template>
  <div class="_21bLU4 _3kbg6I">
    <Header></Header>
    <div class="_3VRLsv" role="main">
      <div class="_gp-ck">
        <section class="ouvJEz">
          <h1 class="_1RuRku">{{ article_detail.name }}</h1>
          <div class="rEsl9f">
            <div class="_2mYfmT">
              <a class="_1OhGeD" href="/u/a70487cda447" rel="noopener noreferrer" target="_blank">
                <img :src="article_detail.user.avatar" :alt="article_detail.user.nickname" class="_13D2Eh"/>
              </a>
              <div style="margin-left: 8px;">
                <div class="_3U4Smb">
                  <span class="FxYr8x"><a class="_1OhGeD" href="/u/a70487cda447" rel="noopener noreferrer"
                                          target="_blank">{{ article_detail.user.nickname }}</a></span>
                  <button class="_3kba3h _1OyPqC _3Mi9q9 _34692-" data-locale="zh-CN" type="button"
                          v-if="article_detail.follow_status === 0" @click="login_handler">
                    <span>关注</span>
                  </button>
                </div>
                <div class="s-dsoj">
                  <time>{{ article_detail.updated_time|time_format }}</time>
                  <span>字数 {{ article_detail.word_count }}</span>
                  <span>阅读 {{ article_detail.read_count }}</span>
                </div>
              </div>
            </div>
          </div>
          <article class="_2rhmJa" v-html="article_detail.render">
          </article>
          <div></div>
          <div class="_1kCBjS">
            <div class="_18vaTa">
              <div class="_3BUZPB">
                <div aria-label="给文章点赞" class="_2Bo4Th" role="button" tabindex="-1">
                  <i aria-label="ic-like" class="anticon">
                    <svg aria-hidden="true" class="" fill="currentColor" focusable="false" height="1em" width="1em">
                      <use xlink:href="#ic-like"></use>
                    </svg>
                  </i>
                </div>
                <span aria-label="查看点赞列表" class="_1LOh_5" role="button" tabindex="-1">
                  {{ article_detail.like_count }}人点赞
                  <i aria-label="icon: right" class="anticon anticon-right">
           <svg aria-hidden="true" class="" data-icon="right" fill="currentColor" focusable="false" height="1em"
                viewbox="64 64 896 896" width="1em">
            <path
              d="M765.7 486.8L314.9 134.7A7.97 7.97 0 0 0 302 141v77.3c0 4.9 2.3 9.6 6.1 12.6l360 281.1-360 281.1c-3.9 3-6.1 7.7-6.1 12.6V883c0 6.7 7.7 10.4 12.9 6.3l450.8-352.1a31.96 31.96 0 0 0 0-50.4z"></path>
           </svg></i></span>
              </div>
              <div class="_3BUZPB">
                <div class="_2Bo4Th" role="button" tabindex="-1">
                  <i aria-label="ic-dislike" class="anticon">
                    <svg aria-hidden="true" class="" fill="currentColor" focusable="false" height="1em" width="1em">
                      <use xlink:href="#ic-dislike"></use>
                    </svg>
                  </i>
                </div>
              </div>
            </div>
            <div class="_18vaTa">
              <a class="_3BUZPB _1x1ok9 _1OhGeD" href="/nb/38290018" rel="noopener noreferrer" target="_blank"><i
                aria-label="ic-notebook" class="anticon">
                <svg aria-hidden="true" class="" fill="currentColor" focusable="false" height="1em" width="1em">
                  <use xlink:href="#ic-notebook"></use>
                </svg>
              </i><span>随笔</span></a>
              <div class="_3BUZPB ant-dropdown-trigger">
                <div class="_2Bo4Th">
                  <i aria-label="ic-others" class="anticon">
                    <svg aria-hidden="true" class="" fill="currentColor" focusable="false" height="1em" width="1em">
                      <use xlink:href="#ic-others"></use>
                    </svg>
                  </i>
                </div>
              </div>
            </div>
          </div>
          <div class="_19DgIp" style="margin-top:24px;margin-bottom:24px"></div>
          <div class="_13lIbp">
            <div class="_191KSt">
              &quot;小礼物走一走，来简书关注我&quot;
            </div>
            <button class="_1OyPqC _3Mi9q9 _2WY0RL _1YbC5u" type="button" @click.stop="article_reward">
              <span>赞赏支持</span>
            </button>
            <span class="_3zdmIj">还没有人赞赏，支持一下</span>
          </div>
          <div class="d0hShY">
            <a class="_1OhGeD" href="/u/a70487cda447" rel="noopener noreferrer" target="_blank">
              <img :alt="article_detail.user.nickname" class="_27NmgV" :src="article_detail.user.avatar"/>
            </a>
            <div class="Uz-vZq">
              <div class="Cqpr1X">
                <a class="HC3FFO _1OhGeD" href="/u/a70487cda447" rel="noopener noreferrer" target="_blank"
                   :title="article_detail.user.nickname">{{ article_detail.user.nickname }}</a>
                <span class="_2WEj6j" title="你读书的样子真好看。">你读书的样子真好看。</span>
              </div>
              <div class="lJvI3S">
                <span>总资产0</span>
                <span>共写了78.7W字</span>
                <span>获得6,072个赞</span>
                <span>共1,308个粉丝</span>
              </div>
            </div>
            <button class="_1OyPqC _3Mi9q9" data-locale="zh-CN" type="button"><span>关注</span></button>
          </div>
        </section>
        <div id="note-page-comment">
          <div class="lazyload-placeholder"></div>
        </div>
      </div>
      <aside class="_2OwGUo">
        <section class="_3Z3nHf">
          <div class="_3Oo-T1">
            <a class="_1OhGeD" href="/u/a70487cda447" rel="noopener noreferrer" target="_blank">
              <img alt="" class="_3T9iJQ" :src="article_detail.user.avatar"/></a>
            <div class="_32ZTTG">
              <div class="_2O0T_w">
                <div class="_2v-h3G">
                  <span class="_2vh4fr" :title="article_detail.user.nickname">
                    <a class="_1OhGeD" href="/u/a70487cda447" rel="noopener noreferrer" target="_blank">
                      {{ article_detail.user.nickname }}
                    </a>
                  </span>
                </div>
                <button class="tzrf9N _1OyPqC _3Mi9q9 _34692-" data-locale="zh-CN" type="button"><span>关注</span>
                </button>
              </div>
              <div class="_1pXc22">
                总资产0
              </div>
            </div>
          </div>
          <div class="_19DgIp"></div>
        </section>
        <div>
          <div class="">
            <section class="_3Z3nHf">
              <h3 class="QHRnq8 QxT4hD"><span>推荐阅读</span></h3>
              <div class="cuOxAY" role="listitem">
                <div class="_3L5YSq" title="这些话没人告诉你，但必须知道的社会规则">
                  <a class="_1-HJSV _1OhGeD" href="/p/a3e56a0559ff" rel="noopener noreferrer" target="_blank">这些话没人告诉你，但必须知道的社会规则</a>
                </div>
                <div class="_19haGh">
                  阅读 5,837
                </div>
              </div>
              <div class="cuOxAY" role="listitem">
                <div class="_3L5YSq" title="浙大学霸最美笔记曝光：真正的牛人，都“变态”到了极致">
                  <a class="_1-HJSV _1OhGeD" href="/p/d2a3724e2839" rel="noopener noreferrer" target="_blank">浙大学霸最美笔记曝光：真正的牛人，都“变态”到了极致</a>
                </div>
                <div class="_19haGh">
                  阅读 12,447
                </div>
              </div>
              <div class="cuOxAY" role="listitem">
                <div class="_3L5YSq" title="征服一个女人最好的方式：不是讨好她，而是懂得去折腾她">
                  <a class="_1-HJSV _1OhGeD" href="/p/f6acf67f039b" rel="noopener noreferrer" target="_blank">征服一个女人最好的方式：不是讨好她，而是懂得去折腾她</a>
                </div>
                <div class="_19haGh">
                  阅读 5,311
                </div>
              </div>
              <div class="cuOxAY" role="listitem">
                <div class="_3L5YSq" title="告别平庸的15个小方法">
                  <a class="_1-HJSV _1OhGeD" href="/p/cff7eb6b232b" rel="noopener noreferrer" target="_blank">告别平庸的15个小方法</a>
                </div>
                <div class="_19haGh">
                  阅读 7,040
                </div>
              </div>
              <div class="cuOxAY" role="listitem">
                <div class="_3L5YSq" title="轻微抑郁的人，会说这3句“口头禅”，若你一个不占，偷着乐吧">
                  <a class="_1-HJSV _1OhGeD" href="/p/2a0ca1729b4b" rel="noopener noreferrer" target="_blank">轻微抑郁的人，会说这3句“口头禅”，若你一个不占，偷着乐吧</a>
                </div>
                <div class="_19haGh">
                  阅读 16,411
                </div>
              </div>
            </section>
          </div>
        </div>
      </aside>
    </div>
    <div class="_23ISFX-body" v-if="show_reward_window" @click.stop="show_reward_window=true">
      <div class="_3uZ5OL">
        <div class="_2PLkjk">
          <img class="_2R1-48"
               src="https://upload.jianshu.io/users/upload_avatars/9602437/8fb37921-2e4f-42a7-8568-63f187c5721b.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/100/h/100/format/webp"
               alt=""/>
          <div class="_2h5tnQ">
            给作者送糖
          </div>
        </div>
        <div class="_1-bCJJ">
          <div class="LMa6S_" :class="reward_info.money===num?'_1vONvL':''" @click="reward_info.money=num"
               v-for="num in reward_list"><span>{{num}}</span></div>
        </div>
        <textarea class="_1yN79W" placeholder="给Ta留言..." v-model="reward_msg"></textarea>
        <div class="_1_B577">
          选择支付方式
        </div>
        <div class="_1-bCJJ">
          <div class="LMa6S_ _3PA8BN" :class="{'_1vONvL': reward_info.pay_type===key}"
               @click="reward_info.pay_type=key" v-for="type,key in pay_type_list" :key="key">
            <span>{{type}}</span>
          </div>
        </div>
        <button type="button" class="_3A-4KL _1OyPqC _3Mi9q9 _1YbC5u" @click="confirm_payment">
          <span>确认支付</span><span> ￥</span>{{reward_info.money}}
        </button>
      </div>
    </div>
    <Footer></Footer>
  </div>
</template>

<script>
    import Header from "./common/Header";
    import Footer from "./common/Footer";

    export default {
        name: "Article",
        components: {
            Header,
            Footer,
        },
        data() {
            return {
                article_id: 0,
                token: '',
                article_detail: {
                    user: {}
                },
                show_reward_window: false,
                reward_list: [2, 5, 10, 20, 50, 100],
                pay_type_list: ['支付宝', '账户余额'],
                reward_info: {
                    'money': 2,
                    'pay_type': 0
                },
                reward_msg: '',
            }
        },
        filters: {
            time_format(time) {
                let t = new Date(time);
                return `${t.getFullYear()}.${t.getMonth() + 1}.${t.getDate()} ${t.getHours()}:${t.getMinutes()}`;
            },
        },
        methods: {
            get_article_detail () {
                this.$axios.get(`${this.$settings.Host}/article/detail/${this.article_id}/`,{
                    headers: this.token && {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    this.article_detail = response.data;
                }).catch(errors => {
                    this.$message.error('获取文章详情信息失败！')
                })
            },
            article_reward () {
                this.$settings.check_login(this);
                this.show_reward_window=true;
            },
            confirm_payment () {
                this.$axios.post(`${this.$settings.Host}/payments/alipay/`, {
                    article_id: this.article_id,
                    pay_type: this.reward_info.pay_type,
                    money: this.reward_info.money,
                    message: this.reward_msg,
                }, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response=>{
                    location.href = response.data
                }).catch(errors=>{
                    this.$message.error('获取支付宝链接失败！')
                })
            },
            login_handler () {
                this.$router.push('/user/login/')
            },
        },
        created() {
            this.article_id = this.$route.params.pk;
            this.token = localStorage.user_token || sessionStorage.user_token;
            this.$axios.post(`${this.$settings.Host}/users/verify/`, {
                token: this.token
            }).then(response=>{
                this.get_article_detail();
            }).catch(error=>{
                this.token = null;
                localStorage.removeItem('user_token');
                sessionStorage.removeItem('user_toke');
                this.get_article_detail();
            });

        },
        mounted() {
            document.onclick = () => {
                this.show_reward_window = false;
            }
        }
    }
</script>
```

### 用户关注或取消关注文章的作者

服务端实现用户关注作者的 api 接口

`users/views.py` 代码:

```python
class FollowAPIView(APIView):
    """关注和取消关注"""
    permission_classes = [IsAuthenticated]
    def post(self, request):
        # 关注者[粉丝]
        user = request.user
        # 作者
        author_id = request.data.get('author_id')
        try:
            User.objects.get(id=author_id)
        except User.DoesNotExist:
            return Response({'error_msg': '要关注的用户不存在！'}, status=status.HTTP_400_BAD_REQUEST)
        # 针对关注状态要进行的操作[0表示取消关注, 1表示关注]
        operation = request.data.get('operation')
        ts = TableStore()
        if operation:
            """关注"""
            ret = ts.add_one(
                'user_relation_table',
                [('user_id', author_id), ('follower_id', user.id)],
                [('follow_time', datetime.now().timestamp())],
            )
        else:
            """取消关注"""
            ret = ts.del_one(
                'user_relation_table',
                [('user_id', author_id), ('follower_id', user.id)],
            )
        if ret:
            return Response({'msg': '操作成功！'})
        else:
            return Response({'error_msg': '操作失败！'}, status=status.HTTP_400_BAD_REQUEST)
```

路由代码：

```python
urlpatterns = [
    ...
    path('follow/', views.FollowAPIView.as_view()),
]
```

继续在 `renranapi/utils/tablestore.py` 中编写表格存储相关代码：

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
```

客户端发送请求，申请 `关注/取消关注`：

```vue
<template>
  <div class="_21bLU4 _3kbg6I">
    <Header></Header>
    <div class="_3VRLsv" role="main">
      <div class="_gp-ck">
        <section class="ouvJEz">
          <h1 class="_1RuRku">{{ article_detail.name }}</h1>
          <div class="rEsl9f">
            <div class="_2mYfmT">
              <a class="_1OhGeD" href="/u/a70487cda447" rel="noopener noreferrer" target="_blank">
                <img :src="article_detail.user.avatar" :alt="article_detail.user.nickname" class="_13D2Eh"/>
              </a>
              <div style="margin-left: 8px;">
                <div class="_3U4Smb">
                  <span class="FxYr8x"><a class="_1OhGeD" href="/u/a70487cda447" rel="noopener noreferrer"
                                          target="_blank">{{ article_detail.user.nickname }}</a></span>
                  <button class="_3kba3h _1OyPqC _3Mi9q9 _34692-" data-locale="zh-CN" type="button"
                          v-if="article_detail.follow_status === 0" @click="login_handler">
                    <span>关注</span>
                  </button>
                  <button class="_3kba3h _1OyPqC _3Mi9q9 _34692-" data-locale="zh-CN" type="button"
                          v-if="article_detail.follow_status === 1" @click="follow_handler(0)"
                          @mouseover="follow_text='取消关注'" @mouseleave="follow_text='已关注'">
                    <span>{{ follow_text }}</span>
                  </button>
                  <button class="_3kba3h _1OyPqC _3Mi9q9 _34692-" data-locale="zh-CN" type="button"
                          v-if="article_detail.follow_status === 2" @click="follow_handler(1)">
                    <span>关注</span>
                  </button>
                </div>
                <div class="s-dsoj">
                  <time>{{ article_detail.updated_time|time_format }}</time>
                  <span>字数 {{ article_detail.word_count }}</span>
                  <span>阅读 {{ article_detail.read_count }}</span>
                </div>
              </div>
            </div>
          </div>
          <article class="_2rhmJa" v-html="article_detail.render">
          </article>
          <div></div>
          <div class="_1kCBjS">
            <div class="_18vaTa">
              <div class="_3BUZPB">
                <div aria-label="给文章点赞" class="_2Bo4Th" role="button" tabindex="-1">
                  <i aria-label="ic-like" class="anticon">
                    <svg aria-hidden="true" class="" fill="currentColor" focusable="false" height="1em" width="1em">
                      <use xlink:href="#ic-like"></use>
                    </svg>
                  </i>
                </div>
                <span aria-label="查看点赞列表" class="_1LOh_5" role="button" tabindex="-1">
                  {{ article_detail.like_count }}人点赞
                  <i aria-label="icon: right" class="anticon anticon-right">
           <svg aria-hidden="true" class="" data-icon="right" fill="currentColor" focusable="false" height="1em"
                viewbox="64 64 896 896" width="1em">
            <path
              d="M765.7 486.8L314.9 134.7A7.97 7.97 0 0 0 302 141v77.3c0 4.9 2.3 9.6 6.1 12.6l360 281.1-360 281.1c-3.9 3-6.1 7.7-6.1 12.6V883c0 6.7 7.7 10.4 12.9 6.3l450.8-352.1a31.96 31.96 0 0 0 0-50.4z"></path>
           </svg></i></span>
              </div>
              <div class="_3BUZPB">
                <div class="_2Bo4Th" role="button" tabindex="-1">
                  <i aria-label="ic-dislike" class="anticon">
                    <svg aria-hidden="true" class="" fill="currentColor" focusable="false" height="1em" width="1em">
                      <use xlink:href="#ic-dislike"></use>
                    </svg>
                  </i>
                </div>
              </div>
            </div>
            <div class="_18vaTa">
              <a class="_3BUZPB _1x1ok9 _1OhGeD" href="/nb/38290018" rel="noopener noreferrer" target="_blank"><i
                aria-label="ic-notebook" class="anticon">
                <svg aria-hidden="true" class="" fill="currentColor" focusable="false" height="1em" width="1em">
                  <use xlink:href="#ic-notebook"></use>
                </svg>
              </i><span>随笔</span></a>
              <div class="_3BUZPB ant-dropdown-trigger">
                <div class="_2Bo4Th">
                  <i aria-label="ic-others" class="anticon">
                    <svg aria-hidden="true" class="" fill="currentColor" focusable="false" height="1em" width="1em">
                      <use xlink:href="#ic-others"></use>
                    </svg>
                  </i>
                </div>
              </div>
            </div>
          </div>
          <div class="_19DgIp" style="margin-top:24px;margin-bottom:24px"></div>
          <div class="_13lIbp">
            <div class="_191KSt">
              &quot;小礼物走一走，来简书关注我&quot;
            </div>
            <button class="_1OyPqC _3Mi9q9 _2WY0RL _1YbC5u" type="button" @click.stop="article_reward">
              <span>赞赏支持</span>
            </button>
            <span class="_3zdmIj">还没有人赞赏，支持一下</span>
          </div>
          <div class="d0hShY">
            <a class="_1OhGeD" href="/u/a70487cda447" rel="noopener noreferrer" target="_blank">
              <img :alt="article_detail.user.nickname" class="_27NmgV" :src="article_detail.user.avatar"/>
            </a>
            <div class="Uz-vZq">
              <div class="Cqpr1X">
                <a class="HC3FFO _1OhGeD" href="/u/a70487cda447" rel="noopener noreferrer" target="_blank"
                   :title="article_detail.user.nickname">{{ article_detail.user.nickname }}</a>
                <span class="_2WEj6j" title="你读书的样子真好看。">你读书的样子真好看。</span>
              </div>
              <div class="lJvI3S">
                <span>总资产0</span>
                <span>共写了78.7W字</span>
                <span>获得6,072个赞</span>
                <span>共1,308个粉丝</span>
              </div>
            </div>
<!--            <button class="_1OyPqC _3Mi9q9" data-locale="zh-CN" type="button"><span>关注</span></button>-->
            <button class="_3kba3h _1OyPqC _3Mi9q9 _34692-" data-locale="zh-CN" type="button"
                          v-if="article_detail.follow_status === 0" @click="login_handler">
                    <span>关注</span>
                  </button>
                  <button class="_3kba3h _1OyPqC _3Mi9q9 _34692-" data-locale="zh-CN" type="button"
                          v-if="article_detail.follow_status === 1" @click="follow_handler(0)"
                          @mouseover="follow_text='取消关注'" @mouseleave="follow_text='已关注'">
                    <span>{{ follow_text }}</span>
                  </button>
                  <button class="_3kba3h _1OyPqC _3Mi9q9 _34692-" data-locale="zh-CN" type="button"
                          v-if="article_detail.follow_status === 2" @click="follow_handler(1)">
                    <span>关注</span>
                  </button>
          </div>
        </section>
        <div id="note-page-comment">
          <div class="lazyload-placeholder"></div>
        </div>
      </div>
      <aside class="_2OwGUo">
        <section class="_3Z3nHf">
          <div class="_3Oo-T1">
            <a class="_1OhGeD" href="/u/a70487cda447" rel="noopener noreferrer" target="_blank">
              <img alt="" class="_3T9iJQ" :src="article_detail.user.avatar"/></a>
            <div class="_32ZTTG">
              <div class="_2O0T_w">
                <div class="_2v-h3G">
                  <span class="_2vh4fr" :title="article_detail.user.nickname">
                    <a class="_1OhGeD" href="/u/a70487cda447" rel="noopener noreferrer" target="_blank">
                      {{ article_detail.user.nickname }}
                    </a>
                  </span>
                </div>
                <button class="_3kba3h _1OyPqC _3Mi9q9 _34692-" data-locale="zh-CN" type="button"
                          v-if="article_detail.follow_status === 0" @click="login_handler">
                    <span>关注</span>
                  </button>
                  <button class="_3kba3h _1OyPqC _3Mi9q9 _34692-" data-locale="zh-CN" type="button"
                          v-if="article_detail.follow_status === 1" @click="follow_handler(0)"
                          @mouseover="follow_text='取消关注'" @mouseleave="follow_text='已关注'">
                    <span>{{ follow_text }}</span>
                  </button>
                  <button class="_3kba3h _1OyPqC _3Mi9q9 _34692-" data-locale="zh-CN" type="button"
                          v-if="article_detail.follow_status === 2" @click="follow_handler(1)">
                    <span>关注</span>
                  </button>
              </div>
              <div class="_1pXc22">
                总资产0
              </div>
            </div>
          </div>
          <div class="_19DgIp"></div>
        </section>
        <div>
          <div class="">
            <section class="_3Z3nHf">
              <h3 class="QHRnq8 QxT4hD"><span>推荐阅读</span></h3>
              <div class="cuOxAY" role="listitem">
                <div class="_3L5YSq" title="这些话没人告诉你，但必须知道的社会规则">
                  <a class="_1-HJSV _1OhGeD" href="/p/a3e56a0559ff" rel="noopener noreferrer" target="_blank">这些话没人告诉你，但必须知道的社会规则</a>
                </div>
                <div class="_19haGh">
                  阅读 5,837
                </div>
              </div>
              <div class="cuOxAY" role="listitem">
                <div class="_3L5YSq" title="浙大学霸最美笔记曝光：真正的牛人，都“变态”到了极致">
                  <a class="_1-HJSV _1OhGeD" href="/p/d2a3724e2839" rel="noopener noreferrer" target="_blank">浙大学霸最美笔记曝光：真正的牛人，都“变态”到了极致</a>
                </div>
                <div class="_19haGh">
                  阅读 12,447
                </div>
              </div>
              <div class="cuOxAY" role="listitem">
                <div class="_3L5YSq" title="征服一个女人最好的方式：不是讨好她，而是懂得去折腾她">
                  <a class="_1-HJSV _1OhGeD" href="/p/f6acf67f039b" rel="noopener noreferrer" target="_blank">征服一个女人最好的方式：不是讨好她，而是懂得去折腾她</a>
                </div>
                <div class="_19haGh">
                  阅读 5,311
                </div>
              </div>
              <div class="cuOxAY" role="listitem">
                <div class="_3L5YSq" title="告别平庸的15个小方法">
                  <a class="_1-HJSV _1OhGeD" href="/p/cff7eb6b232b" rel="noopener noreferrer" target="_blank">告别平庸的15个小方法</a>
                </div>
                <div class="_19haGh">
                  阅读 7,040
                </div>
              </div>
              <div class="cuOxAY" role="listitem">
                <div class="_3L5YSq" title="轻微抑郁的人，会说这3句“口头禅”，若你一个不占，偷着乐吧">
                  <a class="_1-HJSV _1OhGeD" href="/p/2a0ca1729b4b" rel="noopener noreferrer" target="_blank">轻微抑郁的人，会说这3句“口头禅”，若你一个不占，偷着乐吧</a>
                </div>
                <div class="_19haGh">
                  阅读 16,411
                </div>
              </div>
            </section>
          </div>
        </div>
      </aside>
    </div>
    <div class="_23ISFX-body" v-if="show_reward_window" @click.stop="show_reward_window=true">
      <div class="_3uZ5OL">
        <div class="_2PLkjk">
          <img class="_2R1-48"
               src="https://upload.jianshu.io/users/upload_avatars/9602437/8fb37921-2e4f-42a7-8568-63f187c5721b.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/100/h/100/format/webp"
               alt=""/>
          <div class="_2h5tnQ">
            给作者送糖
          </div>
        </div>
        <div class="_1-bCJJ">
          <div class="LMa6S_" :class="reward_info.money===num?'_1vONvL':''" @click="reward_info.money=num"
               v-for="num in reward_list"><span>{{num}}</span></div>
        </div>
        <textarea class="_1yN79W" placeholder="给Ta留言..." v-model="reward_msg"></textarea>
        <div class="_1_B577">
          选择支付方式
        </div>
        <div class="_1-bCJJ">
          <div class="LMa6S_ _3PA8BN" :class="{'_1vONvL': reward_info.pay_type===key}"
               @click="reward_info.pay_type=key" v-for="type,key in pay_type_list" :key="key">
            <span>{{type}}</span>
          </div>
        </div>
        <button type="button" class="_3A-4KL _1OyPqC _3Mi9q9 _1YbC5u" @click="confirm_payment">
          <span>确认支付</span><span> ￥</span>{{reward_info.money}}
        </button>
      </div>
    </div>
    <Footer></Footer>
  </div>
</template>

<script>
    import Header from "./common/Header";
    import Footer from "./common/Footer";

    export default {
        name: "Article",
        components: {
            Header,
            Footer,
        },
        data() {
            return {
                article_id: 0,
                token: '',
                article_detail: {
                    user: {}
                },
                show_reward_window: false,
                reward_list: [2, 5, 10, 20, 50, 100],
                pay_type_list: ['支付宝', '账户余额'],
                reward_info: {
                    'money': 2,
                    'pay_type': 0
                },
                reward_msg: '',
                follow_text: '已关注',
            }
        },
        filters: {
            time_format(time) {
                let t = new Date(time);
                return `${t.getFullYear()}.${t.getMonth() + 1}.${t.getDate()} ${t.getHours()}:${t.getMinutes()}`;
            },
        },
        methods: {
            get_article_detail () {
                this.$axios.get(`${this.$settings.Host}/article/detail/${this.article_id}/`,{
                    headers: this.token && {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    this.article_detail = response.data;
                }).catch(errors => {
                    this.$message.error('获取文章详情信息失败！')
                })
            },
            article_reward () {
                this.$settings.check_login(this);
                this.show_reward_window=true;
            },
            confirm_payment () {
                this.$axios.post(`${this.$settings.Host}/payments/alipay/`, {
                    article_id: this.article_id,
                    pay_type: this.reward_info.pay_type,
                    money: this.reward_info.money,
                    message: this.reward_msg,
                }, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response=>{
                    location.href = response.data
                }).catch(errors=>{
                    this.$message.error('获取支付宝链接失败！')
                })
            },
            login_handler () {
                this.$router.push('/user/login/')
            },
            follow_handler (operation) {
                this.$axios.post(`${this.$settings.Host}/article/follow/`, {
                    author_id: this.article_detail.user.id,
                    operation: operation,
                }, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response=>{
                    this.article_detail.follow_status = operation?1:2;
                }).catch(errors=>{
                    this.$message.error('关注操作失败，请稍后重试！')
                })
            },
        },
        created() {
            this.article_id = this.$route.params.pk;
            this.token = localStorage.user_token || sessionStorage.user_token;
            this.$axios.post(`${this.$settings.Host}/users/verify/`, {
                token: this.token
            }).then(response=>{
                this.get_article_detail();
            }).catch(error=>{
                this.token = null;
                localStorage.removeItem('user_token');
                sessionStorage.removeItem('user_toke');
                this.get_article_detail();
            });

        },
        mounted() {
            document.onclick = () => {
                this.show_reward_window = false;
            }
        }
    }
</script>
```

