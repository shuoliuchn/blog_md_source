## 荏苒资讯的文章编辑器开发

### 显示当前文章的标题和内容

当用户点击指定文章时，需要显示当前文章的标题和内容

Write.vue，组件代码：

```vue
<template>
  <div class="write" v-show="show_page">
    <div class="_2v5v5">
      <div class="_3zibT">
        <router-link to="/">回首页</router-link>
      </div>
      <div class="_1iZMb" @click.stop="">
        <div class="_33Zlg" @click="collection_form=!collection_form"><i class="fa fa-plus"></i><span>新建文集</span></div>
        <div class="_2G97m">
          <form class="M8J6Q" :class="collection_form?'_2a1Rp':'_1mU5v'">
            <input type="text" placeholder="请输入文集名..." v-model="collection_name" class="_1CtV4">
            <button type="submit" class="dwU8Q _3zXcJ _3QfkW" @click.prevent="create_collection">
              <span>提 交</span>
            </button>
            <button type="button" class="vIzwB _3zXcJ" @click="collection_form=false"><span>取 消</span></button>
          </form>
        </div>
      </div>
      <ul class="_3MbJ4 _3t059">
        <li class="_3DM7w" :class="{'_31PCv': (key === collection_key)}" :title="collection.name"
            v-for="(collection,key) in collections" :key="key" @click="select_collection(key)">
          <div class="_3P4JX _2VLy-" v-if="key === collection_key"
               @click.stop="collection_option">
            <i class="fa fa-gear"></i>
            <span>
              <ul class="_2V8zt _3FcHm _2w9pn" :class="{'NvfK4': show_collection_option}">
                <li class="_2po2r cRfUr" @click="edit_collection">
                  <span class=""><i class="fa fa-pencil-square-o _22XWG"></i>修改文集</span>
                </li>
                <li class="_2po2r cRfUr" @click="del_collection">
                  <span class=""><i class="fa fa-trash-o _22XWG"></i>删除文集</span>
                </li>
              </ul>
            </span>
          </div>
          <span>{{ collection.name }}</span>
        </li>
      </ul>
      <div style="height: 50px;"></div>
      <div role="button" class="h-5Am">
        <span class="ant-dropdown-trigger"><i class="fa fa-bars"></i><span>设置</span></span>
        <span class="Yv5Zx">遇到问题<i class="fa fa-question-circle-o"></i></span>
      </div>
    </div>
    <div class="rQQG7">
      <div class="_3revO _2mnPN">
        <div class="_3br9T">
          <div>
            <div class="_1GsW5" @click="create_article(false)"><i class="fa fa-plus-circle"></i><span> 新建文章</span></div>
            <ul class="_2TxA-">
              <li class="_25Ilv" :class="{_33nt7: key === article_key}" :title="article.name"
                  v-for="article,key in article_list" :key="key" @click="select_article(key)">
                <i class="_13kgp" :class="{_2m93u: article.is_public}"></i>
                <div class="_3P4JX poOXI" v-if="key === article_key" @click.stop="article_menu">
                  <i class="fa fa-gear"></i>
                  <span>
                    <ul class="_2V8zt _3FcHm _2w9pn" :class="{NvfK4: show_article_menu}">
                      <li class="_2po2r cRfUr" title="" @click="publish_article(true)" v-if="!article.is_public">
                        <span class=""><i class="fa fa-share _22XWG"></i>直接发布</span></li>
                      <li class="_2po2r cRfUr" title="" @click="publish_article(false)" v-else><span class=""><i
                        class="fa fa-lock _22XWG"></i>设为私密</span></li>
                      <li class="_2po2r cRfUr" title="" v-if="!article.is_public" @click="time_dialog_visible = true">
                        <span class=""><i class="fa fa-clock-o _22XWG"></i>定时发布</span>
                      </li>
                      <li class="_2po2r cRfUr" title=""><span class="_20tIi"><i class="iconfont ic-paid _22XWG"></i>发布为付费文章</span></li>
                      <li class="_2po2r cRfUr" title=""><span class=""><i
                        class="iconfont ic-set _22XWG"></i>设置发布样式</span></li>
                      <li class="_3nZXj _2_WAp _3df2u _2po2r cRfUr" title=""><span class=""><i
                        class="fa fa-folder-open _22XWG"></i>移动文章
                        <div class="_3x4X_">
                          <ul class="_2KzJx oGKRI _3DXDE _2w9pn">
                            <li class="_2po2r cRfUr" :title="collection.name" v-for="collection,key in collections"
                                :key="key" v-if="key !== collection_key" @click="move_article(key)">
                              <span class="">{{ collection.name }}</span>
                            </li>
                          </ul>
                        </div>
                      </span>
                      </li>
                      <li class="_2po2r cRfUr" title=""><span class=""><i
                        class="fa fa-history _22XWG"></i>历史版本</span></li>
                      <li class="_2po2r cRfUr" title="" @click="del_article"><span class=""><i
                        class="fa fa-trash-o _22XWG"></i>删除文章</span></li>
                      <li class="_2po2r cRfUr" title=""><span class=""><i
                        class="fa fa-ban _22XWG"></i>设置禁止转载</span></li>
                    </ul>
                  </span>
                </div>
                <span class="NariC">{{ article.name }}</span>
                <span class="hLzJv">{{ article.content }}</span>
                <span class="_29C-V">字数： {{ article.content?article.content.length:0 }}</span>
              </li>
            </ul>
            <div class="_2cVn3" @click="create_article(true)"><i class="fa fa-plus"></i><span> 在下方新建文章</span></div>
          </div>
        </div>
      </div>
      <input type="text" class="_24i7u" v-model="article_name" v-show="show_editor">
      <div id="editor" v-show="show_editor">
        <mavon-editor
          style="height: 100%"
          v-model="editorContent"
          :ishljs="true"
          ref=md
          @imgAdd="imgAdd"
          @imgDel="imgDel"
        ></mavon-editor>
        <el-dialog
          title="选择定时发文的时间："
          :visible.sync="time_dialog_visible"
          width="20%">
          <el-date-picker
            v-model="schedule_datetime"
            type="datetime"
            placeholder="选择日期时间"
            align="center"
            :picker-options="pickerOptions1">
          </el-date-picker>
          <div class="_3mEYS">本文章将于<span class="yfqan">{{time_format(schedule_datetime)}}</span>发布。</div>
          <span slot="footer" class="dialog-footer">
            <el-button @click="time_dialog_visible = false">取 消</el-button>
            <el-button type="primary" @click="shedule_publish_article">确 定</el-button>
          </span>
        </el-dialog>
      </div>
    </div>
  </div>
</template>
<script>
    import {mavonEditor} from 'mavon-editor'
    import 'mavon-editor/dist/css/index.css'

    export default {
        name: "Write",
        data() {
            return {
                editorContent: "",
                article_name: '',
                img_file: [],
                collection_form: false,
                show_page: false,
                token: '',
                collections: [],    // 当前用户的文集列表
                collection_key: 0,    // 默认让用户选中的文集的下标为0
                collection_name: '',    // 新建文集表单中的文集名称
                show_collection_option: false,    // 是否显示文集菜单
                article_list: [],    // 当前用户当前文集的文章列表
                article_key: 0,    // 默认让用户选中的文章的下标为0
                show_article_menu: false,    // 是否显示文章菜单
                show_editor: true,
                time_dialog_visible: false,
                schedule_datetime: new Date().toLocaleDateString(),    // 定时发布文章的事件设置
                pickerOptions1: {
                    disabledDate(time) {
                        return time.getTime() < Date.now();
                    },
                    shortcuts: [{
                        text: '今天',
                        onClick(picker) {
                            picker.$emit('pick', new Date());
                        }
                    }, {
                        text: '明天',
                        onClick(picker) {
                            const date = new Date();
                            date.setTime(date.getTime() + 3600 * 1000 * 24);
                            picker.$emit('pick', date);
                        }
                    }, {
                        text: '一周后',
                        onClick(picker) {
                            const date = new Date();
                            date.setTime(date.getTime() + 3600 * 1000 * 24 * 7);
                            picker.$emit('pick', date);
                        }
                    }]
                },
                value1: '',
                value2: '',
            }
        },
        watch: {
            editorContent() {
                console.log(this.editorContent)
            },
            collection_key () {
                this.get_article_content();
            },
            article_key () {
                this.get_article_content();
            }
        },
        mounted() {
            document.querySelector("#editor").style.height = document.documentElement.clientHeight - document.querySelector("._24i7u").clientHeight + "px";
            document.onclick = () => {
                this.collection_form = false;
                this.show_collection_option = false;
                this.show_article_menu = false;
            };
        },
        created() {
            this.token = this.$settings.check_login(this);
            this.show_page = !!this.token;
            this.show_page && this.get_collections();
        },
        components: {
            mavonEditor
        },
        methods: {
            get_collections() {
                this.$axios.get(`${this.$settings.Host}/article/collections/`, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    this.collections = response.data;
                    // 因为 axios 发送的是异步请求，所以需要把 get_article 方法写到这里
                    // 这样才能确保在获取到 collection 之后去获取文章内容
                    this.get_atricle();
                }).catch(errors => {
                    this.$settings.clear_user_info();
                    this.show_page = false;
                    this.$settings.check_login();
                })
            },
            create_collection() {
                if (!this.collection_name) {
                    this.$message.error('文集名不能为空！');
                    return
                }
                this.$axios.post(`${this.$settings.Host}/article/collections/`, {name: this.collection_name}, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    this.collections.unshift(response.data);
                    this.collection_form = false;
                    this.$message.success('创建成功！')
                }).catch(errors => {
                    this.$message.error('对不起，创建失败，请稍后重试！')
                })
            },
            edit_collection() {
                this.$prompt('请输入新文集名', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    inputValue: this.collections[this.collection_key].name,
                    inputPattern: /.+/,
                    inputErrorMessage: '内容不能为空！'
                }).then(({value}) => {
                    let collection_id = this.collections[this.collection_key].id;
                    this.$axios.put(`${this.$settings.Host}/article/collections/${collection_id}/`, {
                        name: value,
                    }, {
                        headers: {
                            Authorization: `jwt ${this.token}`,
                        }
                    }).then(response => {
                        this.collections[this.collection_key].name = response.data.name;
                        this.$message({
                            type: 'success',
                            message: '文集名已改为：' + value
                        });
                    }).catch(errors => {
                        this.$message.error('修改失败，请稍后重试！')
                    });
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '取消输入'
                    });
                });
            },
            del_collection() {
                let collection_id = this.collections[this.collection_key].id;
                this.$confirm(`此操作将永久删除文集 ${this.collections[this.collection_key].name}，是否继续？`, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.$axios.delete(`${this.$settings.Host}/article/collections/${collection_id}/`, {
                        headers: {
                            Authorization: `jwt ${this.token}`,
                        }
                    }).then(response => {
                        this.collections.splice(this.collection_key, 1);
                        this.$message.info('删除成功！');
                    }).catch(errors => {
                        this.$message.error('删除失败，请稍后重试！');
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            },
            select_collection(key) {
                this.collection_key = key;
                this.get_atricle();
            },
            collection_option() {
                this.show_collection_option = !this.show_collection_option;
                this.show_article_menu = false;
            },
            article_menu() {
                this.show_article_menu = !this.show_article_menu;
                this.show_collection_option = false;
            },
            get_atricle() {
                this.$axios.get(`${this.$settings.Host}/article/article/`, {
                    params: {
                        'collection_id': this.collections[this.collection_key].id,
                    },
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    this.article_list = response.data;
                    this.article_key = 0;
                    this.show_editor = !!response.data.length;
                    this.get_article_content();
                }).catch(errors => {
                    this.$message.error('获取文章信息失败，请稍后再试！')
                })
            },
            create_article(position) {
                this.$axios.post(`${this.$settings.Host}/article/article/`, {
                    collection: this.collections[this.collection_key].id,
                    position: position,
                }, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    if (position) {
                        this.article_list.push(response.data)
                    } else {
                        this.article_list.unshift(response.data)
                    }
                    this.show_editor = !!this.article_list.length;
                }).catch(errors => {
                    this.$message.error('添加文章失败，请稍后重试！')
                })
            },
            del_article() {
                let article_id = this.article_list[this.article_key].id;
                this.$confirm(`此操作将永久删除文章 ${this.article_list[this.article_key].name}，是否继续？`, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.$axios.delete(`${this.$settings.Host}/article/article/${article_id}/`, {
                        params: {
                            collection_id: this.collections[this.collection_key].id
                        },
                        headers: {
                            Authorization: `jwt ${this.token}`,
                        }
                    }).then(response => {
                        this.article_list.splice(this.article_key, 1);
                        this.show_editor = !!this.article_list.length;
                        this.$message.info('删除成功！');
                    }).catch(errors => {
                        this.$message.error('删除失败，请稍后重试！');
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            },
            publish_article(is_public) {
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
                        this.$message.success('发布成功！');
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
            shedule_publish_article() {
                this.schedule_datetime = new Date().toLocaleDateString();
                this.$axios.put(`${this.$settings.Host}/article/schedule/publish/`, {
                    pub_date: this.schedule_datetime,
                    article_id: this.article_list[this.article_key].id,
                }, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response=>{
                    this.$message.success('定时发布成功！');
                }).catch(errors=>{
                    this.$message.error('定时发送失败，请稍后重试！')
                });
                this.time_dialog_visible = false
            },
            time_format (time) {
                let t = new Date(time);
                return `${t.getFullYear()}-${t.getMonth()}-${t.getDate()} ${t.getHours()}:${t.getMinutes()}`;
            },
            select_article (key) {
                this.article_key = key;
                this.get_article_content();
            },
            get_article_content () {    // 填充当前编辑的文章标题和内容
                if (this.article_list.length && this.article_list[this.article_key].content) {
                    this.editorContent = this.article_list[this.article_key].content
                } else {
                    this.editorContent = ''
                }
                this.article_name = this.article_list.length?this.article_list[this.article_key].name:'';
            },
            // 绑定@imgAdd event
            imgAdd(pos, $file) {
                // 添加文件
            },
            imgDel(pos) {
                // 删除文件
            }
        },
    }
</script>
```

