# 荏苒资讯支付宝打赏功能

## 前端赞赏功能弹框

要人家赞赏，首先得给人弄出打赏的弹框。前端 Article.vue 页面代码：

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
                  <button class="_3kba3h _1OyPqC _3Mi9q9 _34692-" data-locale="zh-CN" type="button"><span>关注</span>
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
            <button class="_1OyPqC _3Mi9q9 _2WY0RL _1YbC5u" type="button" @click.stop="show_reward_window=true">
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
        <textarea class="_1yN79W" placeholder="给Ta留言..."></textarea>
        <div class="_1_B577">
          选择支付方式
        </div>
        <div class="_1-bCJJ">
          <div class="LMa6S_ _3PA8BN" :class="{'_1vONvL': reward_info.pay_type===type}"
               @click="reward_info.pay_type=type" v-for="type in pay_type_list"><span>{{type}}</span></div>
        </div>
        <button type="button" class="_3A-4KL _1OyPqC _3Mi9q9 _1YbC5u">
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
                    'money': this.reward_list[0],
                    'pay_type': '支付宝'
                },
            }
        },
        filters: {
            time_format(time) {
                let t = new Date(time);
                return `${t.getFullYear()}.${t.getMonth() + 1}.${t.getDate()} ${t.getHours()}:${t.getMinutes()}`;
            },
        },
        methods: {
            get_article_detail() {
                this.$axios.get(`${this.$settings.Host}/article/detail/${this.article_id}/`).then(response => {
                    this.article_detail = response.data;
                }).catch(errors => {
                    this.$message.error('获取文章详情信息失败！')
                })
            },

        },
        created() {
            this.article_id = this.$route.params.pk;
            this.token = localStorage.user_token || sessionStorage.user_token;
            this.get_article_detail();
        },
        mounted() {
            document.onclick = () => {
                this.show_reward_window = false;
            }
        }
    }
