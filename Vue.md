# Vue

### 基础

> Object.definepropety

作用：直接在一个对象上定义一个新属性，或者修改一个已经存在的属性

> vue2数据代理

```js
const data = { name: "张三" } //用户配置的data数据，会经过数据劫持和响应式处理后放入Vue实例对象的_data属性中
const vm = { _data: data } //Vue实例对象
// 将对name属性的操作，代理到vm身上
Object.defineProperty(vm, "name", {
       enumerable: true,//可枚举
       set: function (newValue ) {
            this._data.name = newValue
       },
       get: function () {
            return this._data.name
        }
})
```

> 事件修饰符(可连写多个)

```js
@xxx或者v-on:xxx绑定事件
v-model.lazy:失去焦点再收集数据
v-model.number:输入字符串转为数字
v-model.trim:过滤首尾空格
v-model收集表单数据：
	1.type="text" 收集的是value值，用户输入的是value值
    2.type="radio" 收集的是value,且要给标签配置value值
	3.type="checkBox" 如果没有配置value ，收集checked的值, 配置了value且v-model初始为数组，则收集勾选value组成的数组,配置了value且v-model初始为非数组,收集的是checked的值(true/false)
@click.prevent：阻止默认事件
@click.stop：阻止事件冒泡
@click.once：事件只触发一次
@click.capture：使用事件的捕获模式
@click.self：只有event.target是当前操作的元素时才触发事件
@click.passive：事件默认行为立即执行，无需等待回调执行完毕
@click.native :原生点击事件

```

> 键盘事件

```js
@keyup.enter="xxx" //抬起
@keydown.enter="xxx" //按下(tab,ctrl,alt,shift一般配合keydown使用)
@keydown.ctrl.y="xxx" //按下ctrl+y 触发
Vue.config.keyCodes.自定义键名=键码  //字字能够以按键别名
```

> 计算属性

```js
//底层借助了Object.defineproperty
//相对于methods,计算属性具有缓存机制，效率更好
//computed中的属性最终会出现在vm/vc身上
computed:{
    test:{
        //get调用时机：1.初次读取test时，2.所依赖的数据改变时(a)
        //依赖数据没有改变，多次访问的是缓存,不会多次调用get
        get(){
            return this.a
        },
        //当修改test时调用
        set(v){
            this.a=v
        }      
    }
}
//当只考虑数据读取时的简写方式
computed:{
    //等于getter
    test(){
        return this.a
    }
}
```

> 监听（必须存在才能监视）

```js
//方法一：配置项
watch:{
    //监视的属性名
    test(newValue,oldValue):{
        immediate:true,//初始化时handler立即执行
        deep:true,//深度监视(多级结构属性变化watch默认不监测)
        //test属性改变时调用
        handler(newValue,oldValue){}
    }
}
//方法二：
vm.$watch{xxx}
```

> 绑定样式

```js
//字符串写法，适用于类名不确定，需要动态指定
<div :class="mood"></div>

//数组写法,适用于样式个数不确定，名字也不确定
arr=['classa','classb','classc']
<div :class="arr"></div>

//对象写法,适用于个数确定，名字确定，需要动态指定
obj={classa:false,classb:false}
<div :class="{obj}"></div>

//使用style绑定样式
fsize=18
bgColor='red'
<div :style="{fontSize:fsize+'px';backgroundColor:bgcolor}"></div>
<div :style="[{fontSize:fsize+'px'},{backgroundColor:bgcolor}]">

```

> vue监测数据改变

```js
let data={
    a:1,
    b:2
}
//创建一个监视的实例对象，用于监视data中属性的变化
const obs=new Observer(data)
//创建vue实例对象
let vm={}
vm._data=data=obs
function Observer(obj){
    //获取对象中所有的key(a,b)
    const keys=Object.keys(obj)
    keys.forEach(k=>{
        Object.defineProperty(this,k,{//此处的this为obs
            get(){
                return obj[k]
            },
            set(val){
                obj[k]=val
            }
        })
    })
}
```

