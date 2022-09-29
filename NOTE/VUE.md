



# 二.模块化开发

常见的模块化规范：CommonJS、AMD、CMD、ES6的Modules

模块化的核心两个操作：导入+导出

CommonJS导出：

​		*module.export{*

​					*A:true,B:function(){...}, ...* 

​					*}*

CommonJS导入：

​		*let  { test }  = require('modulePath|modeleName')*



## 脚手架

环境：

​	node环境

​	webpack环境

​		comand:		npm install webpack@3.6.0 -g

​		comand:	    webpack --vbersion    //  3.6.0

安装vue-cli3.X:

​		comand: 		npm install -g @vue/cli@3.2.1

​		comand: 		vue --version

卸载vue-cli3.X:

​		comand:		npm uninstall -g @vue/cli

 桥接工具

​		comand: 		npm install -g @vue/cli-init

CLI2:init项目

​		comand: 		vue  init  webpack  project-name

CLI3:init项目

​		comand: 		vue  create  project-name



vue-cli2.x文件结构

​	build:webpack配置文件

​	config:webpack配置文件

​	src

​	static

​	.babelrc

​	.editorconfig

​	.gitignore

​	.postcssrc.js

​	.index.html

​	.package.json

​	

webpack-merge的merge函数：

```
const merge = require('webpack-merge')

var A = {flag:true,age:34}
var B = {flag:false,name:"wangzhe"}

var c = merge(B,A)

console.log(c)
```

结果： { flag: true, name: 'wangzhe', age: 34 }

## vue-cli3.x配置

1.  启动配置服务器

   ​	vue  ui

2. 文件夹：node-modules-->@vue-->cli-server-->webpack.config.js

3. 创建文件 vue.config.js

   ```
   module.exports = {
       
   }
   ```

   



# 四.VueX

集中式组件状态管理

 使用情况：

​		多个界面共享

​		用户的登陆状态，头像，用户名称，地理位置

​		商品的收藏，购物车的物品

##  安装及使用

​		npm install vuex -S

​		创建文件夹store

​	    创建文件index.js

```js
 import Vuex from 'vuex'
import Vue from 'vue'
//安装插件
Vue.use(Vuex)
const store = new Vuex.Store({
 	state:{
	//保存共享数据的区域
    },
    mutations:{
	//
    },
    actions:{
	// 异步操作区
    },
    getters:{
	// 
    },
    modules:{
        
    }
})
//导出
export default store
```

```js
mian中导入
import Vue from 'vue'

import App from './App'
import router from './router'
import store from  './store'
Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  store,  // Vue.prototype.$store = store
  render: h => h(App)
})

```

## 状态图

![vuex](..\img\vuex.png)



## state单一状态树

​	状态区

## getters计算属性

​	像计算属性

```js
getters:{
        nameAge(state){
            //state 默认参数
            return state.name + state.age
        },
        filter(state){
            return state.info.filter(item=>item>14)
        },
        fliterCount(state,getters){
            //state 即 state
            //getters 即 getters
            return  getters.filter.length
        },
        //可传参的属性
        fliterNum(state){
            //state 即 state
            //getters 即 给getters
            return  function(age){
                return state.info.filter(item=>item>age)
            }
        },
        fliterNumCount(state,getters){
            //state 即 state
            //getters 即 getters
            return  function(age,getter=getters){
                return getter.fliterNum(age).length
            }
        },
},
```

```js
$store.getters.nameAge    
```



## mutations同步函数

 ==vuex中state状态更新的唯一方式：提交Mutation==	 

mutation的方法应该被看为两部分：

​		事件类型 type

​		回调函数 handler

```js
 mutations:{
        //方法区
         changeAge(state,age){
            //state 是默认参数 对应上面的state
            console.log(age)
            state.age  = age
         } ,
         changeName(state,payload){
             console.log(payload)
             state.name = payload.name 
         }
    },
```

```js
 this.$store.commit("changeAge",35)    //commit 提交 的是  mutations 
//or
 this.$store.commit({
     type:"changeName",
     name:"qiansui"
 }) //后台收到是是一个对象啦
```

对于在store预先定义的数据等，在方法中进行临时添加的数据不会进行响应式的绑定

```js
changeInfo(state,payload){
    //临时添加
    state['address']="长安"
    state.parter["second"]="BP"
	//该方式做不到响应式
    delete state.age
}
```

 

```js
数组的响应式方法push|pop|shift|unshift|reverse|sort
数组中不是响应式的方式arr[index]=value
解决办法：
	Vue.set(this.arr,index,value)
	使用响应式的方法进行替换
    Vue.delete(state,"age");
```

```js
 changeInfo(state,payload){
     Vue.set(state.parter,'second',"GBP")
     Vue.set(state,'address',"长安")
     Vue.delete(state,"age");
 }
```

## mutations类型常量

```js
@/store/mutations-types.js
export const  CNAHGEAGE= "changeAge"
```

```js
import {CHANGEAGE} from "@/store/mutations-types"
this.$store.commit(CHANGEAGE,35)
```

```js
@/store/index.js 文件
import {CHANGEAGE} from './mutations-types'
 mutations:{
     //方法区
     [CHANGEAGE](state,age){
         console.log(age)
         state.age  = age
     } ,
 }
```

## Actions

如果在mutations中的方法中进行异步操作，会使得devtools 无法正确的跟踪数据变化，不利于调试

如下错误演示代码：

```js
mutations:{
	 //方法区
     [CHANGEAGE](state,age){
         //state 是默认参数 对应上面的state
         setTimeout(() => {
             console.log(age)
             state.age  = age    
         }, 1000);
     } ,
}
```