</script>
```

## 第三方支付接口

第三方支付接口，可以实现网络转账。

常见的第三方支付接口有很多：

国外：万事达，applePay，PayPal，Visa，八达通，西联（邮政汇款）

国内：支付宝，微信，京东钱包，百度钱包，贝宝（PayPal 中国版）

## 支付宝支付接口

### 支付宝开放平台登录

支付宝开放平台官网：https://open.alipay.com/platform/home.htm

使用支付宝账号登录即可。如果是第一次登录，可能会要求等级一些信息，如是填写就好。

![1584442807263](renran-reward.assets/1584442807263-1584868795806.png)

支付宝的申请需要企业资质，但是我们作为开发者可以使用支付宝提供的测试账号先开发功能，将来调整账号即可用于公司项目的正式运营。

地址：https://openhome.alipay.com/platform/developerIndex.htm

### 沙箱环境

- 是支付宝提供给开发者的模拟支付的环境
- 沙箱环境跟真实环境是分开的，项目上线时必须切换对应的配置服务器地址和开发者ID和密钥。
- 沙箱应用开发文档：https://docs.open.alipay.com/200/105311
- 沙箱账号：https://openhome.alipay.com/platform/appDaily.htm?tab=account

```
真实的支付宝网关:   https://openapi.alipay.com/gateway.do
沙箱的支付宝网关:   https://openapi.alipaydev.com/gateway.do
```

### 支付宝开发者文档

- 文档主页：[https://openhome.alipay.com/developmentDocument.htm](https://openhome.alipay.com/developmentDocument.htm)
- 产品介绍：<https://docs.open.alipay.com/270>

### 电脑网站支付流程

【前后端不分离】时序图（时间顺序流程图 ）

![电脑网站支付流程图](renran-reward.assets/%E7%94%B5%E8%84%91%E7%BD%91%E7%AB%99%E6%94%AF%E4%BB%98%E6%B5%81%E7%A8%8B%E5%9B%BE-1584868795809.png)

【前后端分离】时序图

![1584445167899](renran-reward.assets/1584445167899.png)

RSA 算法，属于非对称加密，一旦加密以后不能解密的。
可以通过密钥来进行验证。
密钥成对生成的。分公钥和私钥。
公钥用于验证数据（解签）
私钥用于加密数据（签名）

## 开发支付功能

首先创建专门用于第三方支付的 payments 子应用：

```
cd renranapi/apps
python ../../manage.py startapp payments
```

注册子应用

```python
INSTALLED_APPS = [
	...
    'payments',
]
```

### 配置秘钥

#### 生成应用的私钥和公钥

下载对应系统的秘钥生成工具：https://doc.open.alipay.com/docs/doc.htm?treeId=291&articleId=105971&docType=1

##### Windows 操作系统

生成如下，安装软件时需要管理员身份来安装。

![1566548916758](renran-reward.assets/1566548916758-1584868795809.png)

##### Linux 系统

生成方法如下：

```bash
cd renranapi/apps/payments/
mkdir keys
cd keys
openssl
OpenSSL> genrsa -out app_private_key.pem 2048                         # 生成私钥到指定文件中
OpenSSL> rsa -in app_private_key.pem -pubout -out app_public_key.pem  # 导出公钥
OpenSSL> exit
```

先要注册应用，然后设置接口加签方式。

![1584874490154](renran-reward.assets/1584874490154.png)

应用公钥复制粘贴到支付宝网站页面中。点击修改以后，粘贴进去。

![1554192143494](renran-reward.assets/1554192143494-1584868795810.png)

如果是使用沙箱测试，则需要在沙箱应用处配置应用公钥，从而获取支付宝公钥。

#### 保存应用私钥文件

在 payments 应用中新建 keys 目录，用来保存秘钥文件。

将应用私钥文件 app_private_key.pem 复制到 payment/keys 目录下。

Windows 系统生成的私钥必须在上下两行加上以下标识:

```
-----BEGIN RSA PRIVATE KEY-----
私钥
-----END RSA PRIVATE KEY-----
```

#### 保存支付宝公钥到项目中

在 payments/key 目录下新建 alipay_public_key.pem 文件，用于保存支付宝的公钥文件。

将支付宝的公钥内容复制到 alipay_public_key.pem 文件中

![1584874733720](renran-reward.assets/1584874733720.png)

```
-----BEGIN PUBLIC KEY-----
公钥
-----END PUBLIC KEY-----
```

![1554200221417](renran-reward.assets/1554200221417-1584868795810.png)



#### 使用支付宝的 sdk 开发支付接口

SDK：https://docs.open.alipay.com/270/106291/

Python 版本的支付宝 SDK 文档：[https://github.com/fzlee/alipay/blob/master/README.zh-hans.md](https://github.com/fzlee/alipay/blob/master/README.zh-hans.md)

安装命令：

```
pip install python-alipay-sdk --upgrade
```

### 后端提供发起支付的接口 url 地址

用户模型新增 money 字段，表示作者用户接收别人打赏的资金资产。

```python
class User(AbstractUser):
    """用户模型类"""
    mobile = models.CharField(max_length=15, null=True, unique=True, help_text="手机号码", verbose_name="手机号码")
    wechat = models.CharField(max_length=100, null=True, unique=True, help_text="微信账号", verbose_name="微信账号")
    alipay = models.CharField(max_length=100, null=True, unique=True, help_text="支付宝账号", verbose_name="支付宝账号")
    qq_number = models.CharField(max_length=11, null=True, unique=True, help_text="QQ号", verbose_name="QQ号")
    # 保存文件的子目录
    avatar = models.ImageField(upload_to="avatar", null=True, default=None, verbose_name="头像")
    nickname = models.CharField(max_length=100, null=True, default=None, verbose_name="用户昵称")
    money = models.DecimalField(max_digits=11, decimal_places=2, default=0, verbose_name="资金")

    class Meta:
        db_table = "rr_users"
        verbose_name = "用户信息"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.username
```

在 `payments/models.py` 中创建模型，保存打赏记录：

```python
from django.db import models
from users.models import User
from article.models import Article
from renranapi.utils.models import BaseModel

class Reward(BaseModel):
    REWARD_OPT = (
        (0, "支付宝"),
        (1, "余额"),
    )
    STATUS_OPT = (
        (0, "未付款"),
        (1, "已付款"),
        (2, "已取消"),
        (3, "超时取消"),
    )
    user = models.ForeignKey(User, on_delete=models.DO_NOTHING, verbose_name="打赏用户")
    money = models.DecimalField(decimal_places=2, max_digits=6, verbose_name="打赏金额")
    article = models.ForeignKey(Article, on_delete=models.DO_NOTHING, verbose_name="文章")
    status = models.SmallIntegerField(default=0,choices=STATUS_OPT, verbose_name="打赏状态")
    trade_no = models.CharField(max_length=255, null=True, blank=True, verbose_name="流水号")
    out_trade_no = models.CharField(max_length=255, null=True, blank=True, verbose_name="支付平台返回的流水号")
    reward_type = models.SmallIntegerField(default=0,choices=REWARD_OPT, verbose_name="打赏类型")
    message = models.TextField(null=True,blank=True, verbose_name="打赏留言")

    class Meta:
        db_table = "rr_reward"
        verbose_name = "打赏记录"
        verbose_name_plural = verbose_name

    def __str__(self):
        return f'{self.user.nickname}向{self.article.user.nickname}的文章《{self.article.name}》打赏了{self.money}元'