```
1.vue会监测data中所有层次的数据
2.监测对象中的数据：
	通过setter实现监视数据，在new Vue时就要传入要监测的数据
	(1)对象中后追加的属性，Vue默认不做响应式处理
	(2)Vue.set(target,propertyName/index,value)
		vm.$set(target,propertyName/index,value)
        可以给后追加的属性做响应式
3.如何监测数组中的数据：
	通过包裹数组更新元素的方法实现,本质就是做了两件事：
		(1)调用原生的数组方法更新数组信息
		(2)重新解析模板，更新页面
4.在Vue中修改数组中的某个元素：
	(1)使用push,pop,shift,unshift,splice,sort,reverse
	(2)使用Vue.set()或者vm.$set() 这两个方法不能给vm或vm根数据对象(data本身) 添加属性
```

> 数据过滤器(插值语法和v-bind)

```js
//局部
<h2>{{Date.now() | timeFormater}}</h2>
new Vue({
    el:"#root",
    methods:{}
    filters:{
    	timeFormater(value){
    		//传进来的value值为管道符左边的值
    		return dayjs(value).format('YYYY年MM月DD日')//return返回的将替换整个模板
		}
	}
})
//全局
Vue.filter('name',function(value){
    return xxx
})
```

> 内置指令附加

```
1.v-text 向所在标签渲染文本内容(替换整个节点中的内容)
	<div v-text="name"></div>   name为变量
2.v-html：向指定节点渲染包含html结构的内容(替换整个节点中的内容，有安全性问题)
	<div v-html="<h3>你哈哈<h3>"></div> 
3.v-clock Vue实例创建完成之后删除此属性,可配合css解决网速慢导致页面展示的相关问题
	<style>
    	[v-clock]{
       	 display:none
    	}
    </style>
	<h2 v-clock>{{name}}</h2>
4.v-once 以后数据的改变不会导致v-once所在结构的更新
	<h2 v-once>{{a}}</h2>  a的内容初次渲染后就变为静态内容
5.v-pre 跳过所在节点的编译解析过程，使用在没有用到插值语法的节点 可以加速编译
	<h2 v-pre>你你你</h2>
```

> 自定义指令(函数式)

```js
//v-xxx-xxx
//指令相关的回调中的this指向window
//局部
new Vue({
    data(){
        return {
            n:1
        } 
    },
	directives:{
        //big为自定义指令的名字,使用: v-big="xxx"
        //big函数调用时机：指令与元素成功绑定时，指令所在模板被重新解析时
        //函数式(bind+update)：
		big(element,binding){
            console.log(this)//window
			element.innerText=binding.value*10
		},
        //对象式
        fbind:{
            //元素与元素成功绑定时调用
            bind(element,binging){
                element.value=binding.value
            },
            //指令所在元素被插入页面是调用
            inserted(element){
                element.focus()//获取焦点
            },
            //指令所在模板被重新解析时调用
            update(){
                element.value=binding.value
            }
        }
	}
})
//全局
Vue.directive('fbind',{//配置项})
Vue.directive('big',function(xxx){xx})
```

### Vue生命周期

```
==>>初始化：生命周期，事件，数据代理未开始
==>>beforeCreate(){}//无法通过vm访问data中的数据和methods中的方法，无_data,this为vue实例
==>>初始化：数据监测，数据代理
==>>created(){}//可以通过vm访问data中的数据和methods中的方法
==>>开始解析模板，生成虚拟DOM(内存中),页面不能显示解析好的内容({{}})
==>>beforeMount(){}//显示未经编译的DOM，对DOM操作最终都不生效
==>>将虚拟DOM转换为真实DOM，且页面插入真实DOM
==>>mounted(){}//Vue完成模板解析并把初始化的真实DOM放入页面,对DOM操作有效,一般用于开启定时器，网络请求，订阅消息，绑定自定义事件等初始化操作
==>数据改变？
	==>>beforeUpdate(){}//数据是最新，页面未更新
	==>>根据数据生成新的虚拟DOM，新旧DOM对比，页面更新
	==>>updated(){}//数据时最新，页面时最新
==>vm.$destroy()调用？
	==>beforeDestory(){}//vm所有data，methods，指令等都可用,不会触发更新,马上执行销毁
	==>关闭定时器，取消订阅消息，取消watch，销毁所有子组件，解绑自定义事件等
	==>destroyed(){}//销毁
```

