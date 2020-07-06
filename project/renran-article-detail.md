# 荏苒资讯文章详情页开发

## 文章详情页前端页面搭建

文章详情页，也就是文章发表了之后，用户真正看到的页面。其重要程度仅次于首页，是用户浏览的时间最长的页面。

在 `Writed.vue` 中打通跳转到文章详情页的链接：

```vue
<div class="_3CoyQ">
    <router-link :to="`/article/${article_id}`" class="_2-ZiM">{{article_name}}</router-link>
    <br>
    <router-link :to="`/article/${article_id}`" class="_2ajaT">发布成功，点击查看文章</router-link>
</div>
```

在 `router/index.js` 中创建路由，代码：

```javascript
import Article from "../components/Article";

export default new Router({
  mode: 'history',
  routes: [
    ...
    {
      path: '/article/:pk',
      name: 'Article',
      component: Article,
    },
  ]
})
```

创建 `Article.vue` 组件：

```vue
<template>
  <div class="_21bLU4 _3kbg6I">
   <Header></Header>
   <div class="_3VRLsv" role="main">
    <div class="_gp-ck">
     <section class="ouvJEz">
      <h1 class="_1RuRku">废掉一个人最快的方法，就是让他闲着</h1>
      <div class="rEsl9f">
       <div class="_2mYfmT">
        <a class="_1OhGeD" href="/u/a70487cda447" target="_blank" rel="noopener noreferrer"><img class="_13D2Eh" src="https://upload.jianshu.io/users/upload_avatars/18529254/.png?imageMogr2/auto-orient/strip|imageView2/1/w/96/h/96/format/webp" alt="" /></a>
        <div style="margin-left: 8px;">
         <div class="_3U4Smb">
          <span class="FxYr8x"><a class="_1OhGeD" href="/u/a70487cda447" target="_blank" rel="noopener noreferrer">書酱</a></span>
          <button data-locale="zh-CN" type="button" class="_3kba3h _1OyPqC _3Mi9q9 _34692-"><span>关注</span></button>
         </div>
         <div class="s-dsoj">
          <time datetime="2020-01-08T12:01:00.000Z">2020.01.08 20:01:00</time>
          <span>字数 2,510</span>
          <span>阅读 168</span>
         </div>
        </div>
       </div>
      </div>
      <article class="_2rhmJa">
       <div class="image-package">
        <div class="image-container" style="max-width: 640px; max-height: 420px; background-color: transparent;">
         <div class="image-container-fill" style="padding-bottom: 65.63%;"></div>
         <div class="image-view" data-width="640" data-height="420">
          <img src="https://upload-images.jianshu.io/upload_images/18529254-f62fac0d998cff23?imageMogr2/auto-orient/strip|imageView2/2/w/640/format/webp" />
         </div>
        </div>
        <div class="image-caption"></div>
       </div>
       <p>文/小鸟飞过</p>
       <p>罗曼&middot;罗兰说：“生活中最沉重的负担不是工作，而是无聊。”</p>
       <div class="image-package">
        <div class="image-container" style="max-width: 700px; max-height: 152px; background-color: transparent;">
         <div class="image-container-fill" style="padding-bottom: 14.069999999999999%;"></div>
         <div class="image-view" data-width="1080" data-height="152">
          <img src="http://upload-images.jianshu.io/upload_images/18529254-a932f0ad8fbd51bb?imageMogr2/auto-orient/strip|imageView2/2/w/1080/format/webp" />
         </div>
        </div>
        <div class="image-caption"></div>
       </div>
       <p><strong>废掉一个人最快的方法</strong></p>
       <p><strong>就是让他闲着</strong></p>
       <p>这段时间，综艺节目《妻子的浪漫旅行第三季3》正在热播，四对明星夫妻的相处模式曝光，也让观众更了解了曾经饱受争议的女人唐一菲。</p>
       <p>有人很喜欢她大大咧咧的女侠性格，有人为她叫屈，当然还是有人骂她，说她旧事重提。</p>
       <p>而我，则是觉得非常惋惜。</p>
       <p>唐一菲是中央戏剧学院表演系毕业，真正的科班出身。</p>
       <p>从2003年到2011年，基本保证每年都有作品，要么拍电视剧、要么拍电影，2008年出演新版《红楼梦》的秦可卿也是颇为动人。</p>
       <div class="image-package">
        <div class="image-container" style="max-width: 533px; max-height: 510px; background-color: transparent;">
         <div class="image-container-fill" style="padding-bottom: 95.67999999999999%;"></div>
         <div class="image-view" data-width="533" data-height="510">
          <img src="http://upload-images.jianshu.io/upload_images/18529254-d92ace292d78aecb?imageMogr2/auto-orient/strip|imageView2/2/w/533/format/webp" />
         </div>
        </div>
        <div class="image-caption"></div>
       </div>
       <p>可是自2012年结婚后，8年时间里，只拍了一部电视剧，就再也没了一点儿消息，仿佛整个人生都停滞了。</p>
       <p>她在《妻子3》中展现出的婚姻状态是非常可悲的。</p>
       <p>一喝酒，就是吐槽自己的人生被毁了。</p>
       <div class="image-package">
        <div class="image-container" style="max-width: 532px; max-height: 394px;">
         <div class="image-container-fill" style="padding-bottom: 74.06%;"></div>
         <div class="image-view" data-width="532" data-height="394">
          <img data-original-src="//upload-images.jianshu.io/upload_images/18529254-5f20af5bb10bfa12" data-original-width="532" data-original-height="394" data-original-format="image/jpeg" data-original-filesize="17915" data-image-index="3" style="cursor: zoom-in;" class="image-loading" />
         </div>
        </div>
        <div class="image-caption"></div>
       </div>
       <p>要么直接形容老公凌潇肃是缩头乌龟。</p>
       <div class="image-package">
        <div class="image-container" style="max-width: 506px; max-height: 360px;">
         <div class="image-container-fill" style="padding-bottom: 71.15%;"></div>
         <div class="image-view" data-width="506" data-height="360">
          <img data-original-src="//upload-images.jianshu.io/upload_images/18529254-f2478cdc59c7e193" data-original-width="506" data-original-height="360" data-original-format="image/jpeg" data-original-filesize="23772" data-image-index="4" style="cursor: zoom-in;" class="image-loading" />
         </div>
        </div>
        <div class="image-caption"></div>
       </div>
       <p>作者简介：小鸟飞过，富小书的人，富书专栏作者，写温暖的文字，传递美好的情感；本文首发富小书（ID：fxsfrc），你身边最好的闺蜜，富书2018重磅推出新书《好好生活》。</p>
       <p><strong>注：本文章图片来源网络，如有侵权，请联系删除。</strong></p>
      </article>
      <div></div>
      <div class="_1kCBjS">
       <div class="_18vaTa">
        <div class="_3BUZPB">
         <div class="_2Bo4Th" role="button" tabindex="-1" aria-label="给文章点赞">
          <i aria-label="ic-like" class="anticon">
           <svg width="1em" height="1em" fill="currentColor" aria-hidden="true" focusable="false" class="">
            <use xlink:href="#ic-like"></use>
           </svg></i>
         </div>
         <span class="_1LOh_5" role="button" tabindex="-1" aria-label="查看点赞列表">8人点赞<i aria-label="icon: right" class="anticon anticon-right">
           <svg viewbox="64 64 896 896" focusable="false" class="" data-icon="right" width="1em" height="1em" fill="currentColor" aria-hidden="true">
            <path d="M765.7 486.8L314.9 134.7A7.97 7.97 0 0 0 302 141v77.3c0 4.9 2.3 9.6 6.1 12.6l360 281.1-360 281.1c-3.9 3-6.1 7.7-6.1 12.6V883c0 6.7 7.7 10.4 12.9 6.3l450.8-352.1a31.96 31.96 0 0 0 0-50.4z"></path>
           </svg></i></span>
        </div>
        <div class="_3BUZPB">
         <div class="_2Bo4Th" role="button" tabindex="-1">
          <i aria-label="ic-dislike" class="anticon">
           <svg width="1em" height="1em" fill="currentColor" aria-hidden="true" focusable="false" class="">
            <use xlink:href="#ic-dislike"></use>
           </svg></i>
         </div>
        </div>
       </div>
       <div class="_18vaTa">
        <a class="_3BUZPB _1x1ok9 _1OhGeD" href="/nb/38290018" target="_blank" rel="noopener noreferrer"><i aria-label="ic-notebook" class="anticon">
          <svg width="1em" height="1em" fill="currentColor" aria-hidden="true" focusable="false" class="">
           <use xlink:href="#ic-notebook"></use>
          </svg></i><span>随笔</span></a>
        <div class="_3BUZPB ant-dropdown-trigger">
         <div class="_2Bo4Th">
          <i aria-label="ic-others" class="anticon">
           <svg width="1em" height="1em" fill="currentColor" aria-hidden="true" focusable="false" class="">
            <use xlink:href="#ic-others"></use>
           </svg></i>
         </div>
        </div>
       </div>
      </div>
      <div class="_19DgIp" style="margin-top:24px;margin-bottom:24px"></div>
      <div class="_13lIbp">
       <div class="_191KSt">
        &quot;小礼物走一走，来简书关注我&quot;
       </div>
       <button type="button" class="_1OyPqC _3Mi9q9 _2WY0RL _1YbC5u"><span>赞赏支持</span></button>
       <span class="_3zdmIj">还没有人赞赏，支持一下</span>
      </div>
      <div class="d0hShY">
       <a class="_1OhGeD" href="/u/a70487cda447" target="_blank" rel="noopener noreferrer"><img class="_27NmgV" src="https://upload.jianshu.io/users/upload_avatars/18529254/.png?imageMogr2/auto-orient/strip|imageView2/1/w/100/h/100/format/webp" alt="  " /></a>
       <div class="Uz-vZq">
        <div class="Cqpr1X">
         <a class="HC3FFO _1OhGeD" href="/u/a70487cda447" title="書酱" target="_blank" rel="noopener noreferrer">書酱</a>
         <span class="_2WEj6j" title="你读书的样子真好看。">你读书的样子真好看。</span>
        </div>
        <div class="lJvI3S">
         <span>总资产0</span>
         <span>共写了78.7W字</span>
         <span>获得6,072个赞</span>
         <span>共1,308个粉丝</span>
        </div>
       </div>
       <button data-locale="zh-CN" type="button" class="_1OyPqC _3Mi9q9"><span>关注</span></button>
      </div>
     </section>
     <div id="note-page-comment">
      <div class="lazyload-placeholder"></div>
     </div>
    </div>
    <aside class="_2OwGUo">
     <section class="_3Z3nHf">
      <div class="_3Oo-T1">
       <a class="_1OhGeD" href="/u/a70487cda447" target="_blank" rel="noopener noreferrer"><img class="_3T9iJQ" src="https://upload.jianshu.io/users/upload_avatars/18529254/.png?imageMogr2/auto-orient/strip|imageView2/1/w/90/h/90/format/webp" alt="" /></a>
       <div class="_32ZTTG">
        <div class="_2O0T_w">
         <div class="_2v-h3G">
          <span class="_2vh4fr" title="書酱"><a class="_1OhGeD" href="/u/a70487cda447" target="_blank" rel="noopener noreferrer">書酱</a></span>
         </div>
         <button data-locale="zh-CN" type="button" class="tzrf9N _1OyPqC _3Mi9q9 _34692-"><span>关注</span></button>
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
          <a class="_1-HJSV _1OhGeD" href="/p/a3e56a0559ff" target="_blank" rel="noopener noreferrer">这些话没人告诉你，但必须知道的社会规则</a>
         </div>
         <div class="_19haGh">
          阅读 5,837
         </div>
        </div>
        <div class="cuOxAY" role="listitem">
         <div class="_3L5YSq" title="浙大学霸最美笔记曝光：真正的牛人，都“变态”到了极致">
          <a class="_1-HJSV _1OhGeD" href="/p/d2a3724e2839" target="_blank" rel="noopener noreferrer">浙大学霸最美笔记曝光：真正的牛人，都“变态”到了极致</a>
         </div>
         <div class="_19haGh">
          阅读 12,447
         </div>
        </div>
        <div class="cuOxAY" role="listitem">
         <div class="_3L5YSq" title="征服一个女人最好的方式：不是讨好她，而是懂得去折腾她">
          <a class="_1-HJSV _1OhGeD" href="/p/f6acf67f039b" target="_blank" rel="noopener noreferrer">征服一个女人最好的方式：不是讨好她，而是懂得去折腾她</a>
         </div>
         <div class="_19haGh">
          阅读 5,311
         </div>
        </div>
        <div class="cuOxAY" role="listitem">
         <div class="_3L5YSq" title="告别平庸的15个小方法">
          <a class="_1-HJSV _1OhGeD" href="/p/cff7eb6b232b" target="_blank" rel="noopener noreferrer">告别平庸的15个小方法</a>
         </div>
         <div class="_19haGh">
          阅读 7,040
         </div>
        </div>
        <div class="cuOxAY" role="listitem">
         <div class="_3L5YSq" title="轻微抑郁的人，会说这3句“口头禅”，若你一个不占，偷着乐吧">
          <a class="_1-HJSV _1OhGeD" href="/p/2a0ca1729b4b" target="_blank" rel="noopener noreferrer">轻微抑郁的人，会说这3句“口头禅”，若你一个不占，偷着乐吧</a>
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
   <Footer></Footer>
  </div>
</template>

<script>
    import Header from "./common/Header";
    import Footer from "./common/Footer";
    export default {
        name: "Article",
        components:{
          Header,
          Footer,
        }
    }
</script>

<style scoped>
*,:after,:before {
	box-sizing: border-box
}

a:hover {
	color: #fa9e87
}

a:active {
	color: #c75342
}

a:active,a:hover {
	text-decoration: none;
	outline: 0
}

a[disabled] {
	color: rgba(0,0,0,.25);
	cursor: not-allowed;
	pointer-events: none
}

img {
	vertical-align: middle;
	border-style: none
}

svg:not(:root) {
	overflow: hidden
}
[role=button],a,area,button,input:not([type=range]),label,select,summary,textarea {
	-ms-touch-action: manipulation;
	touch-action: manipulation
}

button,input,optgroup,select,textarea {
	margin: 0;
	color: inherit;
	font-size: inherit;
	font-family: inherit;
	line-height: inherit
}

button,input {
	overflow: visible
}

button,select {
	text-transform: none
}[type=reset],[type=submit],button,html [type=button] {
	-webkit-appearance: button
}[type=button]::-moz-focus-inner,[type=reset]::-moz-focus-inner,[type=submit]::-moz-focus-inner,button::-moz-focus-inner {
	padding: 0;
	border-style: none
}

input[type=checkbox],input[type=radio] {
	-webkit-box-sizing: border-box;
	box-sizing: border-box;
	padding: 0
}

input[type=date],input[type=datetime-local],input[type=month],input[type=time] {
	-webkit-appearance: listbox
}

textarea {
	overflow: auto;
	resize: vertical
}

fieldset {
	min-width: 0;
	margin: 0;
	padding: 0;
	border: 0
}

legend {
	display: block;
	width: 100%;
	max-width: 100%;
	margin-bottom: .5em;
	padding: 0;
	color: inherit;
	font-size: 1.5em;
	line-height: inherit;
	white-space: normal
}

progress {
	vertical-align: baseline
}

::selection {
	color: #fff;
	background: #ec7259
}

.anticon {
	display: inline-block;
	color: inherit;
	font-style: normal;
	line-height: 0;
	text-align: center;
	text-transform: none;
	vertical-align: -.125em;
	text-rendering: optimizeLegibility;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale
}

.anticon>* {
	line-height: 1
}

.anticon svg {
	display: inline-block
}

.anticon:before {
	display: none
}

.anticon .anticon-icon {
	display: block
}

.anticon[tabindex] {
	cursor: pointer
}

._3Vh8Z9-info ._3Vh8Z9-notice-content .anticon,._3Vh8Z9-loading ._3Vh8Z9-notice-content .anticon {
	color: #0681d0
}

._3Vh8Z9-success ._3Vh8Z9-notice-content .anticon {
	color: #42c02e
}

._3Vh8Z9-warning ._3Vh8Z9-notice-content .anticon {
	color: #fa0
}

._3Vh8Z9-error ._3Vh8Z9-notice-content .anticon {
	color: #f50
}


._1OyPqC {
	position: relative;
	flex-shrink: 0;
	display: inline-flex;
	justify-content: center;
	align-items: center;
	border-radius: 50px;
	touch-action: manipulation;
	cursor: pointer;
	background-image: none;
	white-space: nowrap;
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none;
	transition: all .2s cubic-bezier(.645,.045,.355,1);
	font-size: 14px;
	padding: 4px 12px;
	color: #969696;
	background-color: #fff;
	border: 1px solid #999
}

._1OyPqC+._1OyPqC,._1OyPqC>.anticon+span,._1OyPqC>span+.anticon {
	margin-left: 8px
}

._1OyPqC:focus,._1OyPqC:hover {
	color: #7d7d7d;
	background-color: #fff;
	border-color: #999
}

._1OyPqC:active {
	color: #636363;
	background-color: #fff;
	border-color: #999
}

body.reader-night-mode ._1OyPqC {
	color: #b3b3b3;
	background-color: #3d3d3d;
	border-color: #999
}

body.reader-night-mode ._1OyPqC:focus,body.reader-night-mode ._1OyPqC:hover {
	color: #ccc;
	background-color: #3d3d3d;
	border-color: #999
}

body.reader-night-mode ._1OyPqC:active {
	color: #e6e6e6;
	background-color: #3d3d3d;
	border-color: #999
}

._1OyPqC._2SIRy5:before {
	content: "";
	position: absolute;
	border-radius: inherit;
	border: 0 solid #999;
	animation: ZAhfCQ .3s ease-out forwards
}

body.reader-night-mode ._2m5ItF,body.reader-night-mode ._2m5ItF[disabled]:hover {
	color: #0681d0;
	background-color: #3d3d3d;
	border-color: #0681d0
}

body.reader-night-mode ._2m5ItF:focus,body.reader-night-mode ._2m5ItF:hover {
	color: #0681d0;
	background-color: #3a4145;
	border-color: #0681d0
}

body.reader-night-mode ._2m5ItF:active {
	color: #0681d0;
	background-color: #38444c;
	border-color: #0681d0
}

body.reader-night-mode ._2m5ItF._1YbC5u,body.reader-night-mode ._2m5ItF._1YbC5u[disabled]:hover {
	color: #fff;
	background-color: #0681d0;
	border-color: #0681d0
}

body.reader-night-mode ._2m5ItF._1YbC5u:focus,body.reader-night-mode ._2m5ItF._1YbC5u:hover {
	color: #fff;
	background-color: #1287d2;
	border-color: #0681d0
}

body.reader-night-mode ._2m5ItF._1YbC5u:active {
	color: #fff;
	background-color: #1f8ed5;
	border-color: #0681d0
}

._3Mi9q9,._3Mi9q9[disabled]:hover {
	color: #ec7259;
	background-color: #fff;
	border-color: #ec7259
}

._3Mi9q9:focus,._3Mi9q9:hover {
	color: #ec7259;
	background-color: #fef8f7;
	border-color: #ec7259
}

._3Mi9q9:active {
	color: #ec7259;
	background-color: #fdf1ee;
	border-color: #ec7259
}

body.reader-night-mode ._3Mi9q9,body.reader-night-mode ._3Mi9q9[disabled]:hover {
	color: #ec7259;
	background-color: #3d3d3d;
	border-color: #ec7259
}

body.reader-night-mode ._3Mi9q9:focus,body.reader-night-mode ._3Mi9q9:hover {
	color: #ec7259;
	background-color: #46403f;
	border-color: #ec7259
}

body.reader-night-mode ._3Mi9q9:active {
	color: #ec7259;
	background-color: #4f4240;
	border-color: #ec7259
}

._3Mi9q9._2SIRy5:before {
	content: "";
	position: absolute;
	border-radius: inherit;
	border: 0 solid #ec7259;
	animation: ZAhfCQ .3s ease-out forwards
}

._3Mi9q9._1YbC5u,._3Mi9q9._1YbC5u[disabled]:hover {
	color: #fff;
	background-color: #ec7259;
	border-color: #ec7259
}

._3Mi9q9._1YbC5u:focus,._3Mi9q9._1YbC5u:hover {
	color: #fff;
	background-color: #ed7961;
	border-color: #ec7259
}

._3Mi9q9._1YbC5u:active {
	color: #fff;
	background-color: #ee806a;
	border-color: #ec7259
}

body.reader-night-mode ._3Mi9q9._1YbC5u,body.reader-night-mode ._3Mi9q9._1YbC5u[disabled]:hover {
	color: #fff;
	background-color: #ec7259;
	border-color: #ec7259
}

body.reader-night-mode ._3Mi9q9._1YbC5u:focus,body.reader-night-mode ._3Mi9q9._1YbC5u:hover {
	color: #fff;
	background-color: #ed7961;
	border-color: #ec7259
}

body.reader-night-mode ._3Mi9q9._1YbC5u:active {
	color: #fff;
	background-color: #ee806a;
	border-color: #ec7259
}

._3tVfGA {
	color: #999
}

._3tVfGA hr {
	margin: 16px 0;
	border: none;
	border-top: 1px solid #eee
}

body.reader-night-mode ._3tVfGA hr {
	border-color: #2f2f2f
}

.PY53UF:hover>i {
	visibility: visible;
	opacity: 1
}

.PY53UF>span {
	margin-right: 4px;
	overflow: hidden;
	text-overflow: ellipsis;
	white-space: nowrap
}

.PY53UF>i {
	font-size: 12px;
	visibility: hidden;
	opacity: 0
}


._3VRLsv {
	box-sizing: content-box;
	width: 1000px;
	padding-left: 16px;
	padding-right: 16px;
	margin-left: auto;
	margin-right: auto
}

._3Z3nHf,.ouvJEz {
	background-color: #fff;
	border-radius: 4px;
	margin-bottom: 10px
}

body.reader-night-mode ._3Z3nHf,body.reader-night-mode .ouvJEz {
	background-color: #3d3d3d
}

._3kbg6I {
	background-color: #f9f9f9
}

body.reader-night-mode ._3kbg6I {
	background-color: #2d2d2d
}

._3VRLsv {
	display: flex;
	justify-content: center;
	align-items: flex-start;
	min-height: calc(100vh - 66px);
	padding-top: 10px;
	font-size: 16px
}

._gp-ck {
	flex-shrink: 0;
	width: 730px;
	margin-bottom: 24px;
	margin-right: 10px
}

.ouvJEz {
	padding: 24px
}

._2OwGUo {
	flex-shrink: 0;
	width: 260px
}

._3Z3nHf {
	padding: 16px
}

.QxT4hD {
	display: flex;
	justify-content: space-between;
	align-items: center;
	margin-bottom: 16px;
	padding-left: 12px;
	border-left: 4px solid #ec7259;
	font-size: 18px;
	font-weight: 500;
	height: 20px;
	line-height: 20px
}

._3yfjDE {
	flex-direction: column;
	height: calc(100vh - 56px)
}

._3yfjDE,.l3_euy {
	display: flex;
	align-items: center;
	justify-content: center
}

.l3_euy {
	margin-bottom: 32px;
	padding-bottom: 48px;
	border-bottom: 1px solid #eee
}

body.reader-night-mode .l3_euy {
	border-color: #2f2f2f
}

._23lAnl {
	width: 280px;
	height: 280px;
	margin-right: 48px
}

._2msktx {
	font-size: 24px;
	font-weight: 500;
	margin-bottom: 8px
}

._1gKcub {
	font-size: 14px;
	width: 400px;
	line-height: 1.7
}

._2QxXJ4 {
	display: flex
}

._2QxXJ4,._3Fatyw {
	align-items: center
}

._3Fatyw {
	display: inline-flex;
	justify-content: center;
	font-size: 14px
}

._3Fatyw>i {
	font-size: 18px;
	margin-right: 4px
}

._3Fatyw+._3Fatyw {
	margin-left: 120px
}

._23ISFX {
	position: relative;
	display: block;
	margin: 0 auto
}

._23ISFX-mask {
	position: fixed;
	top: 0;
	right: 0;
	left: 0;
	bottom: 0;
	background-color: rgba(0,0,0,.5);
	height: 100vh;
	filter: alpha(opacity=50);
	z-index: 1000
}

._23ISFX-mask-hidden {
	display: none
}

._23ISFX-wrap {
	position: fixed;
	top: 0;
	right: 0;
	bottom: 0;
	left: 0;
	z-index: 1050;
	overflow: auto;
	outline: 0;
	-webkit-overflow-scrolling: touch
}

._23ISFX-wrap-middle {
	text-align: center
}

._23ISFX-wrap-middle:before {
	content: "";
	display: inline-block;
	width: 0;
	height: 100%;
	vertical-align: middle
}

._23ISFX-wrap-middle ._23ISFX {
	position: static;
	display: inline-block;
	text-align: left;
	vertical-align: middle
}

._23ISFX-content {
	position: relative;
	background-color: #fff;
	border-radius: 4px;
	box-shadow: 0 2px 8px rgba(26,26,26,.1)
}

body.reader-night-mode ._23ISFX-content {
	background-color: #3d3d3d
}

._23ISFX-close {
	position: absolute;
	top: 0;
	right: 0;
	margin: 0;
	padding: 0;
	border: 0;
	outline: 0;
	color: grey;
	background-color: transparent;
	cursor: pointer;
	text-decoration: none
}

._23ISFX-close-x {
	display: flex;
	align-items: center;
	justify-content: center;
	width: 48px;
	height: 48px;
	font-size: 16px
}

._23ISFX-close:hover {
	color: #4d4d4d
}

body.reader-night-mode ._23ISFX-close:hover {
	color: #b3b3b3
}

._23ISFX-header {
	border-bottom: 1px solid #eee;
	border-radius: 4px 4px 0 0
}

._23ISFX-title {
	margin: 0;
	font-size: 18px;
	font-weight: bold
}

._23ISFX-body {
	line-height: 1.5;
	word-wrap: break-word
}

._23ISFX-footer {
	display: flex;
	justify-content: flex-end;
	align-items: center;
	border-top: 1px solid #eee;
	border-radius: 4px 4px 0 0
}

._23ISFX-footer,._23ISFX-header {
	padding: 16px 24px
}

body.reader-night-mode ._23ISFX-footer,body.reader-night-mode ._23ISFX-header {
	border-color: #2f2f2f
}

._23ISFX.zoom-appear,._23ISFX.zoom-enter {
	transform: none;
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none
}

.KdlMNE {
	height: 480px;
	overflow-y: scroll;
	padding: 0 24px
}

.WaroTI {
	position: relative;
	padding: 16px 24px
}

._1fPOct {
	font-size: 48px;
	color: #ec7259;
	text-align: center;
	margin: 24px 0
}

._1QfoLq {
	font-size: 15px;
	margin-bottom: 20px
}

.foQ1KX {
	display: flex
}

.foQ1KX ._1RGYzT {
	flex-shrink: 0
}

.foQ1KX ._2eRvNE {
	display: flex;
	overflow: hidden
}

.foQ1KX ._3gPPP9 {
	overflow: hidden;
	text-overflow: ellipsis;
	white-space: nowrap
}

._1G9Mr4 {
	display: block;
	width: 100%;
	font-size: 14px;
	margin-bottom: 20px;
	padding: 12px;
	border: 1px solid #eee;
	border-radius: 4px;
	resize: none
}

._1G9Mr4:-ms-input-placeholder {
	color: #999
}

._1G9Mr4::-ms-input-placeholder {
	color: #999
}

._1G9Mr4::placeholder {
	color: #999
}

body.reader-night-mode ._1G9Mr4 {
	background-color: #333;
	border-color: #2f2f2f
}

.nnghRR {
	font-size: 13px;
	color: #969696
}

.nnghRR>p {
	margin-bottom: 0
}

._2cxUIy {
	margin: 0;
	padding: 0;
	list-style: none
}

.LtPwLP {
	margin-bottom: 16px;
	padding-bottom: 16px;
	display: flex;
	justify-content: space-between;
	align-items: center;
	border-bottom: 1px solid #eee
}

.LtPwLP>div {
	min-height: 100px;
	flex-grow: 1
}

.LtPwLP img {
	width: 150px;
	height: 100px;
	border-radius: 4px;
	border: 1px solid #f2f2f2;
	flex-shrink: 0
}


body.reader-night-mode ._2W7JCU:hover {
	background-color: #a6a6a6
}

body.reader-night-mode ._2W7JCU._1je2YA {
	background-color: #bfbfbf
}

._3F7sjs {
	width: 100%;
	display: flex;
	flex-wrap: wrap;
	flex-shrink: 0
}

._3kPlPc {
	display: inline-flex;
	justify-content: center;
	align-items: center;
	flex-shrink: 0;
	font-size: 24px;
	color: #404040;
	width: 32px;
	height: 32px;
	padding: 4px;
	border-radius: 4px;
	cursor: pointer;
	overflow: hidden
}

._3kPlPc:hover {
	background-color: rgba(51,51,51,.1)
}

body.reader-night-mode ._3kPlPc:hover {
	background-color: #4d4d4d
}

body.reader-night-mode ._1SgxkY {
	color: #a6a6a6
}

body.reader-night-mode ._1SgxkY:hover {
	background-color: #303030
}

._1Jdfvb {
	box-sizing: content-box;
	width: 1000px;
	padding-left: 16px;
	padding-right: 16px;
	margin-left: auto;
	margin-right: auto
}

.W2TSX_ {
	background-color: #f2f2f2
}

.W2TSX_:-ms-input-placeholder {
	color: #999
}

.W2TSX_::-ms-input-placeholder {
	color: #999
}

.W2TSX_::placeholder {
	color: #999
}

body.reader-night-mode .W2TSX_ {
	background-color: #333
}

._1LI0En {
	position: relative;
	display: block
}

._2xr8G8 {
	position: fixed;
	left: 0;
	right: 0;
	bottom: 0;
	background-color: #fff;
	box-shadow: 0 -2px 10px rgba(0,0,0,.05);
	z-index: 100
}

body.reader-night-mode ._2xr8G8 {
	background-color: #3d3d3d
}

._1Jdfvb {
	display: flex;
	align-items: center;
	padding-top: 10px;
	padding-bottom: 10px
}

._1Jdfvb.ufcbR- {
	align-items: flex-end
}

._1Jdfvb.ufcbR- .W2TSX_ {
	width: 560px;
	height: 56px;
	padding-right: 80px;
	border-radius: 4px
}

.TDvCqd {
	display: flex;
	position: relative
}

.TDvCqd[focus-within] .W2TSX_ {
	will-change: width,height,padding-right,border-radius
}

.TDvCqd:focus-within .W2TSX_ {
	will-change: width,height,padding-right,border-radius
}

.W2TSX_ {
	display: inline-flex;
	width: 400px;
	height: 36px;
	resize: none;
	margin-right: 16px;
	padding: 8px 18px;
	border-radius: 18px;
	border: none;
	transition: all .2s cubic-bezier(.19,.4,.17,.85)
}

._2qhU6p {
	position: absolute;
	right: 16px;
	bottom: 8px;
	font-size: 20px;
	margin-right: 12px;
	color: #969696
}

._2qhU6p:hover {
	color: #7d7d7d
}

.-pXE92 {
	color: #969696;
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none
}

.-pXE92,._3nj4GN {
	display: flex;
	align-items: center
}

._3nj4GN {
	font-size: 14px;
	cursor: pointer
}

._3nj4GN>span {
	margin-left: 8px;
	line-height: 20px
}

._3nj4GN .anticon {
	font-size: 22px
}

._3nj4GN:not(:last-child) {
	margin-right: 24px
}

._3nj4GN._3oieia {
	color: #ec7259
}

._2edyx1 {
	flex-shrink: 0;
	margin-right: 8px
}

._1_2JEL {
	display: block
}

.rEsl9f {
	display: flex;
	justify-content: space-between;
	align-items: center;
	margin-bottom: 32px;
	font-size: 13px
}

.s-dsoj {
	display: flex;
	color: #969696
}

.s-dsoj>:not(:last-child) {
	margin-right: 10px
}

._1NDgW7 {
	color: #969696;
	flex-shrink: 0
}

._3tCVn5 {
	display: inline-flex;
	align-items: center;
	justify-content: center;
	color: #ec7259
}

._3tCVn5 i {
	margin-right: .5em
}

._2mYfmT {
	display: flex;
	align-items: center
}

._13D2Eh {
	display: block;
	border-radius: 50%;
	border: 1px solid #eee;
	min-width: 48px;
	min-height: 48px;
	width: 48px;
	height: 48px
}

body.reader-night-mode ._13D2Eh {
	border-color: #2f2f2f
}

._3U4Smb {
	display: flex;
	align-items: center;
	margin-bottom: 6px
}

.FxYr8x {
	font-size: 16px;
	font-weight: 500;
	margin-right: 8px
}

._3kba3h {
	padding: 2px 9px
}

._3_y8t4 {
	display: flex;
	align-items: center;
	width: 100%;
	margin: 20px 0;
	padding: 8px 16px;
	border-radius: 4px;
	background-color: #f2f2f2
}

body.reader-night-mode ._3_y8t4 {
	background-color: #4d4d4d
}

._3_y8t4._1cBl4m,body.reader-night-mode ._3_y8t4._1cBl4m {
	background-color: rgba(218,158,85,.1)
}

._3_y8t4._1cBl4m ._1NiROM {
	background-image: url(data:image/png;
	base64,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);animation: none
}

._1NiROM {
	flex-shrink: 0;
	width: 32px;
	height: 32px;
	cursor: pointer;
	background-image: url(data:image/png;
	base64,iVBORw0KGgoAAAANSUhEUgAAAHAAAABwCAMAAADxPgR5AAAAgVBMVEUAAADrcFrqb1vqb1rrb1rrcFrrcFvvc1/rcFrucVzrb1rrb1ruc1z4dGXxdWDrb1rrcVzrcVzscVv/lGvrcFvtcVvscVvscFvscF3velv/gHzrb1rrcFvscVzrcFvtcVzrcFvqcFvrcFztcVzzeV/rb1vqb1rqb1rrcFzrcFvqb1ocs5f0AAAAKnRSTlMA+8fo7c5wH98k5JYsCxinTEp5BLJSgms2DwbawELyOtKfZGATjPi4i4l1OaSPAAAD10lEQVRo3rTY2ZaqMBAF0BOMzGCDIoPtrG2b///A+5AS0fZqCsJ+d9WiKqeSJXiCzI0uzs6XQkh/51wiNwswkiQ7T3/UCz/Tc5bAsnjpCPWGcJYxrEndqTIwdVPYsF4IZUgs1hjqy1EszheGKJ57KZ19Xmxj79Q0Jy/eFvnekc+dLdBX/K26wtnRwwvecRaqru+4Xwyu3dmt8uBtPvNVd5bXBGxZ2GlkVOGjKpKdbmTgmf92flwmhj0pQ9X6nYPBc+69PDQw1hzunXU8RhakIht3zmyNu1FEGiekFIrMUrClM0VECSNLRfx1z93kK7KEgUiRRY2e6oUiET66NUS4GMAVt6GYft+kwiDVRGmR2fz8AAMFvskcy9sCTjFYelv85Zv8Cdq+NSyoafeL/+bRk/R9Nayo6Rulh5fmDs0vhSUpzdF5va5oX08CWBNMaJPjhYw6XsGiik5Fhj8SulpcWOXSFZfg2ZX2GSyjLXfFk1joA1PDstrXk4rxiDKzhnVryjYeFO2qtY+ugwJdOqKbFCNIN3qdPOw0/gnln9TuhtM7ZjXHKOYrvW/+zPWAkRzoRD5lJWwwkiZ8zHgq6N4aTamzmD4MVSYYTSIfDuXU9IFVRLMyQR9RNxmxHmllthbDAj1UukbceTitDAahLTzw6WQsOyHM8cm3IpucH9j8HsVEn9EAn4SqtduCKdDnNGnXWoiPJqpjfwJP2K63M90TvIJKHvrcGec2FEdGQeLEYDi2wdD/n3n8gkqcGaH09P9yt2lKsAtyQynpbGa6PeyC3FBS+jJapHt2QXYo97ROI4o9tyA7lDlt7Au9cLgFuaGkd9qFWrtlF2SHckuHZUdrnFuQHcpY9x8+xXBgQSVyoyD6FI/TsIImB+9Egdd3RTOsoMkbpdGN+Fe9tawwDMMwSumaso21DMYeZTvslv//wF10C4PIdlxXPxAIUSJLiuGCeapZ0HBL81izpRaHBljrDo2eFkAaq2ihJn610wnia682IC21V5vu8ga627P68lY9T8A6Ec+T4gEG+i/1AIslBtAdH5zEkIooYLiTIkouExEysDJRLoRBPV4I81IfeC0Cqb8ww0xfUo8fZphxrS+ox49r1ECaCurxAyk1cl8K6vEjN2UqnN6gntxUIG2Tz5BzOlwVtgltDI2Tzhjytr7czT1v+9LdoPW2oN1Ndu8YwT0o8Y6Ctg+7Gsd5EQLLppFsjNC5XawepzjQphoRq/xhX2+JV+CxrSjFLGHZ1cziFulsqoKxy5DquuceCq3yyu6eSsl87XqPxXL/6jxwnv9/DpgJGnh/f/gBFVizjSPxtMoAAAAASUVORK5CYII=);background-position: 50%;
	background-repeat: no-repeat;
	background-size: cover
}

._30e-qR {
	background-image: url(data:image/png;
	base64,iVBORw0KGgoAAAANSUhEUgAAAHAAAABwCAMAAADxPgR5AAAAjVBMVEUAAADsblrqcFrqblrrblrpbVnrb1rrb1r7hXPvdF/rcFvqb1vrcFvrcFvrb1rtclzucl/5dWTrcFrrcFvvclzqcVzte17rcFvscVvscFvscVvrcVvscF3vdV7rb1rrb1vscFztcVzscVvrb1rwd17rcFvscVvqb1rrcFvrcFvqb1rqb1rrcFvsb1vqb1rZDUZoAAAALnRSTlMAZZnaMk2z+gUYz8eMcO0sHwrheSJJDaGCbFJLNhPkxEE5KKgQsEOU2PLpuL9arCjQDwAABLlJREFUaN681wl2qjAUBuAfqBBmkMGxdcSh9mX/y3s95IK0B2JQ6LcAQ+6UK/pJM1+/bZxpYFnB1NncdD9LMZI88xyLt7AcL8sxsHDuWlzCcuchBmP7Dlfg+DaGsDUtrsgyt3jVyuW9uCu8Yvk7loE7S5a7MHpn7D0Kd8tk5ga/I7vEs8Iv3vQx0yK0iLTZB2/6CvGM2Gvm7pLsIbFPLs1cejF6y6aNQJ7XeGh9bgR3mqEf5jVCuYihJF40Qusx9BDda9PQGJQxzbjXawRlk4CTq8/QC/OvnAQTKFrwysFGb/aBVxZQMq8zf8JTTlNO5lCgc1Ic8aRjwYmOh6qAWD5e4FtVUlTv97nGS9afXNDV8mekeFFq1HlUqE/Hxsts53GtTriwyTGAfMOFCTpEAd0vxyByumMQoRVzKX82BmJTHl2GNh7VZ4rBpFSrHlpk1H9rDGhN/Zi1vC00j3wMyqcpGXcFtMDAio6ghpb4kiMGdhSRs0L8RPvSCYM70WaFH5b1qB0ePQdLNIkWvdoYgX0V4wQNK0mFDlapK9y5YsQwKLK9zcazoYiJgeOituUlTfk81/jmKp+o8dIWFVPsnwyKPKPkQRET+6oJYlv0bqnaGKUN+u2Blv0jqUEMVQaBqjigomz2xBnjHYhzszNCXlqPeeCal8LG4nTBmAfiQgvVvQkTdDv6B/0kL5q3RTKJ0S25t2IuanSPTlH5eUXe3RZxYn7TbXTaizrN67H28fhJu6RdjW+fzZIsSqIVV/XLO5MElJNg2z7awoNJJEGd1e+w82ispbxyDdEiOpgVSUy1ujFECiN0o9WrKw6+WZmhWySSWH1/oPYH1UAL3axMICGGTYqM6lXG48RBC88kGmRc2hd9ipXUP0vyQi/NUrGF1Ix+Qae2l3srd69bjBZsXuYvBZG2vo4bbTgPxElRaAytWJYkyxhytKfdKLQ7jG5HxeLQGB9dSGU3pTYcGzXilNrjHaN7p4YXBc8wOkaj5q8P/POQKhRN/J84M2hxGASiMMoIEQomNA3dzUqjlPaU///3tlTroz044yHpd6sUwuiM8+ZpR0fRM0l/PJzV0vNJw5aFdZSwVfNfJw4ntiwu9cLvqVA55+tdv1jrhX9hrraOgK+oQA18/WpjLm8v+2DQINQvb6Y9eZJtqQZ/THtCA2a2lEuazH1gGjAkRjVEx5aFTiyMxGBF1DS62F87gT9Cv8fzT+REFGTipkAmQghvCoQwpP6GQOqf3ocZHkshdvg50LzI3E4MMxjXBJB5cJtKnqzqAfKSH9caB9LePAml+tSTKB9IG0fum0nkTe1UYpWP3G2mQm8yQy5RlYlNpgJsE2mApeetCFFum8iNIW8ysWyUylihMdRmfUWTmMv/BpUhmfXVaO6NJuGwFFTCycy9RvtymNMJlrNGiJPUvpQbtAjRvi0hQN6gbbegPQWaPpYWs1iZBb27yb73M8LuDyV7PwV9/7Hrv3o7NAIABoEgGI/H0H+fUUhECD9zfBu3L855hGApTbKM6KzL6hw4oKERLPwxz1t4gGeWKDER1hwz40K6GSrIxpDf3HMDaO2T3U0o+Z1db4TlYjpfz6M+B4QfxfL+YHl/sMb94QKtd3ujrQOdfAAAAABJRU5ErkJggg==);animation: spinning 1s linear infinite
}

._2qk-7T {
	background-image: url(data:image/png;
	base64,iVBORw0KGgoAAAANSUhEUgAAAHAAAABwCAMAAADxPgR5AAAAgVBMVEUAAADrb1rrcFvqb1vscFvrcVzrb1rscFvwdF//in7rb1rrcFvqcFrrb1vrcFvuc1zvc17rb1rrcFvrcFrrcFvrb1vrcVvwc1zyeVzse17/c2bscF3rcFvrb1rrcFvrcFvscVvrcVvtcVztcFvscVvsclvscF3tcV3rb1zsc1vqb1qqgzfAAAAAKnRSTlMA+s/HcEvtZhgF8+LelosrH+fIvbaheSISDQo2wKjVsIOBYVRRQ0I5JzXOPizQAAADRklEQVRo3ryX23qrIBCFB0ED1lMOxlPbpDlv3v8B98UQMW00GoH/rh/1mzBr1gJgGoXP4g31ooCQIPLoJmZ+AZao/MQj8gnES/wKDJPuKZEDELpPwRiceXIEHuNggsuGyJGQzQXm4lM5CerDHI6/exnQfHu6plkpRJml19M2p8Hvzh7fn5RP2WWZHzJ4QnbIl7LL53vzUydd7b63Nxjgtv3uapnUb4j30Wlk3MBLmrjT3I+pUoqk8zGrR/aEdX5kImACmZ7NaDHhS7GI9LxmE9rZNidkYmJrWNgKMbqtC3nni8Nk+Je8s4BR/LTineEtzq2UPzCCWCp2JbxJuZOKGF5ybwhhMANG7qKM3d+ygVk0S73HMfpFBcykiLSOr+fT4zAb7r2eVf+evhUYoLpnf68fs0DtrwIjVGqPQU/mCKr042AIrnSkAp6RqPkswBiFmtVkQEDSgEEa0itjrfKIgVGYSsm6r6E7MMyup6kpwV9SgmFK7BxJ4RHlmTMY56y8DQ8cddSaRR8HR+iCFg05WICHGCdPLMHACuyvNShGjAArCAwcCi0XHevm0YeQfuls0BICLCHQGptWVWJTQa0i4Q9/BvVgV1ZhuFpMX0Pq4GFL3svLB1+jCms+aU0Td52R4gf/oJ+1VKwnrWka/BfMtz2+x15NGbIYXOsHX3P7jgm30M9KtqwG1/rZaitWOKM36CeULeHgWj83nNOqjbUlDCA7DK4NsMR4a0/e3HbBHM/h1hQH2wUPrTFQwsx2wQxFBCgwZsB2QcCwKdTMUPsFqZoahjNjv2Cu4jRG29sviNaP1Vl4sl/whGeiau3VfsErDouyYWq/YKqMGKEN7RdEI0bKHqX9giUaXgWNsF9QYNQ4L+i8pc6HxrktnBvfebQ5D2/nx5PzA9j5FcP1Jcr5NdH5Rdj5Vd/5Y8b5c839g1Q/uf9XbwdVAIQwEEOFrAL8G9wzJx7QCakABDD5/YgvNz0q4LMJPgzR0xc+7tHzJT7Q0hM0PrLTGQEPJXQKeh+7wjnPECyjSdYRnXNZ3QMHMjTChT/qeYsP8NQSJSfCqmNmXkgHUMEVhhzrB2PGkH7ueQVau5DdM5TcjV3vwfKudD5wHACeP/zOAcLuAXk0BwAAAABJRU5ErkJggg==)
}

._35-1od {
	flex-grow: 1;
	margin: 0 16px;
	overflow: hidden
}

._2aoc2_ {
	font-size: 14px;
	font-weight: 500;
	margin-bottom: 2px;
	overflow: hidden;
	text-overflow: ellipsis;
	white-space: nowrap
}

.iWiJE9 {
	font-size: 12px
}

._2z_B4K {
	color: #da9e55;
	margin-right: 8px
}

._1jirLm,._3u_PHG {
	color: #969696
}

._1jirLm {
	flex-shrink: 0;
	font-size: 13px
}

._2rhmJa code,._2rhmJa pre,._2rhmJa pre[class*=language-] {
	font-family: Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace;
	font-size: 12px
}

._2rhmJa {
	font-weight: 400;
	line-height: 1.8;
	margin-bottom: 20px
}

._2rhmJa._3qEfO1 {
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none
}

._2rhmJa h1,._2rhmJa h2,._2rhmJa h3,._2rhmJa h4,._2rhmJa h5,._2rhmJa h6 {
	margin-bottom: 16px
}

._2rhmJa h1 {
	font-size: 26px
}

._2rhmJa h2 {
	font-size: 24px
}

._2rhmJa h3 {
	font-size: 22px
}

._2rhmJa h4 {
	font-size: 20px
}

._2rhmJa h5 {
	font-size: 18px
}

._2rhmJa h6 {
	font-size: 16px
}

._2rhmJa p {
	margin-bottom: 20px;
	word-break: break-word
}

._2rhmJa hr {
	margin: 0 0 20px;
	border: 0;
	border-top: 1px solid #eee!important
}

body.reader-night-mode ._2rhmJa hr {
	border-color: #2f2f2f!important
}

._2rhmJa blockquote {
	padding: 20px;
	background-color: #fafafa;
	border-left: 6px solid #e6e6e6;
	word-break: break-word;
	font-size: 16px;
	font-weight: normal;
	line-height: 30px;
	margin: 0 0 20px
}

body.reader-night-mode ._2rhmJa blockquote {
	background-color: #595959;
	border-color: #262626
}

._2rhmJa blockquote h1:last-child,._2rhmJa blockquote h2:last-child,._2rhmJa blockquote h3:last-child,._2rhmJa blockquote h4:last-child,._2rhmJa blockquote h5:last-child,._2rhmJa blockquote h6:last-child,._2rhmJa blockquote li:last-child,._2rhmJa blockquote ol:last-child,._2rhmJa blockquote p:last-child,._2rhmJa blockquote ul:last-child {
	margin-bottom: 0
}

._2rhmJa blockquote .image-package {
	width: 100%;
	margin-left: 0
}

._2rhmJa ol,._2rhmJa ul {
	word-break: break-word;
	margin: 0 0 20px 20px
}

._2rhmJa ol li,._2rhmJa ul li {
	line-height: 30px
}

._2rhmJa ol li ol,._2rhmJa ol li ul,._2rhmJa ul li ol,._2rhmJa ul li ul {
	margin-top: 16px
}

._2rhmJa ol {
	list-style-type: decimal
}

._2rhmJa ul {
	list-style-type: disc
}

._2rhmJa code {
	padding: 2px 4px;
	border: none;
	vertical-align: middle;
	white-space: pre-wrap
}

._2rhmJa :not(pre) code {
	color: #c7254e;
	background-color: #f2f2f2
}

body.reader-night-mode ._2rhmJa :not(pre) code {
	background-color: #262626
}

._2rhmJa pre,._2rhmJa pre[class*=language-] {
	word-wrap: normal;
	word-break: break-all;
	white-space: pre;
	overflow-x: scroll;
	overscroll-behavior-x: contain;
	margin-top: 0;
	margin-bottom: 20px;
	border-radius: 4px;
	z-index: 0;
	padding: 1em;
	line-height: 1.5;
	color: #ccc;
	background: #2d2d2d
}

._2rhmJa pre[class*=language-] code,._2rhmJa pre code {
	padding: 0;
	background-color: transparent;
	color: inherit;
	white-space: pre;
	vertical-align: unset
}

._2rhmJa table {
	width: 100%;
	margin-bottom: 20px;
	border-collapse: collapse;
	border: 1px solid #eee;
	border-left: none;
	word-break: break-word
}

body.reader-night-mode ._2rhmJa table,body.reader-night-mode ._2rhmJa table td,body.reader-night-mode ._2rhmJa table th {
	border-color: #2f2f2f
}

._2rhmJa table td,._2rhmJa table th {
	padding: 8px;
	border: 1px solid #eee;
	line-height: 20px;
	vertical-align: middle
}

._2rhmJa table th {
	font-weight: bold
}

._2rhmJa table thead th {
	vertical-align: middle;
	text-align: inherit
}

._2rhmJa table tr:nth-of-type(2n) {
	background-color: hsla(0,0%,70.2%,.15)
}

._2rhmJa table .image-package {
	width: 100%;
	margin-left: 0
}

._2rhmJa img {
	max-width: 100%
}

._2rhmJa .image-package {
	width: 100%;
	margin: 0;
	padding-bottom: 25px;
	text-align: center;
	font-size: 0
}

._2rhmJa .image-package img {
	max-width: 100%;
	width: auto;
	height: auto;
	vertical-align: middle;
	border: 0
}

body.reader-night-mode ._2rhmJa .image-package img {
	opacity: .85
}

._2rhmJa .image-package .image-container {
	position: relative;
	z-index: 95;
	background-color: #e6e6e6;
	transition: background-color .1s linear;
	margin: 0 auto
}

body.reader-night-mode ._2rhmJa .image-package .image-container {
	background-color: #595959
}

._2rhmJa .image-package .image-container-fill {
	z-index: 90
}

._2rhmJa .image-package .image-container .image-view {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	overflow: hidden
}

._2rhmJa .image-package .image-container .image-view-error {
	cursor: pointer;
	color: grey
}

body.reader-night-mode ._2rhmJa .image-package .image-container .image-view-error {
	color: #b3b3b3
}

._2rhmJa .image-package .image-container .image-view-error:after {
	content: "\56FE\7247\83B7\53D6\5931\8D25\FF0C\8BF7\70B9\51FB\91CD\8BD5";
	position: absolute;
	top: 50%;
	left: 50%;
	width: 100%;
	transform: translate(-50%,-50%);
	color: inherit;
	font-size: 14px
}

._2rhmJa .image-package .image-container .image-view img.image-loading {
	opacity: .3
}

._2rhmJa .image-package .image-container .image-view img {
	transition: all .15s linear;
	z-index: 95;
	opacity: 1
}

._2rhmJa .image-package .image-caption {
	min-width: 20%;
	max-width: 80%;
	min-height: 43px;
	display: inline-block;
	padding: 10px;
	margin: 0 auto;
	border-bottom: 1px solid #eee;
	font-size: 13px;
	color: #999
}

._2rhmJa .image-package .image-caption:empty {
	display: none
}

body.reader-night-mode ._2rhmJa .image-package .image-caption {
	border-color: #2f2f2f
}

._2rhmJa .math-block[mathimg="1"] {
	display: block;
	margin: 1em auto
}

._2rhmJa .math-inline[mathimg="1"] {
	display: inline;
	margin: 0 3px;
	vertical-align: middle
}

._2rhmJa .math-block[mathimg="1"],._2rhmJa .math-inline[mathimg="1"] {
	max-width: 100%
}

body.reader-night-mode ._2rhmJa .math-block[mathimg="1"],body.reader-night-mode ._2rhmJa .math-inline[mathimg="1"] {
	filter: invert(.8)
}

._3GbnS5 {
	padding: 0;
	line-height: 1.5;
	position: relative;
	width: 100%;
	height: 1px;
	margin: 20px 0;
	border: none;
	border-top: #b3b3b3;
	display: table;
	white-space: nowrap;
	text-align: center
}

._3GbnS5:after,._3GbnS5:before {
	content: "";
	display: table-cell;
	position: relative;
	top: 50%;
	left: 0;
	width: 50%;
	border-top: 1px solid;
	border-top-color: inherit;
	transform: scaleY(.5) translateY(50%);
	transform-origin: 50% 50% 0;
	transform-origin: initial
}

._2Lt-af {
	display: inline-block;
	padding: 0 12px;
	font-size: 14px;
	font-weight: normal;
	text-align: center;
	white-space: nowrap;
	color: #b3b3b3;
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none
}

._2Lt-af>a {
	margin-left: .5em
}

._19DgIp {
	width: 100%;
	height: 1px;
	margin: 16px 0;
	background-color: #eee
}

body.reader-night-mode ._19DgIp {
	background-color: #2f2f2f
}

._1kCBjS {
	justify-content: space-between;
	font-size: 14px;
	color: #969696;
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none
}

._1kCBjS,._3BUZPB,._18vaTa {
	display: flex;
	align-items: center
}

._3BUZPB>span {
	margin-left: 8px
}

._3BUZPB:not(:last-child) {
	margin-right: 12px
}

._2Bo4Th {
	display: flex;
	align-items: center;
	justify-content: center;
	width: 40px;
	height: 40px;
	color: #969696;
	border: 1px solid #eee;
	border-radius: 50%;
	font-size: 18px;
	cursor: pointer
}

body.reader-night-mode ._2Bo4Th {
	border-color: #2f2f2f
}


._1LOh_5 {
	cursor: pointer
}

._1LOh_5 .anticon {
	font-size: 12px
}

._1x1ok9 {
	cursor: pointer
}

._1x1ok9 .anticon {
	font-size: 16px
}

._1yN79W {
	background-color: #f2f2f2
}

._1yN79W:-ms-input-placeholder {
	color: #999
}

._1yN79W::-ms-input-placeholder {
	color: #999
}

._1yN79W::placeholder {
	color: #999
}

body.reader-night-mode ._1yN79W {
	background-color: #333
}

._3uZ5OL {
	text-align: center;
	padding: 48px 64px;
  ext-align: center;
  padding: 48px 64px;
  width: 50%;
  position: fixed;
  top: 0;
  height: 540px;
  border-radius: 5px;
  left: 0;
  right: 0;
  bottom: 0;
  background: #eee;
  z-index: 999;
  margin: auto;
}

._2PLkjk {
	display: flex;
	justify-content: center;
	align-items: center;
	margin-bottom: 24px
}

._2R1-48 {
	min-width: 50px;
	min-height: 50px;
	width: 50px;
	height: 50px;
	border-radius: 50%;
	border: 1px solid #eee
}

._2h5tnQ {
	font-size: 24px;
	font-weight: 500;
	margin-left: 16px
}

._1-bCJJ {
	flex-wrap: wrap
}

._1-bCJJ,.LMa6S_ {
	display: flex;
	justify-content: center
}

.LMa6S_ {
	align-items: center;
	width: 162.5px;
	height: 56px;
	font-size: 16px;
	color: #969696;
	margin-bottom: 12px;
	margin-right: 12px;
	border-radius: 10px;
	border: 1px solid #eee;
	cursor: pointer;
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none
}

body.reader-night-mode .LMa6S_ {
	border-color: #2f2f2f
}

.LMa6S_._1vONvL {
	color: #ec7259
}

.LMa6S_._1vONvL,body.reader-night-mode .LMa6S_._1vONvL {
	border-color: #ec7259
}

.LMa6S_._1sSZ6C {
	cursor: not-allowed;
	color: #969696;
	opacity: .5
}

.LMa6S_>i {
	font-size: 20px
}

.LMa6S_>span {
	font-size: 28px;
	font-style: italic
}

.LMa6S_:nth-child(3n) {
	margin-right: 0
}

.LMa6S_:nth-last-child(-n+3) {
	margin-bottom: 0
}

.LMa6S_:last-child {
	margin-right: 0
}

._2ByDWa>span {
	font-size: 16px;
	font-style: normal;
	opacity: 1
}

._2ByDWa>input {
	position: absolute;
	top: 50%;
	left: 50%;
	width: 100%;
	height: 36px;
	margin: 0 auto;
	text-align: center;
	transform: translate(-50%,-50%);
	background-color: transparent;
	opacity: 0;
	cursor: pointer
}

._2ByDWa>input::-webkit-inner-spin-button,._2ByDWa>input::-webkit-outer-spin-button {
	display: none
}

._2ByDWa._1vONvL>span {
	opacity: 0
}

._2ByDWa._1vONvL>input {
	opacity: 1;
	cursor: text
}

._3PA8BN>i {
	font-size: 30px
}

._3PA8BN>span {
	font-size: 16px;
	font-style: normal;
	margin-left: 4px
}

._3PA8BN,._3PA8BN._1vONvL {
	color: #404040
}

body.reader-night-mode ._3PA8BN,body.reader-night-mode ._3PA8BN._1vONvL {
	color: #b3b3b3
}

._1yN79W {
	display: block;
	width: 100%;
	height: 80px;
	resize: none;
	margin-top: 12px;
	padding: 12px;
	border: none;
	border-radius: 10px
}

._1_B577 {
	font-size: 15px;
	margin: 12px 0
}

._3A-4KL {
	margin-top: 24px;
	font-size: 18px;
	font-weight: normal;
	padding: 10px 48px
}

.d0hShY {
	display: flex;
	align-items: center;
	background-color: #fafafa;
	border-radius: 4px;
	padding: 12px 16px
}

body.reader-night-mode .d0hShY {
	background-color: #333
}

._27NmgV {
	border-radius: 50%;
	border: 1px solid #eee;
	min-width: 50px;
	min-height: 50px;
	width: 50px;
	height: 50px
}

body.reader-night-mode ._27NmgV {
	border-color: #2f2f2f
}

.Uz-vZq {
	flex-grow: 1;
	margin: 0 12px;
	overflow: hidden
}

.Cqpr1X {
	display: flex;
	align-items: center;
	margin-bottom: 2px
}

.HC3FFO {
	flex-shrink: 0;
	font-size: 16px;
	font-weight: 500
}

._3GlyHK,.HC3FFO {
	margin-right: 6px
}

._2WEj6j {
	font-size: 14px;
	color: #666;
	overflow: hidden;
	text-overflow: ellipsis;
	white-space: nowrap
}

.lJvI3S {
	font-size: 14px;
	color: #969696
}

.lJvI3S>span {
	margin-right: 12px
}

.lJvI3S>span:last-child {
	margin-right: 0
}

._3MbC71>span {
	margin-right: 6px
}

._14FSyQ,.H4XBOO>img {
	width: 24px;
	height: 24px;
	min-width: 24px;
	min-height: 24px;
	border-radius: 50%;
	border: 2px solid #fff
}

body.reader-night-mode .H4XBOO>img {
	border-color: #3d3d3d
}

._13lIbp {
	display: flex;
	flex-direction: column;
	align-items: center;
	margin: 40px 0 32px
}

._191KSt {
	font-size: 16px;
	font-weight: 500;
	margin-bottom: 16px
}

._3zdmIj {
	color: #666;
	margin-top: 24px
}



._37OvKa>i {
	color: #ec7259;
	font-size: 20px;
	margin-right: 8px
}


._3S34Y_>img {
	width: 144px;
	height: 144px;
	margin-bottom: 12px
}

._2JdSds>span {
	margin-left: 2px;
	pointer-events: none
}

._3yPTTQ>i {
	font-size: 16px;
	margin-right: 4px
}

</style>
```