```

迁移迁移

```python
python manage.py makemigrations
python manage.py migrate
```

注册模型到 xadmin 中，创建 `adminx.py`，代码

```python
import xadmin

from .models import Reward
class RewardModelAdmin(object):
    pass
xadmin.site.register(Reward,RewardModelAdmin)
```

编写视图提供支付的 url 跳转链接地址：

```python
import random
from datetime import datetime

from alipay import AliPay
from django.conf import settings
from rest_framework import status
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response
from rest_framework.viewsets import ViewSet

from . import models
from article.models import Article


class AlipayAPIViewSet(ViewSet):
    permission_classes = [IsAuthenticated]
    def post(self, request):
        """生成打赏链接，并创建打赏记录"""
        user = request.user
        reward_type = request.data.get('pay_type')
        message = request.data.get('content')
        money = request.data.get('money')
        # 打赏金额应该有效
        if money <= 0:
            return Response({'error_msg': '打赏金额应该大于 0！'}, status=status.HTTP_400_BAD_REQUEST)
        article_id = request.data.get('article_id')
        # 确保文章存在
        try:
            Article.objects.get(id=article_id, is_public=True)
        except Article.DoesNotExist:
            return Response({'error_msg': '文章不存在或尚未发布，无法打赏'}, status=status.HTTP_400_BAD_REQUEST)
        # 确保文章的作者不是自己
        if Article.object.filter(user=user, id=article_id):
            return Response({'error_mes': '不能给自己的文章打赏！'}, status=status.HTTP_400_BAD_REQUEST)
        # 生成唯一随机流水号
        while 1:
            trade_no = f'{datetime.now().strftime("%Y%m%d%H%M%S")}{"%06d" % user.id}{"%06d" % random.randint(1, 999999)}'
            try:
                models.Reward.objects.get(trade_no=trade_no)
            except models.Reward.DoesNotExist:
                break

        reward = models.Reward.objects.create(
            user=user,
            money=money,
            article_id=article_id,
            status=0,
            trade_no=trade_no,
            out_trade_no=None,
            reward_type=reward_type,
            message=message,
            orders=0,
        )
        # 以支付宝的方式打赏
        if reward_type == 0:
            # 生成支付链接
            # 获取公钥和私钥
            with open(settings.ALIAPY_CONFIG["app_private_key_path"]) as fh:
                app_private_key_string = fh.read()
            with open(settings.ALIAPY_CONFIG["alipay_public_key_path"]) as fh:
                alipay_public_key_string = fh.read()
            # 初始化支付对象
            alipay = AliPay(
                appid=settings.ALIAPY_CONFIG["appid"],
                app_notify_url=settings.ALIAPY_CONFIG["app_notify_url"],  # 默认回调url
                app_private_key_string=app_private_key_string,
                # 支付宝的公钥，验证支付宝回传消息使用，不是你自己的公钥,
                alipay_public_key_string=alipay_public_key_string,
                sign_type=settings.ALIAPY_CONFIG["sign_type"],
                debug=settings.ALIAPY_CONFIG["debug"]  # 默认False
            )

            # 调用接口
            order_string = alipay.api_alipay_trade_page_pay(
                out_trade_no=reward.trade_no,
                total_amount=float(reward.money),  # 打赏金额
                subject="打赏文章",
                return_url=settings.ALIAPY_CONFIG["return_url"],
                notify_url=settings.ALIAPY_CONFIG["notify_url"]  # 可选, 不填则使用默认notify url
            )

            url = settings.ALIAPY_CONFIG["gateway_url"] + order_string
        else:
            url = ''
        return Response(url)