### Vue组件

> 完成特定功能的代码和资源的集合,也是一个vue文件，作用：方便维护，简化编码



#### 非单文件组件（一个文件中包含n个组件）

```js
//创建组件
const school=Vue.extend({
    template:`<div>
    	<h2>{{schoolName}}</h2>
    	<h2>{{address}}</h2>
    </div>`
    data(){
        return{
            schoolName:'尚硅谷',
            address:"北京昌平"
        }
    }
})
const student=Vue.extend({
    template:`<div>
    	<h2>{{studentName}}</h2>
    	<h2>{{age}}</h2>
    </div>`,
    data(){
        return{
            studentName:'张三',
            age:18
        }
    }
})
//注册组件(局部)
new Vue({
    el:'#root',
    compoents:{school,student}
})
//注册组件(全局)
Vue.compoent('school',school)


/*
	注意点：
		1.组件名：
			多个单词组成时：'my-school'，MySchool(脚手架环境)
			一个单词组成时：school,School
		2.组件标签：
			<school></school>
			<school/>(脚手架)
		3.创建组件简写：
			cosnt school={
				template:'xxxx',
				data(){
					reutrn {
						xxx
					}
				}
			}
```

#### props通信



```
传递数据(也可以是方法)：
<School address="北京" name="尚硅谷" :age="18"/>//当使用:age时，里面的内容会被当做表达式解析
接收数据：
1）props:["address","name","age"]
2）props:{address:String,name:String,age:Number}
3）props:{
	address:{
		type:String,//类型
		required:true,//必须传
	},
	age:{
		type:Number,
		default:99,//默认值
	}
}


props是只读的属性，如果需要修改数据，需要复制props的内容到data中，去修改data中的数据
切记：不要修改props传递的数据
props只适用于父子组件间的通信
```

`如何实现props数据双向绑定？`

```vue
父组件
<div>
	<!-- <Child :money.sync="total"/> -->
    父组件则通过$event 来接收经过子组件修改后的值。
    <Child :money="total" v-on:update:money="total = $event"/>
</div>



子组件通过props接收数据
当子组件想要修改父组件中的total值时，利用$emit,携带值通知父组件修改
<button @click="$emit('update:money', money-100)">
    
    
    
.sync与v-model的区别：
v-model倾向于结果(value)，是一种改变操作，  
.sync倾向于互相状态的传递，是一种更新状态的操作
```











#### mixin混入

```js
//混入可以吧多个组件共用的配置提取成一个混入对象
//1.定义
export const mixin={
    data(){
        return {
            a:100
        }
    }
    methods:{
        showName(){
            
        }
    },
    mounted(){
        console.log(111)
    }
}
//2.使用
//全局:
Vue.mixin(mixin)//全局都可以使用，且不需要import导入

//组件中：(局部)
import {mixin} from 'xxx'
export default={
    name:'School',
    mixins:[mixin]
}
```

#### 插件

```js


//plugin.js
export default {
    install(Vue,args){
        //全局过滤器
        Vue.filter('muSlice',function(value){
            return value.slice(0,4)
        })
        //自定义全局指令
        Vue.directive('fbind',{
            bind(element,binding){
                element.value=binding.value
            },
            inserted(element,binding){
                element.focus()
            },
            update(element,binding){
                element.value=binding.value
            }
        })
        //定义混入
        Vue.mixin({data(){return{a:1}}})
        Vue.prototype.hello=()=>{console.log('111')}
    }
}
//main.js引入
import plugins from 'xxx'
//应用插件
Vue.use(plugins,test)//test为传递的参数
```

