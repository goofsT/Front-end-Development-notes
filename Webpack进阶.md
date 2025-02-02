# Webpack高级进阶学习笔记

# Vue中的配置分析

>将基于脚手架4.x版本的vue2.x配置进行分析,大致内容与react无太大区别
>
>1、卸载老版本vuecli（1.x， 2.x）:`npm uninstall vue-cli -g`或者`yarn global remove vue-cli`
>
>2、安装最新的vuecli:`npm install -g @vue/cli`
>
>3、`vue create project-name`,选择创建vue2.x,vue2与3版本的weback配置差不多
>
>4、vue并没有提供将webpack配置直接暴露出来的命令,但是提供了另外一种方法让我们能审查vue里面的配置,通过指令将单独的配置单独打包成单独的文件
>
>​	Ⅰ-`vue inspect --mode=development > webpack.dev.js` :将webpack的开发环境单独打包
>
>​	Ⅱ-`vue inspect --mode=production > webpack.prod.js` :将webpack的生产环境单独打包
>
>​	Ⅲ-生成后的文件会报错,想要取消报错,在最前面用`module.exports=`将其暴露出去,就不会报错了





# 三、loader

### Ⅰ-编写一个简单的loader

>loader本质上是一种函数,此处创建一个js编写loader

```js
//loader1.js
module.exports = function (content, map, meta) {
  console.log(content,"------------------------------------");
  return content
}
```

> 1、如不配置loader解析规则,默认路径是`node_modules`,所以平时使用`style-loader`等都不用加路径
>
> webpack.config.js

```js
const path = require('path');
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        loader: 'loader1',//当配置了liader解析规则后写法
        // loader:path.resolve(__dirname,'loaders','loader1') //不写resolveLoader配置的写法
      }
    ]
  },
  // 配置loader解析规则,默认路径是`node_modules`
  resolveLoader: {
    modules: [
      'node_modules',
      path.resolve(__dirname, 'loaders')
    ]
  }
}
```

### Ⅱ-loader的执行顺序

>1、loader是从上往下编译,编译结束后运行的顺序是`从下往上,从右往左 `执行
>
>2、当你有写loader想要先行运行的话,可以加在`pitch`方法上,这样在编译时就会调用,且`从上往下`执行

```js
module.exports = function (content, map, meta) {
  console.log(111111,"------------------------------------");
  return content
}
module.exports.pitch = function () {//编译时从上往下调用
  console.log('pitch 111');
}
```

### Ⅲ-同步&异步loader

> 1、`this.callback()`:同步的方法  -->可以替代`return`

```js
module.exports = function (content, map, meta) {
  console.log(111111,"------------------------------------");
  this.callback(null, content, map, meta);
	//是否有错误,需要传递的内容(处理后的内容) ,可选参数,可选参数
}
```

> 2、`this.async()`:异步的方法
>
> ​	使用`this.async()`方法会使整个loader停住,只有当你再次调用`callback`方法才会继续执行,整体性能会好过同步,`推荐使用`

```js
// 异步loader
module.exports = function (content, map, meta) {
  console.log(222);
  const callback = this.async();
  setTimeout(() => {
    callback(null, content);
  }, 1000)
}

```

### Ⅳ-获取&校验loader的options

> 1、需要下载依赖:`npm i loader-utils -D`
>
> 2、需要下载依赖:`schema-utils`

```js
//webpack.config.js传入
{	loader: 'loader3',
    options: {
        name: 'jack',
        age: 18 //当校验文件追加属性调为false,将会报错
    }
}
------------------------------------------------------------------------------------
//loader3.js
// loader本质上是一个函数
const { getOptions } = require('loader-utils');
const { validate } = require('schema-utils');
const schema = require('./schema');
module.exports = function (content, map, meta) {
  // 获取options
  const options = getOptions(this);
  console.log(333, options);
  // 校验options是否合法
  validate(schema, options, {
    name: 'loader3'
  })
  return content;
}
```

> 校验文件:定义校验规则

```json
{
  "type": "object",//指定options的类型
  "properties": { //定义options里面有什么属性
    "name": {	//定义有name类型
      "type": "string",	
      "description": "名称～"	//定义描述,可以随便写
    }
  },
  "additionalProperties": false //代表是否可以运行追加其他属性
}
```

### Ⅴ-自定义babel-loader

> 自定义babel-loader,并不是工作中的babel配置

> webpack.config.js配置

```js
module.exports = {
  module: {
    rules: [{
      test: /\.js$/,
      loader:'babelLoader',
      options:{ presets:['@babel/preset-env'] }
    }]
  },
  // 配置loader解析规则,默认路径是`node_modules`
  resolveLoader: {  modules: [ 'node_modules',path.resolve(__dirname, 'loaders')]}
}
```

> babelLoader.js  -->中间大写是防止与自带的babelloader冲突

```js
const { getOptions } = require('loader-utils');
const { validate } = require('schema-utils');
const babel = require('@babel/core');
const util = require('util');
const babelSchema = require('./babelSchema.json');
//babel.transfrom用来编译代码的方法,是一个普通异步方法
//util.promisify会将普通异步方法转换成基于promise的异步方法
const transform=util.promisify(babel.transform)

module.exports = function (content, map, meta) {
  //获取loader的options配置
  const options = getOptions(this) || {};
  //校验babel的options的配置
  validate(babelSchema,options,{
    name:'Babel Loader'
  })
  //创建异步
  const callback=this.async();
  //使用babel编译代码
  transform(content,options)
  .then(({code,map})=>callback(null,code,map,meta))
  .catch((e)=>callback(e))
}
```