```

#### 在配置文件中编辑支付宝的配置信息（实际的值根据自己的账号而定）

在 `setttins/dev.py` 中添加支付宝接口相关的配置代码:

```python
# 支付宝相关配置
ALIAPY_CONFIG = {
    # "gateway_url": "https://openapi.alipay.com/gateway.do?",    # 真实支付宝网关地址
    "gateway_url": "https://openapi.alipaydev.com/gateway.do?",    # 沙箱支付宝网关地址
    "appid": "2016101900722372",   # 沙箱应用的 appid
    "app_notify_url": None,
    "app_private_key_path": os.path.join(BASE_DIR, "apps/payments/keys/app_private_key.pem"),    # 本地生成的私钥
    "alipay_public_key_path": os.path.join(BASE_DIR, "apps/payments/keys/alipay_public_key.pem"),    # 使用本地公钥在沙箱生成的公钥
    "sign_type": "RSA2",
    "debug": False,
    "return_url": "http://www.moluo.net:8080/wallet",    # 同步回调地址
    "notify_url": "http://api.renran.cn:8000/payments/alipay/result/",    # 异步结果通知
}
```

注册 url 地址，`payments/urls.py` 代码：

```python
from django.urls import path,re_path
from . import views
urlpatterns = [
    path('alipay/', views.AlipayAPIViewSet.as_view({'post': 'post'})),
]
```

总路由,代码:

```python
path('payments/', include('payments.urls')),
```

## 客户端实现点击打赏请求

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
                  <button class="_3kba3h _1OyPqC _3Mi9q9 _34692-" data-locale="zh-CN" type="button"><span>关注</span>
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
                this.$axios.get(`${this.$settings.Host}/article/detail/${this.article_id}/`).then(response => {
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
        },
        created() {
            this.article_id = this.$route.params.pk;
            this.token = localStorage.user_token || sessionStorage.user_token;
            this.get_article_detail();
        },
        mounted() {
            document.onclick = () => {
                this.show_reward_window = false;
            }
        }
    }
</script>
```

## 用户支付完成以后的支付结果处理

客户端接收支付宝跳转发送回来的同步结果参数，并发起请求服务端的同步处理结果的 API 接口。

首先创建前端页面组件，`Wallet.vue`

```vue
<template>

</template>

<script>
    export default {
        name: "Wallet",
        data () {
            return {
                token: '',
            }
        },
        created() {
            this.token = this.$settings.check_login(this);
            // 把支付宝的同步结果通知转发给服务端
            this.get_pay_result();
        },
        methods: {
            get_pay_result() {
                // 判断是否是从支付页面返回
                if(this.$route.query.out_trade_no) {
                    // 将结果返回给后台
                    this.$axios.get(`${this.$settings.Host}/payments/alipay/result/${location.search}`, {
                        headers: {
                            Authorization: `jwt ${this.token}`
                        }
                    }).then(response=>{
                        this.$router.push('/')
                    }).catch(errors=>{
                        this.$router.push('/')
                    })
                }
            }

        }
    }
</script>

<style scoped>

</style>
```

路由代码：

```javascript
import Wallet from "../components/Wallet";

export default new Router({
	...
    {
      path: '/wallet',
      name: 'Wallet',
      component: Wallet,
    },
  ]
})
```

### 服务端完成同步支付结果和异步通知的处理

用户付过钱之后，会跳转到回调页面，也就是 wallet。url 中会携带付款情况的各种参数。前端 wallet 页面会把这些参数传给后端，我们就在这里处理一下。除了前端会有支付结果返回外，支付宝还会给我们的后端发送异步通知。这两个通知的数据是一样的，只不过一个是 get 请求，一个是 post 请求，在获取数据的时候稍微处理一下即可。

首先是在视图中，增加一个 return_result 方法，用来处理前端发送过来的通知参数和支付宝给我们后端发送的通知：