#### 组件的自定义事件通信 

```
适用于子组件传递数据给父组件
定义事件:
当test事件被触发时，getStudentName方法被调用
事件添加在Student组件实例对象上
1.<School @test="getStudentName"/>
2.<School v-on:test="getStudentName"/>
3.<Student ref="student">
  methods:{
  	getStudentName(name,...params){
  		console.log(name)
  	}
  }	
  mounted(){
  	this.$refs.student.$on("test",this.getStudentName)	可触发多次
  	this.$refs.student.$once("test",this.getStudentName) 只触发一次
  	回调如果不写成箭头函数，则this指向为触发事件的组件实例对象
  	this.$refs.student.$on("test",(name)=>{
  		this.getStudent(name)
  	})
  }	
  
触发事件：
methods:{
	xxx(){
		this.$emit("test",name,11,22,33)//触发事件
	}
}

解绑事件：
methods:{
	unBind(){
		this.$off("test") 解绑一个事件
		this.$off(["test","test1"]) 解绑多个事件
		this.$off() 解绑全部自定义事件
	}
}
```

#### 作用域插槽通信

```vue
在父组件中，定义一个data对象，并使用作用域插槽将其传递给子组件
<template>
  <div>
    <MyComponent>
      <template #default="{ data }">  # 等同于 v-slot:
        <button @click="handleButtonClick(data)">点击我</button>
      </template>
    </MyComponent>
    <p>父组件接收到的数据: {{ receivedData }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      receivedData: ''
    };
  },
  methods: {
    handleButtonClick(data) {
      // 在这里处理子组件按钮的点击事件
      console.log('点击了按钮');
      this.receivedData = data;
    }
  }
}
</script>

在子组件中，使用v-slot指令来声明作用域插槽，并在插槽中将数据传递给父组件：
<template>
  <div>
    <slot :data="customData"></slot>
  </div>
</template>
<script>
export default {
  data() {
    return {
      customData: '作用域插槽中的数据'
    };
  }
}
</script>

父组件通过使用v-slot指令来声明一个作用域插槽，并通过插槽的参数data接收子组件传递的数据。然后，父组件在按钮的点击事件处理函数中将接收到的数据保存到receivedData属性中，并在模板中将其显示出来。

子组件定义了一个customData属性来存储要传递给父组件的数据。在子组件的插槽中，使用:data的方式将customData传递给父组件。
```







#### 全局事件总线

```js
//适用于：任意组件间通信
//main.js：
new Vue({
	el:'#root',
    render:h=>h(App),
    beforeCreate(){
    	Vue.prototype.$bus=this//安装全局事件总线
    }
})

//组件中定义事件(在beforeDestroy要解绑自定义事件)：
this.$bus.$on('test',(number)=>{
    console.log(number)//123
})
//组件中触发事件
this.$bus.$emit('test',123)
```

#### 消息订阅与发布

```js
//1.npm install pubsub-js 
	
///2.订阅（接收数据的地方）：
  import pubsub from 'pubsub-js'
  mounted(){
    this.pubId=pubsub.subscribe('test',function(messageName,data){
  	console.log(messageName)//test(消息名字)
  	console.log(data)//123
    console.log(this)//undefined(当回调为箭头函数时,this为当前组件实例对象)    
  })
  },
  beforeDestroy(){
      pubsub.unsubscribe(this.pubId)//销毁前取消订阅	
  }    
  

//3.发布（发送数据的地方）：
 import pubsub from 'pubsub-js'
 pubsub.publish('test',123)
```

#### nextTick

```
1.语法：this.$nextTick(回调)
2.作用：在下一次Dom更新结束后执行回调
3.什么时候用：数据改变，要基于更新后的dom进行操作时
```

#### 动画效果