> 校验文件:babelSchema.json

```json
{
  "type": "object",
  "properties": {
    "presets": {
      "type": "array"
    }
  },
  "addtionalProperties": true
}
```

# 四、plugins

### Ⅰ-tabaple介绍与使用

> 安装tapable：npm install tapable -D
>
> 初始化hooks容器 2.1 同步hooks，任务会依次执行:SyncHook、SyncBailHook 2.2 异步hooks，异步并行：AsyncParallelHook，异步串行：AsyncSeriesHook
>
> 往hooks容器中注册事件/添加回调函数
>
> 触发hooks
>
> 启动文件：node tapable.test.js

```js
const { SyncHook, SyncBailHook, AsyncParallelHook, AsyncSeriesHook } = require('tapable');
class Lesson {
  constructor() {
    // 初始化hooks容器
    this.hooks = {
      // 同步hooks，任务回依次执行
      // go: new SyncHook(['address'])
      // SyncBailHook：一旦有返回值就会退出～
      go: new SyncBailHook(['address']),

      // 异步hooks
      // AsyncParallelHook：异步并行
      // leave: new AsyncParallelHook(['name', 'age']),
      // AsyncSeriesHook: 异步串行
      leave: new AsyncSeriesHook(['name', 'age'])
    }
  }
  tap() {
    // 往hooks容器中注册事件/添加回调函数
    this.hooks.go.tap('class0318', (address) => {
      console.log('class0318', address);
      return 111;
    })
    this.hooks.go.tap('class0410', (address) => {
      console.log('class0410', address);
    })

    this.hooks.leave.tapAsync('class0510', (name, age, cb) => {
      setTimeout(() => {
        console.log('class0510', name, age);
        cb();
      }, 2000)
    })

    this.hooks.leave.tapPromise('class0610', (name, age) => {
      return new Promise((resolve) => {
        setTimeout(() => {
          console.log('class0610', name, age);
          resolve();
        }, 1000)
      })
    })
  }
  start() {
    // 触发hooks
    this.hooks.go.call('c318');
    this.hooks.leave.callAsync('jack', 18, function () {
      // 代表所有leave容器中的函数触发完了，才触发
      console.log('end~~~');
    });
  }
}
const l = new Lesson();
l.tap();
l.start();
```

### Ⅱ- compiler钩子

>工作方式：异步串行执行，因此下面代码输出顺序如下： 1.1 emit.tap 111 1.2 1秒后输出 emit.tapAsync 111 1.3 1秒后输出 emit.tapPromise 111 1.4 afterEmit.tap 111 1.5 done.tap 111
>
>tapAsync和tapPromise表示异步

```js
class Plugin1 {
  apply(complier) {

    complier.hooks.emit.tap('Plugin1', (compilation) => {
      console.log('emit.tap 111');
    })

    complier.hooks.emit.tapAsync('Plugin1', (compilation, cb) => {
      setTimeout(() => {
        console.log('emit.tapAsync 111');
        cb();
      }, 1000)
    })

    complier.hooks.emit.tapPromise('Plugin1', (compilation) => {
      return new Promise((resolve) => {
        setTimeout(() => {
          console.log('emit.tapPromise 111');
          resolve();
        }, 1000)
      })
    })

    complier.hooks.afterEmit.tap('Plugin1', (compilation) => {
      console.log('afterEmit.tap 111');
    })

    complier.hooks.done.tap('Plugin1', (stats) => {
      console.log('done.tap 111');
    })

  }
}

module.exports = Plugin1;
```

>此部分剩余部分,暂时跳过,并无太多实践,以后再来补足
>
>

# 五、自定义webpack

### Ⅰ-webpack执行流程

>1、初始化 Compiler：webpack(config) 得到 Compiler 对象
>
>2、开始编译：调用 Compiler 对象 run 方法开始执行编译
>
>3、确定入口：根据配置中的 entry 找出所有的入口文件。
>
>4、编译模块：从入口文件出发，调用所有配置的 Loader 对模块进行编译，再找出该模块依赖的模块，递归直到所有模块被加载进来
>
>5、完成模块编译： 在经过第 4 步使用 Loader 编译完所有模块后，得到了每个模块被编译后的最终内容以及它们之间的依赖关系。
>
>6、输出资源：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表。（注意：这步是可以修改输出内容的最后机会）
>
>7、输出完成：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统

### Ⅱ-准备工作

>1、创建文件夹myWebpack
>
>2、创建src-->(add.js / count.js / index.js)，写入对应的js代码
>
>3、创建config-->webpack.config.js写入webpack基础配置（entry和output）
>
>4、创建lib文件夹，里面写webpack的主要配置
>
>5、创建script-->build.js（将lib文件夹下面的myWebpack核心代码和config文件下的webpack基础配置引入并调用run()函数开始打包）
>
>6、为了方便启动，控制台通过输入命令 `npm init -y`拉取出package.json文件，修改文件中scripts部分为`"build": "node ./script/build.js"`表示通过在终端输入命令`npm run build`时会运行/script/build.js文
>
>7、如果需要断点调试:在scripts中添加`"debug": "node --inspect-brk ./script/build.js"`表示通过在终端输入命令`npm run debug`时会调试/script/build.js文件中的代码

> 下面部分暂停学习,转回React系统学习 2021/3/6