```python
import random
from datetime import datetime

from alipay import AliPay
from django.conf import settings
from django.db import transaction
from rest_framework import status
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response
from rest_framework.viewsets import ViewSet

from . import models
from article.models import Article


class AlipayAPIViewSet(ViewSet):
    permission_classes = [IsAuthenticated]

    def get_alipay(self):
        # 生成支付链接
        # 获取公钥和私钥
        with open(settings.ALIAPY_CONFIG["app_private_key_path"]) as fh:
            app_private_key_string = fh.read()
        with open(settings.ALIAPY_CONFIG["alipay_public_key_path"]) as fh:
            alipay_public_key_string = fh.read()
        # 初始化支付对象
        alipay = AliPay(
            appid=settings.ALIAPY_CONFIG["appid"],
            app_notify_url=settings.ALIAPY_CONFIG["app_notify_url"],  # 默认回调url
            app_private_key_string=app_private_key_string,
            # 支付宝的公钥，验证支付宝回传消息使用，不是你自己的公钥,
            alipay_public_key_string=alipay_public_key_string,
            sign_type=settings.ALIAPY_CONFIG["sign_type"],
            debug=settings.ALIAPY_CONFIG["debug"]  # 默认False
        )
        return alipay

    def post(self, request):
        """生成打赏链接，并创建打赏记录"""
        user = request.user
        reward_type = request.data.get('pay_type')
        message = request.data.get('content')
        money = request.data.get('money')
        # 打赏金额应该有效
        if money <= 0:
            return Response({'error_msg': '打赏金额应该大于 0！'}, status=status.HTTP_400_BAD_REQUEST)
        article_id = request.data.get('article_id')
        # 确保文章存在
        try:
            Article.objects.get(id=article_id, is_public=True)
        except Article.DoesNotExist:
            return Response({'error_msg': '文章不存在或尚未发布，无法打赏'}, status=status.HTTP_400_BAD_REQUEST)
        # 确保文章的作者不是自己
        if Article.objects.filter(user=user, id=article_id):
            return Response({'error_mes': '不能给自己的文章打赏！'}, status=status.HTTP_400_BAD_REQUEST)
        # 生成唯一随机流水号
        while 1:
            trade_no = f'{datetime.now().strftime("%Y%m%d%H%M%S")}{"%06d" % user.id}{"%06d" % random.randint(1, 999999)}'
            try:
                models.Reward.objects.get(trade_no=trade_no)
            except models.Reward.DoesNotExist:
                break

        reward = models.Reward.objects.create(
            user=user,
            money=money,
            article_id=article_id,
            status=0,
            trade_no=trade_no,
            out_trade_no=None,
            reward_type=reward_type,
            message=message,
            orders=0,
        )
        # 以支付宝的方式打赏
        if reward_type == 0:
            alipay = self.get_alipay()
            # 调用接口
            order_string = alipay.api_alipay_trade_page_pay(
                out_trade_no=reward.trade_no,
                total_amount=float(reward.money),  # 打赏金额
                subject="打赏文章",
                return_url=settings.ALIAPY_CONFIG["return_url"],
                notify_url=settings.ALIAPY_CONFIG["notify_url"]  # 可选, 不填则使用默认notify url
            )

            url = settings.ALIAPY_CONFIG["gateway_url"] + order_string
        else:
            url = ''
        return Response(url)

    def return_result(self, request):
        """支付宝同步结果和异步通知处理"""
        # post和get请求获取data的方式略有不同，需要处理一下
        # 支付宝的异步回调需要有域名，且可以解析到IP，需要在服务器中测试使用
        data = request.query_params.dict() or request.data.dict()
        signature = data.pop('sign')
        alipay = self.get_alipay()
        success = alipay.verify(data, signature)
        if success:
            """支付结果处理"""
            # 开启ORM的MySQL事务的自动提交,在with语句范围内,所有的SQL会全部被事务控制,要么一起提交,要么一起不提交
            with transaction.atomic():
                # 设置事务的回滚点,用于指定在事务失败时,在哪一部分的SQL语句无效
                save_point = transaction.savepoint()
                try:
                    # 修改打赏记录的状态为已付款
                    reward = models.Reward.objects.get(trade_no=data.get('out_trade_no'), status=0)
                    reward.status = 1
                    reward.save()
                    # 增加文章的打赏人数
                    article = reward.article
                    article.reward_count += 1
                    article.save()
                    # 给文章作者资产增加打赏的资金
                    author = article.user
                    author.money = round(author.money + reward.money, 2)
                    author.save()
                except:
                    transaction.savepoint_rollback(save_point)
                    return Response({'error_msg': '支付有误！'}, status=status.HTTP_400_BAD_REQUEST)
        else:
            return Response({'error_msg': '支付有误！'}, status=status.HTTP_400_BAD_REQUEST)
```

路由代码：

```python
from django.urls import path,re_path
from . import views
urlpatterns = [
    path("alipay/", views.AliPayAPIViewSet.as_view({"post":"post"})),
    path("alipay/result/", views.AliPayAPIViewSet.as_view({"get":"return_result","post":"return_result"})),
]
```

支付宝功能就此实现，使用支付宝沙箱账号可以进行测试。

因为支付宝的异步通知是要发送请求给域名，需要在服务器中才能测试。