```
<transition name="xxx">
	<h1 v-show="isShow">
</transition>
xxx-enter-active{
	animation:test 0.5s linear
}
xxx-leave-active{
	animateion:test 0.5s linear reverse
}
@keyframes test{...}
```

#### 过渡效果

```
需要过渡效果的元素
h1{
transition :0.5s linear
}
进入的起点
.xxx-enter{
 transoform:translateX(-100%)
}
进入的终点
.xxx-enter-to{
transoform:translateX(0)
}
离开的起点
.xxx-leave{
 transoform:translateX(0)
}
离开的终点
.xxx-leave-to{
transoform:translateX(-100%)
}


总结:
1.准备好样式
	进入样式：
	 v-enter:进入的起点
	 v-enter-active:进入过程中
	 v-enter-to:进入的终点
	离开样式：
     v-leave:离开的起点
     v-leave-active:离开过程中
     v-leave-to:离开的终点
2.使用transition标签包裹要做动画的元素，添加name属性，如果有多个需要使用transition-group,并设置key
```

#### 配置代理

```js
//Vue.config.js
//方式一：
devServer:{
	proxy:'http://localhost:5000',//目标服务器
}
//方式二：
  devServer: {
    proxy: {
    	//请求前缀
      '/api': {
        target: 'http://localhost:5000',
        pathRewrite：{
            '^/test':''
        }
        ws: true,//用于支持websocket
        changeOrigin: true
      },
      '/foo': {
        target: '<other_url>'
      }
    }
  }
```

#### 插槽

```
1.作用：让父组件可以向子组件指定位置插入结构，适用于父=》子
2.默认，具名，作用域
3.
	(1)默认插槽：
		父：
		<Test>
			<div>123</div>
		</Test>
		子：
		<template>
			<div>
				<slot>默认内容</slot>
			</div>
		</template>
	(2)具名插槽：
		父：
		<Test>
			<div slot="center">123</div>
			<div slot="footer">456</div>
		</Test>
		子：
		<template>
			<div>
				<slot name="center">默认内容</slot>
				<slot name="footer">默认内容</slot>
			</div>
		</template>
	(3)作用域插槽(数据在自身，根据数据生成的结构需要组件使用者来决定)
		父：
		<Test>
			<template scope="data">  也可以使用slot-scope接收数据
				<div>{{data.name}}<div>
			</template>
		</Test>
		子：
		<template>
			<div>
				<slot :name="name">默认内容</slot>
			</div>
		</template>
	
```

#### Vuex

> 在Vue中实现集中式数据（状态管理）的Vue插件，对Vue应用中多个组建的共享状态进行集中式管理，适用于任意组件之间的通信

> 使用时机：1.多个组件依赖同一个状态
>
> ​					2.来自不同组件的行为需要变更同一状态

```js
1.安装vuex npm i vuex
2.main.js中
	import store from 'xxx'
	new Vue({
        xxx,
        store,
    })
3.src目录创建stroe目录，在stroe目录创建index.js
	import Vuex from 'vuex'
	import  Vue from 'vue'
	Vue.use(Vuex)
	//用于响应组件中的动作
	const actions={
        jia:function(context,value){
            context.commit('JIA',value)//调用mutations中的方法
        }
    }
    //操作数据
    const mutations={
        JIA:function(state,value){
           state.a++ 
        }
        
    }
    //保存数据
    const state={
        a:0
    }
    //加工state中的数据
    const getters={
        bigA(state){
            return state.a*10
        }
    }
   export default new Vuex.Store({
        actions,
        mutations,
        state,
        getters
    })
4.组件中使用：
 this.$store.dispatch('jia',1)//调用方法，传递数据
 this.$store.commit('JIA',1)//直接操作数据
 <div>{{$tore.state.a}}</div> 读取vuex中的数据
```