### 文章内容自动保存

当用户对于标题和内容进行修改时，我们需要时刻完成内容的提交。

客户端，代码：

```vue
<template>
  <div class="write" v-show="show_page">
    <div class="_2v5v5">
      <div class="_3zibT">
        <router-link to="/">回首页</router-link>
      </div>
      <div class="_1iZMb" @click.stop="">
        <div class="_33Zlg" @click="collection_form=!collection_form"><i class="fa fa-plus"></i><span>新建文集</span></div>
        <div class="_2G97m">
          <form class="M8J6Q" :class="collection_form?'_2a1Rp':'_1mU5v'">
            <input type="text" placeholder="请输入文集名..." v-model="collection_name" class="_1CtV4">
            <button type="submit" class="dwU8Q _3zXcJ _3QfkW" @click.prevent="create_collection">
              <span>提 交</span>
            </button>
            <button type="button" class="vIzwB _3zXcJ" @click="collection_form=false"><span>取 消</span></button>
          </form>
        </div>
      </div>
      <ul class="_3MbJ4 _3t059">
        <li class="_3DM7w" :class="{'_31PCv': (key === collection_key)}" :title="collection.name"
            v-for="(collection,key) in collections" :key="key" @click="select_collection(key)">
          <div class="_3P4JX _2VLy-" v-if="key === collection_key"
               @click.stop="collection_option">
            <i class="fa fa-gear"></i>
            <span>
              <ul class="_2V8zt _3FcHm _2w9pn" :class="{'NvfK4': show_collection_option}">
                <li class="_2po2r cRfUr" @click="edit_collection">
                  <span class=""><i class="fa fa-pencil-square-o _22XWG"></i>修改文集</span>
                </li>
                <li class="_2po2r cRfUr" @click="del_collection">
                  <span class=""><i class="fa fa-trash-o _22XWG"></i>删除文集</span>
                </li>
              </ul>
            </span>
          </div>
          <span>{{ collection.name }}</span>
        </li>
      </ul>
      <div style="height: 50px;"></div>
      <div role="button" class="h-5Am">
        <span class="ant-dropdown-trigger"><i class="fa fa-bars"></i><span>设置</span></span>
        <span class="Yv5Zx">遇到问题<i class="fa fa-question-circle-o"></i></span>
      </div>
    </div>
    <div class="rQQG7">
      <div class="_3revO _2mnPN">
        <div class="_3br9T">
          <div>
            <div class="_1GsW5" @click="create_article(false)"><i class="fa fa-plus-circle"></i><span> 新建文章</span></div>
            <ul class="_2TxA-">
              <li class="_25Ilv" :class="{_33nt7: key === article_key}" :title="article.name"
                  v-for="article,key in article_list" :key="key" @click="select_article(key)">
                <i class="_13kgp" :class="{_2m93u: article.is_public}"></i>
                <div class="_3P4JX poOXI" v-if="key === article_key" @click.stop="article_menu">
                  <i class="fa fa-gear"></i>
                  <span>
                    <ul class="_2V8zt _3FcHm _2w9pn" :class="{NvfK4: show_article_menu}">
                      <li class="_2po2r cRfUr" title="" @click="publish_article(true)" v-if="!article.is_public">
                        <span class=""><i class="fa fa-share _22XWG"></i>直接发布</span></li>
                      <li class="_2po2r cRfUr" title="" @click="publish_article(false)" v-else><span class=""><i
                        class="fa fa-lock _22XWG"></i>设为私密</span></li>
                      <li class="_2po2r cRfUr" title="" v-if="!article.is_public" @click="time_dialog_visible = true">
                        <span class=""><i class="fa fa-clock-o _22XWG"></i>定时发布</span>
                      </li>
                      <li class="_2po2r cRfUr" title=""><span class="_20tIi"><i class="iconfont ic-paid _22XWG"></i>发布为付费文章</span></li>
                      <li class="_2po2r cRfUr" title=""><span class=""><i
                        class="iconfont ic-set _22XWG"></i>设置发布样式</span></li>
                      <li class="_3nZXj _2_WAp _3df2u _2po2r cRfUr" title=""><span class=""><i
                        class="fa fa-folder-open _22XWG"></i>移动文章
                        <div class="_3x4X_">
                          <ul class="_2KzJx oGKRI _3DXDE _2w9pn">
                            <li class="_2po2r cRfUr" :title="collection.name" v-for="collection,key in collections"
                                :key="key" v-if="key !== collection_key" @click="move_article(key)">
                              <span class="">{{ collection.name }}</span>
                            </li>
                          </ul>
                        </div>
                      </span>
                      </li>
                      <li class="_2po2r cRfUr" title=""><span class=""><i
                        class="fa fa-history _22XWG"></i>历史版本</span></li>
                      <li class="_2po2r cRfUr" title="" @click="del_article"><span class=""><i
                        class="fa fa-trash-o _22XWG"></i>删除文章</span></li>
                      <li class="_2po2r cRfUr" title=""><span class=""><i
                        class="fa fa-ban _22XWG"></i>设置禁止转载</span></li>
                    </ul>
                  </span>
                </div>
                <span class="NariC">{{ article.name }}</span>
                <span class="hLzJv">{{ article.content }}</span>
                <span class="_29C-V">字数： {{ article.content?article.content.length:0 }}</span>
              </li>
            </ul>
            <div class="_2cVn3" @click="create_article(true)"><i class="fa fa-plus"></i><span> 在下方新建文章</span></div>
          </div>
        </div>
      </div>
      <input type="text" class="_24i7u" v-model="article_name" v-show="show_editor">
      <div id="editor" v-show="show_editor">
        <mavon-editor
          style="height: 100%"
          v-model="editor_content"
          @change="change_article"
          :ishljs="true"
          ref=md
          @imgAdd="imgAdd"
          @imgDel="imgDel"
        ></mavon-editor>
        <el-dialog
          title="选择定时发文的时间："
          :visible.sync="time_dialog_visible"
          width="20%">
          <el-date-picker
            v-model="schedule_datetime"
            type="datetime"
            placeholder="选择日期时间"
            align="center"
            :picker-options="pickerOptions1">
          </el-date-picker>
          <div class="_3mEYS">本文章将于<span class="yfqan">{{time_format(schedule_datetime)}}</span>发布。</div>
          <span slot="footer" class="dialog-footer">
            <el-button @click="time_dialog_visible = false">取 消</el-button>
            <el-button type="primary" @click="shedule_publish_article">确 定</el-button>
          </span>
        </el-dialog>
      </div>
    </div>
  </div>
</template>
<script>
    import {mavonEditor} from 'mavon-editor'
    import 'mavon-editor/dist/css/index.css'

    export default {
        name: "Write",
        data() {
            return {
                editor_content: "",
                editor_content_render: "",
                article_name: '',
                img_file: [],
                collection_form: false,
                show_page: false,
                token: '',
                collections: [],    // 当前用户的文集列表
                collection_key: 0,    // 默认让用户选中的文集的下标为0
                collection_name: '',    // 新建文集表单中的文集名称
                show_collection_option: false,    // 是否显示文集菜单
                article_list: [],    // 当前用户当前文集的文章列表
                article_key: 0,    // 默认让用户选中的文章的下标为0
                show_article_menu: false,    // 是否显示文章菜单
                show_editor: true,
                time_dialog_visible: false,
                schedule_datetime: new Date().toLocaleDateString(),    // 定时发布文章的事件设置
                pickerOptions1: {
                    disabledDate(time) {
                        return time.getTime() < Date.now();
                    },
                    shortcuts: [{
                        text: '今天',
                        onClick(picker) {
                            picker.$emit('pick', new Date());
                        }
                    }, {
                        text: '明天',
                        onClick(picker) {
                            const date = new Date();
                            date.setTime(date.getTime() + 3600 * 1000 * 24);
                            picker.$emit('pick', date);
                        }
                    }, {
                        text: '一周后',
                        onClick(picker) {
                            const date = new Date();
                            date.setTime(date.getTime() + 3600 * 1000 * 24 * 7);
                            picker.$emit('pick', date);
                        }
                    }]
                },
                value1: '',
                value2: '',
            }
        },
        watch: {
            collection_key () {
                this.get_article_content();
            },
            article_key () {
                this.get_article_content();
            },
            article_name () {
                this.save_article();
            },
        },
        mounted() {
            document.querySelector("#editor").style.height = document.documentElement.clientHeight - document.querySelector("._24i7u").clientHeight + "px";
            // 给当前网页绑定点击事件
            document.onclick = () => {
                this.collection_form = false;    // 隐藏添加文集输入框
                this.show_collection_option = false;    // 隐藏文集菜单
                this.show_article_menu = false;    // 隐藏文章选项菜单
            };
        },
        created() {
            this.token = this.$settings.check_login(this);
            this.show_page = !!this.token;
            this.show_page && this.get_collections();
        },
        components: {
            mavonEditor
        },
        methods: {
            get_collections() {
                this.$axios.get(`${this.$settings.Host}/article/collections/`, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    this.collections = response.data;
                    // 因为 axios 发送的是异步请求，所以需要把 get_article 方法写到这里
                    // 这样才能确保在获取到 collection 之后去获取文章内容
                    this.get_atricle();
                }).catch(errors => {
                    this.$settings.clear_user_info();
                    this.show_page = false;
                    this.$settings.check_login();
                })
            },
            create_collection() {
                if (!this.collection_name) {
                    this.$message.error('文集名不能为空！');
                    return
                }
                this.$axios.post(`${this.$settings.Host}/article/collections/`, {name: this.collection_name}, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    this.collections.unshift(response.data);
                    this.collection_form = false;
                    this.$message.success('创建成功！')
                }).catch(errors => {
                    this.$message.error('对不起，创建失败，请稍后重试！')
                })
            },
            edit_collection() {
                this.$prompt('请输入新文集名', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    inputValue: this.collections[this.collection_key].name,
                    inputPattern: /.+/,
                    inputErrorMessage: '内容不能为空！'
                }).then(({value}) => {
                    let collection_id = this.collections[this.collection_key].id;
                    this.$axios.put(`${this.$settings.Host}/article/collections/${collection_id}/`, {
                        name: value,
                    }, {
                        headers: {
                            Authorization: `jwt ${this.token}`,
                        }
                    }).then(response => {
                        this.collections[this.collection_key].name = response.data.name;
                        this.$message({
                            type: 'success',
                            message: '文集名已改为：' + value
                        });
                    }).catch(errors => {
                        this.$message.error('修改失败，请稍后重试！')
                    });
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '取消输入'
                    });
                });
            },
            del_collection() {
                let collection_id = this.collections[this.collection_key].id;
                this.$confirm(`此操作将永久删除文集 ${this.collections[this.collection_key].name}，是否继续？`, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.$axios.delete(`${this.$settings.Host}/article/collections/${collection_id}/`, {
                        headers: {
                            Authorization: `jwt ${this.token}`,
                        }
                    }).then(response => {
                        this.collections.splice(this.collection_key, 1);
                        this.$message.info('删除成功！');
                    }).catch(errors => {
                        this.$message.error('删除失败，请稍后重试！');
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            },
            select_collection(key) {
                this.collection_key = key;
                this.get_atricle();
            },
            collection_option() {
                this.show_collection_option = !this.show_collection_option;
                this.show_article_menu = false;
            },
            article_menu() {
                this.show_article_menu = !this.show_article_menu;
                this.show_collection_option = false;
            },
            get_atricle() {
                this.$axios.get(`${this.$settings.Host}/article/article/`, {
                    params: {
                        'collection_id': this.collections[this.collection_key].id,
                    },
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    this.article_list = response.data;
                    this.article_key = 0;
                    this.show_editor = !!response.data.length;
                    this.get_article_content();
                }).catch(errors => {
                    this.$message.error('获取文章信息失败，请稍后再试！')
                })
            },
            create_article(position) {
                this.$axios.post(`${this.$settings.Host}/article/article/`, {
                    collection: this.collections[this.collection_key].id,
                    position: position,
                }, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    if (position) {
                        this.article_list.push(response.data)
                    } else {
                        this.article_list.unshift(response.data)
                    }
                    this.show_editor = !!this.article_list.length;
                }).catch(errors => {
                    this.$message.error('添加文章失败，请稍后重试！')
                })
            },
            del_article() {
                let article_id = this.article_list[this.article_key].id;
                this.$confirm(`此操作将永久删除文章 ${this.article_list[this.article_key].name}，是否继续？`, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.$axios.delete(`${this.$settings.Host}/article/article/${article_id}/`, {
                        params: {
                            collection_id: this.collections[this.collection_key].id
                        },
                        headers: {
                            Authorization: `jwt ${this.token}`,
                        }
                    }).then(response => {
                        this.article_list.splice(this.article_key, 1);
                        this.show_editor = !!this.article_list.length;
                        this.$message.info('删除成功！');
                    }).catch(errors => {
                        this.$message.error('删除失败，请稍后重试！');
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            },
            publish_article(is_public) {
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
                        this.$message.success('发布成功！');
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
            shedule_publish_article() {
                this.schedule_datetime = new Date().toLocaleDateString();
                this.$axios.put(`${this.$settings.Host}/article/schedule/publish/`, {
                    pub_date: this.schedule_datetime,
                    article_id: this.article_list[this.article_key].id,
                }, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response=>{
                    this.$message.success('定时发布成功！');
                }).catch(errors=>{
                    this.$message.error('定时发送失败，请稍后重试！')
                });
                this.time_dialog_visible = false
            },
            time_format (time) {
                let t = new Date(time);
                return `${t.getFullYear()}-${t.getMonth()}-${t.getDate()} ${t.getHours()}:${t.getMinutes()}`;
            },
            select_article (key) {
                this.article_key = key;
                this.get_article_content();
            },
            get_article_content () {    // 填充当前编辑的文章标题和内容
                if (this.article_list.length && this.article_list[this.article_key].content) {
                    this.editor_content = this.article_list[this.article_key].content
                } else {
                    this.editor_content = ''
                }
                this.article_name = this.article_list.length?this.article_list[this.article_key].name:'';
            },
            change_article (value, render) {
                // 当文章内容发生改变时,自动执行的事件方法
                // console.log("原始内容：", value);
                // console.log("解析内容：", render);
                this.editor_content_render = render;
                this.save_article();
            },
            save_article () {
                let pk = this.article_list[this.article_key].id;
                this.$axios.put(`${this.$settings.Host}/article/article/save/${pk}/`, {
                    content: this.editor_content,
                    render: this.editor_content_render,
                    name: this.article_name,
                }, {
                    headers: {
                        Authorization: `jwt ${this.token}`,
                    }
                }).then(response=>{
                    this.$message.success('保存成功！')
                }).catch(errors=>{
                    this.$message.error('保存失败，请稍后重试！')
                })
            },
            // 绑定@imgAdd event
            imgAdd(pos, $file) {
                // 添加文件
            },
            imgDel(pos) {
                // 删除文件
            }
        },
    }
</script>
```

