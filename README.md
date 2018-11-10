## 小米商城 移动端 仿版
## 声明
本项目纯属本人闲时开发，页面参考小米商场官网页面，项目中所涉及到的接口均为假数据，为实现页面数据交互效果（假数据小米商城官网）；<br>
其中开发均为本人一人参与，如有侵权，联系删除（@小米）；<br>
发现问题可Issess本人，会做相应修改，但具体功能截至，不做后续迭代；<br>
如有借鉴或转发，否，必追究；<br>
## 项目简介
#### 路由
- 首页
  - 商品详情
    - 规格选择
    - 评论列表
    - 评论详情
    - 参数详情
    - 猜你喜欢
- 分类
- 购物车
- 我的

## 开发过程
#### 1、基于vue-cli@3，搭建项目骨架
（1）搭建骨架，可参考[官网](https://cli.vuejs.org/zh/)
```javascript
npm install -g @vue/cli
vue create xiaomi_market //可自定义配置自己的框架需求 xiaomi_market为本项目的项目名
```
（2）开启项目
```javascript
cd xiaomi_market
npm i
npm run serve //开启本地项目
```
#### 2、引入rem布局的必要文件
（1）增加flexible.js于/public/js/位置下，/public/index.html中引入该文件；<br>
（2）配置编译器的自动转换功能，（VScode可使用cssrem插件）;<br>
（3）添加代码，控制自适应的返回在320px--540px之间。
```javascript
if (width / dpr > 540) {
    width = 540 * dpr;
}
if (width / dpr < 320) {
    width = 320 * dpr;
}
```
#### 3、引入reset.css、字体图标库
（1）在App.vue 加入reset.css文件内容；<br>
（2）/public/index.html中引入字体图标库，可使用[iconfont阿里巴巴](http://www.iconfont.cn/)。
#### 4、配置代理处理跨域
（1）在根目录增加文件：vue.config.js<br>
（2）添加相应代码，可参考[官方文档](https://cli.vuejs.org/zh/config/#devserver)
#### 5、封装接口请求函数
（1）添加axios依赖；<br>
（2）在根目录添加文件夹server/；<br>
（3）在server/文件内添加api.js、index.js；<br>
（4）api.js依赖axios封装请求和相应的拦截器，并对GET、POST请求进行进一步封装返回promise；<br>
（5）index.js就对应的接口进行统一管理，主要引入api.js的方法，返回结果，需要引入的接口做相应的引入，即可。
#### 6、配置环境变量
（1）根目录新增文件 .env.test、.env.production，为打包目标做服务；<br>
（2）修改package.json中scripts如下（可自行定义）：
```javascript
{
    "serve": "vue-cli-service serve",//运行本地环境，无需打包
    "test": "vue-cli-service build --mode test",//打包测试环境代码
    "production": "vue-cli-service build --mode production"//打包生产环境代码
}
```
（3）根目录新增文件夹config，并在config/新增文件：dev.js、test.js、production.js，在其中配置相应的环境变量和值；<br>
（4）增加vue.config.js文件的内容如下：
```javascript
const dev_config = require('./config/dev.js');
const test_config = require('./config/test.js');
const prodctioin_config = require('./config/production.js');

const env = process.env.NODE_ENV;//当前的环境
let config = null;
if(env=='production'){
    config = prodctioin_config;
}else if(env=='test'){
    config = test_config;
}else{
    config = dev_config;
};
```
根据不同的环境，引入对应的config配置值。
#### 7、添加插件miut-ui(UI框架)
安装和调用 Vue CLI 插件,可使用vue add xxxx;<br>
其他框架依旧使用npm install xxx;<br>
这里可以参考[mint-ui官网](https://mint-ui.github.io/#!/zh-cn)
在main.js文件添加如下代码：
```javascript
import Mint from 'mint-ui';
import 'mint-ui/lib/style.css';
Vue.use(Mint);
```
#### 8、使用状态管理模式，vuex
（1）根目录新增文件夹stote/（本次将vuex模块化，实际不需如此复杂，这里为了应用一下）；<br>
（2）store/中新增index.js（store的中心文件，暴露store给mian.js引用）、新增modules/（存放各个模块）；<br>
（3）modules/中新增indexItem.js(indexItem这个模块的中心文件)、新增indexItem/（存放该模块的actions、getters、mutations内容）；<br>
（4）indexItem.js添加如下代码：
```javascript
// indexItem
import actions from './indexItem/actions';
import getters from './indexItem/getters';
import mutations from './indexItem/mutations';

const state = {
    activeIndex:1
};


export default {
    namespaced:true,
    state,
    actions,
    getters,
    mutations
}
```
（5）store/index.js添加如下代码：
```javascript
import Vue from 'vue'
import Vuex from 'vuex'
import indexItem from './modules/indexItem'

Vue.use(Vuex)


export default new Vuex.Store({
  modules: {
    indexItem
  }
})
```
（6）根目录main.js做vuex全局挂载（修改文件引用即可），至此vuex即可全局使用，可参考[vuex官方实例](https://github.com/vuejs/vuex/tree/dev/examples/shopping-cart)；<br>
（7）使用vuex,使用方法有很多，可参考[官方文档](https://vuex.vuejs.org/zh/guide/state.html)，我们这里使用对象展开运算符的方法:
```javascript
import { mapState } from 'vuex';


...
  computed: {
    ...mapState('indexItem',[
      'activeIndex'
    ])
  },
...
...
    console.log(this.activeIndex); //即可使用
...
...
  methods:{
    ...mapActions('indexItem',[
      'changeIndexItem',
      'setSwipeName'
    ]),
    changeItem(index){ 
      this.changeIndexItem(index);//即可使用
    }
  },
...
```
#### 9、开始项目代码
不过多介绍项目的代码进行的细节,会抽取部分简介：
- 引入‘lodash.debounce’进行防抖操作；