```
前提：import {mapState,mapActions,mapMutations,mapGetters} from 'vuex'
1.借助mapState或mapGetters生成计算属性,从state或getters中读取数据
computed:{
	对象写法，访问时用计算属性名访问
	...mapState({计算属性名:'state中的数据名',计算属性名:'state中的数据名'}),
	数组写法，访问时用state中的数据名访问
	...mapState(['state中的数据名','state中的数据名']), 
	...mapGetters({bigA:'bigA'})
	...mapGetters(['bigA'])
}
2.借助mapActions或mapMutations生成methods方法
methods:{
	对象写法
	...mapActions({jia:"jia"}) 前者为方法名，后者为vuex中actions配置的方法
	...mapActions(['jia','jian']) 方法名与vuex中actions配置的方法名相同
    ...mapMutations({methodsName:'JIA'})
    ...mapMutations(['JIA','JIAN']) 
}
```

vuex模块化+命名空间

```js
//vuex中
const personModule={
    namespaced:true,//配置后才能通过模块名访问
	actions:{},
	mutations:{ADD(state,value){}},
	state:{personList:{}},
    getters:{
        getOne(){
            return 1
        }
    }
}
const countModule={
    namespaced:true,
	actions:{},
	mutations:{},
	state:{sum:0}
}
export default new Vuex.Store({
	modules:{
		personModule,
		countModule
	}
})
//此时，使用映射关系时
...mapState('personModule',['personList'])
...mapState('countModule',['sum'])
//其他问题
this.$store.commit('personModule/ADD',personObj)
this.$store.getters['personModule/getOne']
```



`模板化访问getters`

```js
 computed: {
                welcome() {
                    return store.getters.welcome
                },
                username() {
                    return store.getters['user/username']
                },
                token() {
                    console.log(store);
                    return store.getters['user/token']
                }
            },
```

`模块化访问方法`

```js
store.commit('user/login', { username, token: 'sxgWKnLADfS8hUxbiMWyb' })
```





#### 路由

```js
1.npm i vue-router
2.main.js中引入
import router from './router/index.js'
new Vue({
	router
})
3.新建router目录，新建index.js
import VueRouter from 'vue-router'
import About from 'xxx'
import Home from 'xx'
Vue.use(VueRouter)
export default new VueRouter({
    routes:[
        {
            path:'/about',
            compoent:About
        },
        {
            path:'/home',
            compoent:Home
        }
    ]
})
4.组件中借助router-link标签实现路由跳转
<router-link to="/home" active-class="active">xxx<router-link>
<router-link to="/about">xxx<router-link>
5.指定展示位置
<router-view><router-view>
```

```
注意点：

1.pages文件夹一半存放路由组件，Components文件夹一半存放普通组件

2.通过切换后的路由组建默认是被销毁的，需要的时候再次挂载

3.每个组件都有自己的$route,里面存储自己的路由信息

4.整个应用只有一个$router
```

> 多级路由：

```js
export default new VueRouter({
	routes:[
		path:'/home',
		compoent:About,
		children:[
			{
			 path:'news',//子路由不加斜杠
        	 name:'news',//命名，此时用path可以name而不用path :to="{name:news}"
			 compoent:News
			}
		]
	]
})
<router-link to='/home/news'>
```

> 路由query传参：

```
1.传递 
方式一：<router-link :to="`xxx?id=${m.id}`">
方式二：<router-link :to="{path:'/home',
				  query:{id:m.id,title:m.title}}">
<router-link>
2.接收 
this.$route.query.id 
```

> 路由params参数

```
方式一：<router-link :to="`/home/${m.id}`"></router-link>
路由配置中{
	name:'home',
	path:'home/:id'//占位
}
方式二：<router-link :to="{
 name:'home',  当使用params传递参数时，必须使用name而不能使用path
 params:{
 	id:m.id
 }
}"></router-link>
接收：this.$route.params.id //666
```

> 路由props