服务端提供修改文章内容和标题的 api 接口。

模型新增一个保存文章内容显示效果的字段 render。

```python
class Article(BaseModel):
    """文章模型"""
    content = models.TextField(null=True, blank=True, verbose_name="文章内容")
    render = models.TextField(null=True, blank=True, verbose_name="渲染后的文章内容")
    user = models.ForeignKey(User, on_delete=models.DO_NOTHING, verbose_name="作者")
    collection = models.ForeignKey(Collection, on_delete=models.CASCADE, verbose_name="文集")
    pub_date = models.DateTimeField(null=True, default=None, verbose_name="发布时间")
    access_pwd = models.CharField(max_length=15, null=True, blank=True, verbose_name="访问密码")
    read_count = models.IntegerField(default=0, null=True, blank=True, verbose_name="阅读量")
    like_count = models.IntegerField(default=0, null=True, blank=True, verbose_name="点赞量")
    collect_count = models.IntegerField(default=0, null=True, blank=True, verbose_name="收藏量")
    comment_count = models.IntegerField(default=0, null=True, blank=True, verbose_name="评论量")
    reward_count = models.IntegerField(default=0, null=True, blank=True, verbose_name="赞赏量")
    is_public = models.BooleanField(default=False, verbose_name="是否发布")

    class Meta:
        db_table = "rr_article"
        verbose_name = "文章"
        verbose_name_plural = verbose_name
```