**规矩：**

​		==<u>mutations中所有的代码不能是 异步的</u>，所有的异步均在actions中书写==

```js
actions: {
    AupdataInfo(context,para) {
    //context 默认参数，暂时理解为store对象即可
        setTimeout(() => {
        	context.commit(CHANGEAGE, para)
        }, 1000);
    }
},
```

```js
this.$store.dispatch('AupdataInfo', 45);
```

**数据修改成功后，有必要通知外部信息修改成功**

方式一：

```js
 actions: {
        AupdataInfo(context,arg) {
            //context 默认参数，暂时理解为store对象即可 ，至多只接受一个arg参数
            setTimeout(() => {
                context.commit(CHANGEAGE, arg[0])
                arg[1]()
            }, 1000);
        }
    },
```

```js
this.$store.dispatch('AupdataInfo',[35,()=>{alert("年龄修改完毕")}])
//利用数组或者对象{}进行多值传递
```



方式三：

```js
this.$store.dispatch('AupdataInfo', 35).then(()=>{alert("年龄修改成功")});
```

```js
 AupdataInfo(context, payload) {
     //context 默认参数，暂时理解为store对象即可
     return new Promise((reslove, reject) => {
         setTimeout(() => {
             context.commit(CHANGEAGE, payload)
             reslove()
         }, 1000);
     });
 }
```





## modules

为了避免state数据臃肿，于是使用modules进行数据的分割

在modules可以定义模块，各个模块可以有自己的state\getters\mutations\actions\modules

```js
var ModuleA = {
    state:{name:"zhangsan"},
    mutations:{
        update(state){
            //即本身state
            state.name ="lisi"
        }
    },
    actions:{
        Aupdate(context){
            // context 中含有一些根的rootState rootGetters
            setTimeout(() => {
                context.commit("update")      // 只commit ModuleA中的 mutations 
            }, 1000);  
        }	
    },
    getters:{
        get(state){
            return state.name+"1122"
        },
		getA(state,getters){
			//state  即 ModuleA state
            //getter 即 ModuleA getters
            return getters.get+"1234"
        },
        getRootAge(state,getters,rootState){
            //rootState 即 root state
            return rootState.age
        } 
    },
    modules:{}
}
var ModuleB = {
    state:{},
    mutations:{},
    actions:{},
    getters:{},
    modules:{}
}
index.js
modules: {
        A:ModuleA,
        B:ModuleB,
    }
```

```js
//数据使用
$store.state.A
$store.state.B
```

```js
//使用mutations的方法
this.$store.commit("update")   //与之前没有任何变化  建议命名一定要不同
```

```js
//使用getters计算属性
$store.getters.get
$store.getters.getA
```

```js
//使用actions
this.$store.dispatch("Aupdate")
```



## 对象的解构

```js
var obj = { name:"wangzhe",age:"34",sex:"female"}
const {name,age,sex} = obj
之后的name,age,sex 就是obj中对应的name,age,sex	
```

在actions中，方法的参数本应该是context

​	funName(context){ context.commit(方法名) }

现在:

```js
funName({state,commit,getters}){ commit(方法名) }
```

##  项目store的结构

store

​	index.js

​	actions.js

​	mutations.js

​	modules

​		car.js

​		products.js





# 六.Scroll安装

https://better-scroll.github.io/docs/en-US/guide/how-to-install.html#npm

```bash
npm install better-scroll --save
npm install @better-scroll/core --save
npm install @better-scroll/pull-up --save

import BetterScroll from 'better-scroll'
let bs = new BetterScroll('.wrapper', {})

import BScroll from '@better-scroll/core'
import PullUp from '@better-scroll/pull-up'
BScroll.use(PullUp)
```

## 基本滑动

要求wrapper中只包含一个标签

```js
import BScorll from "@better-scroll/core";
```

```js
mounted() {
    this.bs = new BScorll(".el", {});
},
```

## 滚动监听

```js
import BScorll from "@better-scroll/core";
```

```js
mounted() {
    this.bs = new BScorll(".el", {
      probeType: 3,
      // 0 表示不侦测
      // 1 表示不侦测
      // 2 手指滚动时实时侦测,手指离开后的惯性不进行侦测
      // 3 侦测全部滚动
    });

    // 监听滚动
    this.bs.on("scroll", (position) => {
    //   console.log(position.x, position.y);
    });
},
```

## 上拉加载

```js
import BScorll from "@better-scroll/core";
import PullUp from "@better-scroll/pull-up";
//插件导入和安装
BScorll.use(PullUp);
```

```js
mounted() {
    this.bs = new BScorll(".el", {
      probeType: 3,
      // 0 表示不侦测
      // 1 表示不侦测
      // 2 手指滚动时实时侦测,手指离开后的惯性不进行侦测
      // 3 侦测全部滚动
      click: true,
      // 监听内部点击
      pullUpLoad: true,
      //
    });

        // 监控是否到达底部，到达底部则进行触发
        this.bs.on("pullingUp", () => {
            console.log('to bottom')
            //等数据请求完成，并且将新的数据展示出来后，进行调用
            this.bs.finishPullUp();
            // 保证更多次的加载
        });
  },
```

## 跳转到顶部

scrollTO(xposition,yposition,timeout)

```js
    scrollTo(x, y, timeout = 500) {
       // 先要有对象 再有方法 再调用方法
       this.bs && this.bs.scrollTo && this.bs.scrollTo(x, y, timeout);
    },
```

## 重新计算高度

refresh()

```js
    refresh() {
       // 先要有对象 再有方法 再调用方法
      this.bs && this.bs.refresh && this.bs.refresh();
    },
```