## 动态展示文章详情

此时的文章详情页是静态的，不管文章 id 是多少，显示的都是同一个东西。这显然不符合我们的需求。我们希望对于指定的文章 id，页面显示的是对应文章的内容。

在当前页面中查询当前文章的基本内容。

服务端视图中提供 API 接口

```python
class ArticleDetailAPIView(RetrieveAPIView):
    queryset = models.Article.objects.filter(is_public=True)
    serializer_class = serializers.ArticleDetailModelSerializer
```

序列化器代码：

```python
class AuthorModelSerializer(serializers.ModelSerializer):
    """文章作者"""
    class Meta:
        model = models.User
        fields = ['id', 'nickname', 'avatar']


class ArticleDetailModelSerializer(serializers.ModelSerializer):
    """文章详情"""
    user = AuthorModelSerializer()
    class Meta:
        model = models.Article
        fields = [
            'user', 'render', 'name',
            'updated_time', 'access_pwd', 'read_count',
            'like_count', 'collect_count',
            'comment_count', 'reward_count', 'word_count',
        ]
```

路由代码：

```python
urlpatterns = [
    ...
    re_path('^detail/(?P<pk>\d+)/$', views.ArticleDetailAPIView.as_view()),
]
```

客户端代码：

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
            <button class="_1OyPqC _3Mi9q9 _2WY0RL _1YbC5u" type="button"><span>赞赏支持</span></button>
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
        data () {
            return {
                article_id: 0,
                token: '',
                article_detail: {
                    user: {}
                },
            }
        },
        filters: {
            time_format (time) {
                let t = new Date(time);
                return `${t.getFullYear()}.${t.getMonth()+1}.${t.getDate()} ${t.getHours()}:${t.getMinutes()}`;
            },
        },
        methods: {
            get_article_detail () {
                this.$axios.get(`${this.$settings.Host}/article/detail/${this.article_id}/`).then(response=>{
                    this.article_detail = response.data;
                }).catch(errors=>{
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

        }
    }
</script>
```