数据迁移，

```python
python manage.py makemigrations
python manage.py migrate
```

视图代码:

```python
class ArticleSaveAPIView(APIView):
    permission_classes = [IsAuthenticated]
    def put(self, request, pk):
        user = request.user
        content = request.data.get('content')
        render = request.data.get('render')
        name = request.data.get('name')
        try:
            article = models.Article.objects.get(pk=pk, user=user)
        except models.Article.DoesNotExist:
            return Response({'error_msg': '要保存的文章不存在！'}, status=status.HTTP_400_BAD_REQUEST)
        article.content = content
        article.render = render
        article.name = name
        article.save()
        return Response('ok')
```

路由代码：

```python
urlpatterns = [
    ...
    re_path('^article/save/(?P<pk>\d+)/$', views.ArticleSaveAPIView.as_view()),
]
```

### 前端 ajax 节流

> 现在用户每一次粒度很小的操纵都会导致前端发送一次 ajax 请求，所以我们可以通过定时器 setTimeout 来让 ajax 延时发送请求。例如，当用户进行修改操作时，我们可以调用 setTimeout 来让 ajax 2 后秒发送请求。如果 2 秒内，用户有继续操作了文章，则重新计算 2 秒。 这种解决问题的思路，在前端里面叫函数节流/ajax 节流。