```js
//路由配置中
{
 name:'xxx',
 path:'/detail',
 compoent:Detail,
 //方式一：props对象中的key-value会以props的形式传递给Detail组件
 props:{a:1,b:'hello'},
 //方式二：值为true时，会把该路由收到的所有params参数通过props传递给Detail组件
 props:true，
 //方式三：值为函数
 props($route){
     return {id:$route.query.id}
 }
}
//Detail组件中使用props接收
props:['xxx','xxx'] 
```

> router-link的replace属性

```
1.作用：控制路由跳转时操作浏览器历史记录的模式
2.浏览器的历史记录有两种写入方式：分别为push和replace,push时追加历史记录，replace是替换栈顶记录，路由跳转时默认为push
3.如何开启replace：<router-link replace>
```

> 编程式路由导航,不借助router-link实现路由跳转

```
this.$router.push({
	name:'xxx',
	path:'xxx',
	query:{
	 id:1
	}
})
this.$router.replace({
	name:'xxx',
	path:'xxx',
	query:{
	 id:1
	}
})
this.$router.back()
this.$router.forward() 
this.$router.go()
```

> 缓存路由组件

```
1.作用:让不展示的路由组件保持挂载，不被销毁
<keep-alive include='Home'>  include后为组件名,多个: :include="['Home','About']"
	<router-view></router-view>
</keep-alive>

2.两个路由组件独有的生命周期
activated(){
	组件被激活时调用
}
deactivated(){
	组件失活时调用
}
```

> 路由守卫

```js
//作用：对路由进行权限控制
//分类：全局守卫，独享守卫，组件内守卫
//router/index.js
const router= new VueRouter({
  routes:[
  	{
  	name:'home',
  	path:'/home',
  	compoent:Home,
    meta:{//路由元信息
        isAuth:true,//表示需要进行权限校验
        title:'主页'
   	 }    
  	},
  	{
  	name:'about',
  	path:'/about',
  	compoent:About
  	}
  ]
})
//全局前置路由守卫,每次切换路由之前被调用，初始化时调用
router.beforeEach((to,from,next)=>{
	if(to.meta.isAuth){
		if(locaStorage.getItem('username')=='atguigu'){
			next()//放行
		}else{
            console.log('无权限')
        }
	}else{
         next()
    }
})
//全局后置路由守卫，每次切换路由之后被调用
router.afterEach((to,from)=>{
    if(to.meta.title){
        document.title=to.meta.title
    }else{
        document.title='xxx'
    }
})
export default router



//独享路由守卫
const router= new VueRouter({
  routes:[
  	{
  	name:'home',
  	path:'/home',
  	compoent:Home,
    meta:{//路由元信息
        isAuth:true,//表示需要进行权限校验
        title:'主页'
   	 },
    //切换到当前路由之前调用    
    beforeEnter((to,from,next)=>{
        xxx
    }) 
  	},
  	{
  	name:'about',
  	path:'/about',
  	compoent:About
  	}
  ]
})


//组件内路由守卫
export default{
    name:'Home',
    data(){return {}},
    //通过路由规则进入该组件时被调用
    beforeRouterEnter(to,from,next){
        
    },
     //通过路由规则离开该组件时被调用
    beforeRouterLeave(to,from,next){
        
    }
}
```

> history模式与hash模式

```js
*-//hash兼容性较好,地址栏有#,#及后面的hash不会包含在http请求，hash值不会带给服务器，不会造成前端路由导致找不到后端资源的问题
//history，地址无#，兼容性略差，部署需要解决页面服务端404问题
const router= new VueRouter({
  mode:'history',//默认为hash	
  routes:[
  	{
  	name:'home',
  	path:'/home',
  	compoent:Home,
    meta:{//路由元信息
        isAuth:true,//表示需要进行权限校验
        title:'主页'
   	 },
  	},
  	{
  	name:'about',
  	path:'/about',
  	compoent:About
  	}
  ]
})

//history解决访问资源404问题：找后端大哥，例如nodejs中的connect-history-api-fallback
const express =require('express')
const history=require('connect-history-api-fallback')
const app=express()
app.use(history())
```