```vue
<template>
  <div class="write" v-show="show_page">
    <div class="_2v5v5">
      <div class="_3zibT">
        <router-link to="/">回首页</router-link>
      </div>
      <div class="_1iZMb" @click.stop="">
        <div class="_33Zlg" @click="collection_form=!collection_form"><i class="fa fa-plus"></i><span>新建文集</span></div>
        <div class="_2G97m">
          <form class="M8J6Q" :class="collection_form?'_2a1Rp':'_1mU5v'">
            <input type="text" placeholder="请输入文集名..." v-model="collection_name" class="_1CtV4">
            <button type="submit" class="dwU8Q _3zXcJ _3QfkW" @click.prevent="create_collection">
              <span>提 交</span>
            </button>
            <button type="button" class="vIzwB _3zXcJ" @click="collection_form=false"><span>取 消</span></button>
          </form>
        </div>
      </div>
      <ul class="_3MbJ4 _3t059">
        <li class="_3DM7w" :class="{'_31PCv': (key === collection_key)}" :title="collection.name"
            v-for="(collection,key) in collections" :key="key" @click="select_collection(key)">
          <div class="_3P4JX _2VLy-" v-if="key === collection_key"
               @click.stop="collection_option">
            <i class="fa fa-gear"></i>
            <span>
              <ul class="_2V8zt _3FcHm _2w9pn" :class="{'NvfK4': show_collection_option}">
                <li class="_2po2r cRfUr" @click="edit_collection">
                  <span class=""><i class="fa fa-pencil-square-o _22XWG"></i>修改文集</span>
                </li>
                <li class="_2po2r cRfUr" @click="del_collection">
                  <span class=""><i class="fa fa-trash-o _22XWG"></i>删除文集</span>
                </li>
              </ul>
            </span>
          </div>
          <span>{{ collection.name }}</span>
        </li>
      </ul>
      <div style="height: 50px;"></div>
      <div role="button" class="h-5Am">
        <span class="ant-dropdown-trigger"><i class="fa fa-bars"></i><span>设置</span></span>
        <span class="Yv5Zx">遇到问题<i class="fa fa-question-circle-o"></i></span>
      </div>
    </div>
    <div class="rQQG7">
      <div class="_3revO _2mnPN">
        <div class="_3br9T">
          <div>
            <div class="_1GsW5" @click="create_article(false)"><i class="fa fa-plus-circle"></i><span> 新建文章</span></div>
            <ul class="_2TxA-">
              <li class="_25Ilv" :class="{_33nt7: key === article_key}" :title="article.name"
                  v-for="article,key in article_list" :key="key" @click="select_article(key)">
                <i class="_13kgp" :class="{_2m93u: article.is_public}"></i>
                <div class="_3P4JX poOXI" v-if="key === article_key" @click.stop="article_menu">
                  <i class="fa fa-gear"></i>
                  <span>
                    <ul class="_2V8zt _3FcHm _2w9pn" :class="{NvfK4: show_article_menu}">
                      <li class="_2po2r cRfUr" title="" @click="publish_article(true)" v-if="!article.is_public">
                        <span class=""><i class="fa fa-share _22XWG"></i>直接发布</span></li>
                      <li class="_2po2r cRfUr" title="" @click="publish_article(false)" v-else><span class=""><i
                        class="fa fa-lock _22XWG"></i>设为私密</span></li>
                      <li class="_2po2r cRfUr" title="" v-if="!article.is_public" @click="time_dialog_visible = true">
                        <span class=""><i class="fa fa-clock-o _22XWG"></i>定时发布</span>
                      </li>
                      <li class="_2po2r cRfUr" title=""><span class="_20tIi"><i class="iconfont ic-paid _22XWG"></i>发布为付费文章</span></li>
                      <li class="_2po2r cRfUr" title=""><span class=""><i
                        class="iconfont ic-set _22XWG"></i>设置发布样式</span></li>
                      <li class="_3nZXj _2_WAp _3df2u _2po2r cRfUr" title=""><span class=""><i
                        class="fa fa-folder-open _22XWG"></i>移动文章
                        <div class="_3x4X_">
                          <ul class="_2KzJx oGKRI _3DXDE _2w9pn">
                            <li class="_2po2r cRfUr" :title="collection.name" v-for="collection,key in collections"
                                :key="key" v-if="key !== collection_key" @click="move_article(key)">
                              <span class="">{{ collection.name }}</span>
                            </li>
                          </ul>
                        </div>
                      </span>
                      </li>
                      <li class="_2po2r cRfUr" title=""><span class=""><i
                        class="fa fa-history _22XWG"></i>历史版本</span></li>
                      <li class="_2po2r cRfUr" title="" @click="del_article"><span class=""><i
                        class="fa fa-trash-o _22XWG"></i>删除文章</span></li>
                      <li class="_2po2r cRfUr" title=""><span class=""><i
                        class="fa fa-ban _22XWG"></i>设置禁止转载</span></li>
                    </ul>
                  </span>
                </div>
                <span class="NariC">{{ article.name }}</span>
                <span class="hLzJv">{{ article.content }}</span>
                <span class="_29C-V">字数： {{ article.content?article.content.length:0 }}</span>
              </li>
            </ul>
            <div class="_2cVn3" @click="create_article(true)"><i class="fa fa-plus"></i><span> 在下方新建文章</span></div>
          </div>
        </div>
      </div>
      <input type="text" class="_24i7u" v-model="article_name" v-show="show_editor">
      <div id="editor" v-show="show_editor">
        <mavon-editor
          style="height: 100%"
          v-model="editor_content"
          @change="change_article"
          :ishljs="true"
          ref=md
          @imgAdd="imgAdd"
          @imgDel="imgDel"
        ></mavon-editor>
        <el-dialog
          title="选择定时发文的时间："
          :visible.sync="time_dialog_visible"
          width="20%">
          <el-date-picker
            v-model="schedule_datetime"
            type="datetime"
            placeholder="选择日期时间"
            align="center"
            :picker-options="pickerOptions1">
          </el-date-picker>
          <div class="_3mEYS">本文章将于<span class="yfqan">{{time_format(schedule_datetime)}}</span>发布。</div>
          <span slot="footer" class="dialog-footer">
            <el-button @click="time_dialog_visible = false">取 消</el-button>
            <el-button type="primary" @click="shedule_publish_article">确 定</el-button>
          </span>
        </el-dialog>
      </div>
    </div>
  </div>
</template>
<script>
    import {mavonEditor} from 'mavon-editor'
    import 'mavon-editor/dist/css/index.css'

    export default {
        name: "Write",
        data() {
            return {
                editor_content: "",
                editor_content_render: "",
                article_name: '',
                img_file: [],
                collection_form: false,
                show_page: false,
                token: '',
                collections: [],    // 当前用户的文集列表
                collection_key: 0,    // 默认让用户选中的文集的下标为0
                collection_name: '',    // 新建文集表单中的文集名称
                show_collection_option: false,    // 是否显示文集菜单
                article_list: [],    // 当前用户当前文集的文章列表
                article_key: 0,    // 默认让用户选中的文章的下标为0
                show_article_menu: false,    // 是否显示文章菜单
                show_editor: true,
                time_dialog_visible: false,
                schedule_datetime: new Date().toLocaleDateString(),    // 定时发布文章的事件设置
                pickerOptions1: {
                    disabledDate(time) {
                        return time.getTime() < Date.now();
                    },
                    shortcuts: [{
                        text: '今天',
                        onClick(picker) {
                            picker.$emit('pick', new Date());
                        }
                    }, {
                        text: '明天',
                        onClick(picker) {
                            const date = new Date();
                            date.setTime(date.getTime() + 3600 * 1000 * 24);
                            picker.$emit('pick', date);
                        }
                    }, {
                        text: '一周后',
                        onClick(picker) {
                            const date = new Date();
                            date.setTime(date.getTime() + 3600 * 1000 * 24 * 7);
                            picker.$emit('pick', date);
                        }
                    }]
                },
                value1: '',
                value2: '',
                timer_id: 0,    // 自动保存文章的计时器id
            }
        },
        watch: {
            collection_key () {
                this.get_article_content();
            },
            article_key () {
                this.get_article_content();
            },
            article_name () {
                this.save_article();
            },
        },
        mounted() {
            document.querySelector("#editor").style.height = document.documentElement.clientHeight - document.querySelector("._24i7u").clientHeight + "px";
            // 给当前网页绑定点击事件
            document.onclick = () => {
                this.collection_form = false;    // 隐藏添加文集输入框
                this.show_collection_option = false;    // 隐藏文集菜单
                this.show_article_menu = false;    // 隐藏文章选项菜单
            };
        },
        created() {
            this.token = this.$settings.check_login(this);
            this.show_page = !!this.token;
            this.show_page && this.get_collections();
        },
        components: {
            mavonEditor
        },
        methods: {
            get_collections() {
                this.$axios.get(`${this.$settings.Host}/article/collections/`, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    this.collections = response.data;
                    // 因为 axios 发送的是异步请求，所以需要把 get_article 方法写到这里
                    // 这样才能确保在获取到 collection 之后去获取文章内容
                    this.get_atricle();
                }).catch(errors => {
                    this.$settings.clear_user_info();
                    this.show_page = false;
                    this.$settings.check_login();
                })
            },
            create_collection() {
                if (!this.collection_name) {
                    this.$message.error('文集名不能为空！');
                    return
                }
                this.$axios.post(`${this.$settings.Host}/article/collections/`, {name: this.collection_name}, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    this.collections.unshift(response.data);
                    this.collection_form = false;
                    this.$message.success('创建成功！')
                }).catch(errors => {
                    this.$message.error('对不起，创建失败，请稍后重试！')
                })
            },
            edit_collection() {
                this.$prompt('请输入新文集名', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    inputValue: this.collections[this.collection_key].name,
                    inputPattern: /.+/,
                    inputErrorMessage: '内容不能为空！'
                }).then(({value}) => {
                    let collection_id = this.collections[this.collection_key].id;
                    this.$axios.put(`${this.$settings.Host}/article/collections/${collection_id}/`, {
                        name: value,
                    }, {
                        headers: {
                            Authorization: `jwt ${this.token}`,
                        }
                    }).then(response => {
                        this.collections[this.collection_key].name = response.data.name;
                        this.$message({
                            type: 'success',
                            message: '文集名已改为：' + value
                        });
                    }).catch(errors => {
                        this.$message.error('修改失败，请稍后重试！')
                    });
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '取消输入'
                    });
                });
            },
            del_collection() {
                let collection_id = this.collections[this.collection_key].id;
                this.$confirm(`此操作将永久删除文集 ${this.collections[this.collection_key].name}，是否继续？`, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.$axios.delete(`${this.$settings.Host}/article/collections/${collection_id}/`, {
                        headers: {
                            Authorization: `jwt ${this.token}`,
                        }
                    }).then(response => {
                        this.collections.splice(this.collection_key, 1);
                        this.$message.info('删除成功！');
                    }).catch(errors => {
                        this.$message.error('删除失败，请稍后重试！');
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            },
            select_collection(key) {
                this.collection_key = key;
                this.get_atricle();
            },
            collection_option() {
                this.show_collection_option = !this.show_collection_option;
                this.show_article_menu = false;
            },
            article_menu() {
                this.show_article_menu = !this.show_article_menu;
                this.show_collection_option = false;
            },
            get_atricle() {
                this.$axios.get(`${this.$settings.Host}/article/article/`, {
                    params: {
                        'collection_id': this.collections[this.collection_key].id,
                    },
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    this.article_list = response.data;
                    this.article_key = 0;
                    this.show_editor = !!response.data.length;
                    this.get_article_content();
                }).catch(errors => {
                    this.$message.error('获取文章信息失败，请稍后再试！')
                })
            },
            create_article(position) {
                this.$axios.post(`${this.$settings.Host}/article/article/`, {
                    collection: this.collections[this.collection_key].id,
                    position: position,
                }, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    if (position) {
                        this.article_list.push(response.data)
                    } else {
                        this.article_list.unshift(response.data)
                    }
                    this.show_editor = !!this.article_list.length;
                }).catch(errors => {
                    this.$message.error('添加文章失败，请稍后重试！')
                })
            },
            del_article() {
                let article_id = this.article_list[this.article_key].id;
                this.$confirm(`此操作将永久删除文章 ${this.article_list[this.article_key].name}，是否继续？`, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.$axios.delete(`${this.$settings.Host}/article/article/${article_id}/`, {
                        params: {
                            collection_id: this.collections[this.collection_key].id
                        },
                        headers: {
                            Authorization: `jwt ${this.token}`,
                        }
                    }).then(response => {
                        this.article_list.splice(this.article_key, 1);
                        this.show_editor = !!this.article_list.length;
                        this.$message.info('删除成功！');
                    }).catch(errors => {
                        this.$message.error('删除失败，请稍后重试！');
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            },
            publish_article(is_public) {
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
                        this.$message.success('发布成功！');
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
            shedule_publish_article() {
                this.schedule_datetime = new Date().toLocaleDateString();
                this.$axios.put(`${this.$settings.Host}/article/schedule/publish/`, {
                    pub_date: this.schedule_datetime,
                    article_id: this.article_list[this.article_key].id,
                }, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response=>{
                    this.$message.success('定时发布成功！');
                }).catch(errors=>{
                    this.$message.error('定时发送失败，请稍后重试！')
                });
                this.time_dialog_visible = false
            },
            time_format (time) {
                let t = new Date(time);
                return `${t.getFullYear()}-${t.getMonth()}-${t.getDate()} ${t.getHours()}:${t.getMinutes()}`;
            },
            select_article (key) {
                this.article_key = key;
                this.get_article_content();
            },
            get_article_content () {    // 填充当前编辑的文章标题和内容
                if (this.article_list.length && this.article_list[this.article_key].content) {
                    this.editor_content = this.article_list[this.article_key].content
                } else {
                    this.editor_content = ''
                }
                this.article_name = this.article_list.length?this.article_list[this.article_key].name:'';
            },
            change_article (value, render) {
                // 当文章内容发生改变时,自动执行的事件方法
                // console.log("原始内容：", value);
                // console.log("解析内容：", render);
                this.editor_content_render = render;
                clearTimeout(this.timer_id);
                this.timer_id = setTimeout(this.save_article, 1000);
            },
            save_article () {
                let pk = this.article_list[this.article_key].id;
                this.$axios.put(`${this.$settings.Host}/article/article/save/${pk}/`, {
                    content: this.editor_content,
                    render: this.editor_content_render,
                    name: this.article_name,
                }, {
                    headers: {
                        Authorization: `jwt ${this.token}`,
                    }
                }).then(response=>{
                    this.$message.success('保存成功！')
                }).catch(errors=>{
                    this.$message.error('保存失败，请稍后重试！')
                })
            },
            // 绑定@imgAdd event
            imgAdd(pos, $file) {
                // 添加文件
            },
            imgDel(pos) {
                // 删除文件
            }
        },
    }
</script>
```

### 完善富文本编辑器添加图片到服务端功能

服务端实现上传图片的 API 接口

视图代码：

```python
class ArticleImageAPIView(CreateAPIView):
    permission_classes = [IsAuthenticated]
    queryset = models.ArticleImage.objects.all()
    serializer_class = serializers.ArticleImageModelSerializer
```

序列化器：

```python
class ArticleImageModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = models.ArticleImage
        fields = ['image']

    def create(self, validated_data):
        user_id = self.context['request'].user.id
        img_obj = models.ArticleImage.objects.create(
            image=validated_data.get('image'),
            user=user_id
        )
        img_obj.group = str(img_obj.image).split('/', 1)[0]
        img_obj.save()
        return img_obj
```

路由代码：

```python
urlpatterns = [
    ...
    path('image/', views.ArticleImageAPIView.as_view()),
]
```

客户端实现在富文本编辑器中用户添加图片时直接上传文件到服务端 FastDFS 系统。

```vue
<template>
  <div class="write" v-show="show_page">
    <div class="_2v5v5">
      <div class="_3zibT">
        <router-link to="/">回首页</router-link>
      </div>
      <div class="_1iZMb" @click.stop="">
        <div class="_33Zlg" @click="collection_form=!collection_form"><i class="fa fa-plus"></i><span>新建文集</span></div>
        <div class="_2G97m">
          <form class="M8J6Q" :class="collection_form?'_2a1Rp':'_1mU5v'">
            <input type="text" placeholder="请输入文集名..." v-model="collection_name" class="_1CtV4">
            <button type="submit" class="dwU8Q _3zXcJ _3QfkW" @click.prevent="create_collection">
              <span>提 交</span>
            </button>
            <button type="button" class="vIzwB _3zXcJ" @click="collection_form=false"><span>取 消</span></button>
          </form>
        </div>
      </div>
      <ul class="_3MbJ4 _3t059">
        <li class="_3DM7w" :class="{'_31PCv': (key === collection_key)}" :title="collection.name"
            v-for="(collection,key) in collections" :key="key" @click="select_collection(key)">
          <div class="_3P4JX _2VLy-" v-if="key === collection_key"
               @click.stop="collection_option">
            <i class="fa fa-gear"></i>
            <span>
              <ul class="_2V8zt _3FcHm _2w9pn" :class="{'NvfK4': show_collection_option}">
                <li class="_2po2r cRfUr" @click="edit_collection">
                  <span class=""><i class="fa fa-pencil-square-o _22XWG"></i>修改文集</span>
                </li>
                <li class="_2po2r cRfUr" @click="del_collection">
                  <span class=""><i class="fa fa-trash-o _22XWG"></i>删除文集</span>
                </li>
              </ul>
            </span>
          </div>
          <span>{{ collection.name }}</span>
        </li>
      </ul>
      <div style="height: 50px;"></div>
      <div role="button" class="h-5Am">
        <span class="ant-dropdown-trigger"><i class="fa fa-bars"></i><span>设置</span></span>
        <span class="Yv5Zx">遇到问题<i class="fa fa-question-circle-o"></i></span>
      </div>
    </div>
    <div class="rQQG7">
      <div class="_3revO _2mnPN">
        <div class="_3br9T">
          <div>
            <div class="_1GsW5" @click="create_article(false)"><i class="fa fa-plus-circle"></i><span> 新建文章</span></div>
            <ul class="_2TxA-">
              <li class="_25Ilv" :class="{_33nt7: key === article_key}" :title="article.name"
                  v-for="article,key in article_list" :key="key" @click="select_article(key)">
                <i class="_13kgp" :class="{_2m93u: article.is_public}"></i>
                <div class="_3P4JX poOXI" v-if="key === article_key" @click.stop="article_menu">
                  <i class="fa fa-gear"></i>
                  <span>
                    <ul class="_2V8zt _3FcHm _2w9pn" :class="{NvfK4: show_article_menu}">
                      <li class="_2po2r cRfUr" title="" @click="publish_article(true)" v-if="!article.is_public">
                        <span class=""><i class="fa fa-share _22XWG"></i>直接发布</span></li>
                      <li class="_2po2r cRfUr" title="" @click="publish_article(false)" v-else><span class=""><i
                        class="fa fa-lock _22XWG"></i>设为私密</span></li>
                      <li class="_2po2r cRfUr" title="" v-if="!article.is_public" @click="time_dialog_visible = true">
                        <span class=""><i class="fa fa-clock-o _22XWG"></i>定时发布</span>
                      </li>
                      <li class="_2po2r cRfUr" title=""><span class="_20tIi"><i class="iconfont ic-paid _22XWG"></i>发布为付费文章</span></li>
                      <li class="_2po2r cRfUr" title=""><span class=""><i
                        class="iconfont ic-set _22XWG"></i>设置发布样式</span></li>
                      <li class="_3nZXj _2_WAp _3df2u _2po2r cRfUr" title=""><span class=""><i
                        class="fa fa-folder-open _22XWG"></i>移动文章
                        <div class="_3x4X_">
                          <ul class="_2KzJx oGKRI _3DXDE _2w9pn">
                            <li class="_2po2r cRfUr" :title="collection.name" v-for="collection,key in collections"
                                :key="key" v-if="key !== collection_key" @click="move_article(key)">
                              <span class="">{{ collection.name }}</span>
                            </li>
                          </ul>
                        </div>
                      </span>
                      </li>
                      <li class="_2po2r cRfUr" title=""><span class=""><i
                        class="fa fa-history _22XWG"></i>历史版本</span></li>
                      <li class="_2po2r cRfUr" title="" @click="del_article"><span class=""><i
                        class="fa fa-trash-o _22XWG"></i>删除文章</span></li>
                      <li class="_2po2r cRfUr" title=""><span class=""><i
                        class="fa fa-ban _22XWG"></i>设置禁止转载</span></li>
                    </ul>
                  </span>
                </div>
                <span class="NariC">{{ article.name }}</span>
                <span class="hLzJv">{{ article.content }}</span>
                <span class="_29C-V">字数： {{ article.content?article.content.length:0 }}</span>
              </li>
            </ul>
            <div class="_2cVn3" @click="create_article(true)"><i class="fa fa-plus"></i><span> 在下方新建文章</span></div>
          </div>
        </div>
      </div>
      <input type="text" class="_24i7u" v-model="article_name" v-show="show_editor">
      <div id="editor" v-show="show_editor">
        <mavon-editor
          style="height: 100%"
          v-model="editor_content"
          @change="change_article"
          :ishljs="true"
          ref=md
          @imgAdd="img_add"
          @imgDel="img_del"
        ></mavon-editor>
        <el-dialog
          title="选择定时发文的时间："
          :visible.sync="time_dialog_visible"
          width="20%">
          <el-date-picker
            v-model="schedule_datetime"
            type="datetime"
            placeholder="选择日期时间"
            align="center"
            :picker-options="pickerOptions1">
          </el-date-picker>
          <div class="_3mEYS">本文章将于<span class="yfqan">{{time_format(schedule_datetime)}}</span>发布。</div>
          <span slot="footer" class="dialog-footer">
            <el-button @click="time_dialog_visible = false">取 消</el-button>
            <el-button type="primary" @click="shedule_publish_article">确 定</el-button>
          </span>
        </el-dialog>
      </div>
    </div>
  </div>
</template>
<script>
    import {mavonEditor} from 'mavon-editor'
    import 'mavon-editor/dist/css/index.css'

    export default {
        name: "Write",
        data() {
            return {
                editor_content: "",
                editor_content_render: "",
                article_name: '',
                img_file: [],
                collection_form: false,
                show_page: false,
                token: '',
                collections: [],    // 当前用户的文集列表
                collection_key: 0,    // 默认让用户选中的文集的下标为0
                collection_name: '',    // 新建文集表单中的文集名称
                show_collection_option: false,    // 是否显示文集菜单
                article_list: [],    // 当前用户当前文集的文章列表
                article_key: 0,    // 默认让用户选中的文章的下标为0
                show_article_menu: false,    // 是否显示文章菜单
                show_editor: true,
                time_dialog_visible: false,
                schedule_datetime: new Date().toLocaleDateString(),    // 定时发布文章的事件设置
                pickerOptions1: {
                    disabledDate(time) {
                        return time.getTime() < Date.now();
                    },
                    shortcuts: [{
                        text: '今天',
                        onClick(picker) {
                            picker.$emit('pick', new Date());
                        }
                    }, {
                        text: '明天',
                        onClick(picker) {
                            const date = new Date();
                            date.setTime(date.getTime() + 3600 * 1000 * 24);
                            picker.$emit('pick', date);
                        }
                    }, {
                        text: '一周后',
                        onClick(picker) {
                            const date = new Date();
                            date.setTime(date.getTime() + 3600 * 1000 * 24 * 7);
                            picker.$emit('pick', date);
                        }
                    }]
                },
                value1: '',
                value2: '',
                timer_id: 0,    // 自动保存文章的计时器id
            }
        },
        watch: {
            collection_key () {
                this.get_article_content();
            },
            article_key () {
                this.get_article_content();
            },
            article_name () {
                this.save_article();
            },
        },
        mounted() {
            document.querySelector("#editor").style.height = document.documentElement.clientHeight - document.querySelector("._24i7u").clientHeight + "px";
            // 给当前网页绑定点击事件
            document.onclick = () => {
                this.collection_form = false;    // 隐藏添加文集输入框
                this.show_collection_option = false;    // 隐藏文集菜单
                this.show_article_menu = false;    // 隐藏文章选项菜单
            };
        },
        created() {
            this.token = this.$settings.check_login(this);
            this.show_page = !!this.token;
            this.show_page && this.get_collections();
        },
        components: {
            mavonEditor
        },
        methods: {
            get_collections() {
                this.$axios.get(`${this.$settings.Host}/article/collections/`, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    this.collections = response.data;
                    // 因为 axios 发送的是异步请求，所以需要把 get_article 方法写到这里
                    // 这样才能确保在获取到 collection 之后去获取文章内容
                    this.get_atricle();
                }).catch(errors => {
                    this.$settings.clear_user_info();
                    this.show_page = false;
                    this.$settings.check_login();
                })
            },
            create_collection() {
                if (!this.collection_name) {
                    this.$message.error('文集名不能为空！');
                    return
                }
                this.$axios.post(`${this.$settings.Host}/article/collections/`, {name: this.collection_name}, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    this.collections.unshift(response.data);
                    this.collection_form = false;
                    this.$message.success('创建成功！')
                }).catch(errors => {
                    this.$message.error('对不起，创建失败，请稍后重试！')
                })
            },
            edit_collection() {
                this.$prompt('请输入新文集名', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    inputValue: this.collections[this.collection_key].name,
                    inputPattern: /.+/,
                    inputErrorMessage: '内容不能为空！'
                }).then(({value}) => {
                    let collection_id = this.collections[this.collection_key].id;
                    this.$axios.put(`${this.$settings.Host}/article/collections/${collection_id}/`, {
                        name: value,
                    }, {
                        headers: {
                            Authorization: `jwt ${this.token}`,
                        }
                    }).then(response => {
                        this.collections[this.collection_key].name = response.data.name;
                        this.$message({
                            type: 'success',
                            message: '文集名已改为：' + value
                        });
                    }).catch(errors => {
                        this.$message.error('修改失败，请稍后重试！')
                    });
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '取消输入'
                    });
                });
            },
            del_collection() {
                let collection_id = this.collections[this.collection_key].id;
                this.$confirm(`此操作将永久删除文集 ${this.collections[this.collection_key].name}，是否继续？`, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.$axios.delete(`${this.$settings.Host}/article/collections/${collection_id}/`, {
                        headers: {
                            Authorization: `jwt ${this.token}`,
                        }
                    }).then(response => {
                        this.collections.splice(this.collection_key, 1);
                        this.$message.info('删除成功！');
                    }).catch(errors => {
                        this.$message.error('删除失败，请稍后重试！');
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            },
            select_collection(key) {
                this.collection_key = key;
                this.get_atricle();
            },
            collection_option() {
                this.show_collection_option = !this.show_collection_option;
                this.show_article_menu = false;
            },
            article_menu() {
                this.show_article_menu = !this.show_article_menu;
                this.show_collection_option = false;
            },
            get_atricle() {
                this.$axios.get(`${this.$settings.Host}/article/article/`, {
                    params: {
                        'collection_id': this.collections[this.collection_key].id,
                    },
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    this.article_list = response.data;
                    this.article_key = 0;
                    this.show_editor = !!response.data.length;
                    this.get_article_content();
                }).catch(errors => {
                    this.$message.error('获取文章信息失败，请稍后再试！')
                })
            },
            create_article(position) {
                this.$axios.post(`${this.$settings.Host}/article/article/`, {
                    collection: this.collections[this.collection_key].id,
                    position: position,
                }, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response => {
                    if (position) {
                        this.article_list.push(response.data)
                    } else {
                        this.article_list.unshift(response.data)
                    }
                    this.show_editor = !!this.article_list.length;
                }).catch(errors => {
                    this.$message.error('添加文章失败，请稍后重试！')
                })
            },
            del_article() {
                let article_id = this.article_list[this.article_key].id;
                this.$confirm(`此操作将永久删除文章 ${this.article_list[this.article_key].name}，是否继续？`, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.$axios.delete(`${this.$settings.Host}/article/article/${article_id}/`, {
                        params: {
                            collection_id: this.collections[this.collection_key].id
                        },
                        headers: {
                            Authorization: `jwt ${this.token}`,
                        }
                    }).then(response => {
                        this.article_list.splice(this.article_key, 1);
                        this.show_editor = !!this.article_list.length;
                        this.$message.info('删除成功！');
                    }).catch(errors => {
                        this.$message.error('删除失败，请稍后重试！');
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            },
            publish_article(is_public) {
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
                        this.$message.success('发布成功！');
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
            shedule_publish_article() {
                this.schedule_datetime = new Date().toLocaleDateString();
                this.$axios.put(`${this.$settings.Host}/article/schedule/publish/`, {
                    pub_date: this.schedule_datetime,
                    article_id: this.article_list[this.article_key].id,
                }, {
                    headers: {
                        Authorization: `jwt ${this.token}`
                    }
                }).then(response=>{
                    this.$message.success('定时发布成功！');
                }).catch(errors=>{
                    this.$message.error('定时发送失败，请稍后重试！')
                });
                this.time_dialog_visible = false
            },
            time_format (time) {
                let t = new Date(time);
                return `${t.getFullYear()}-${t.getMonth()}-${t.getDate()} ${t.getHours()}:${t.getMinutes()}`;
            },
            select_article (key) {
                this.article_key = key;
                this.get_article_content();
            },
            get_article_content () {    // 填充当前编辑的文章标题和内容
                if (this.article_list.length && this.article_list[this.article_key].content) {
                    this.editor_content = this.article_list[this.article_key].content
                } else {
                    this.editor_content = ''
                }
                this.article_name = this.article_list.length?this.article_list[this.article_key].name:'';
            },
            change_article (value, render) {
                // 当文章内容发生改变时,自动执行的事件方法
                // console.log("原始内容：", value);
                // console.log("解析内容：", render);
                this.editor_content_render = render;
                clearTimeout(this.timer_id);
                this.timer_id = setTimeout(this.save_article, 1000);
            },
            save_article () {
                let pk = this.article_list[this.article_key].id;
                this.$axios.put(`${this.$settings.Host}/article/article/save/${pk}/`, {
                    content: this.editor_content,
                    render: this.editor_content_render,
                    name: this.article_name,
                }, {
                    headers: {
                        Authorization: `jwt ${this.token}`,
                    }
                }).then(response=>{
                    // this.$message.success('保存成功！')
                }).catch(errors=>{
                    this.$message.error('保存失败，请稍后重试！')
                })
            },
            // 绑定@imgAdd event
            img_add(file_name, $file_obj) {
                // 添加文件
                // 将文件上传到服务器
                let form_data = new FormData();
                form_data.append('image', $file_obj);
                this.$axios.post(`${this.$settings.Host}/article/image/`, form_data, {
                    headers: {
                        'Content-Type': 'multipart/form-data',
                        'Authorization': `jwt ${this.token}`,
                    }
                }).then(response=>{
                    // 将返回的url替换到文本原位置![...](0) -> ![...](url)
                    this.$refs.md.$img2Url(file_name, response.data.image);
                }).catch(errors=>{
                    this.img_del(file_name);
                    this.$message.error('添加图片失败，请稍后重试！')
                })
            },
            img_del(file_name) {
                // 删除文件
                // this.$refs.toolbar_left.$imgDelByFilename(file_name);
            }
        },
    }
</script>
```