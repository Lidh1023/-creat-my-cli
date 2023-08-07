# -creat-my-cli
## 创建一个脚手架

### process.env环境变量

#### process.env环境变量配置
1、process 对象是一个全局变量，提供了有关当前 Node.js 进程的信息并对其进行控制
2、process.env 属性会返回包含用户环境的对象

##### 为什么需要环境变量
1、版本差异
  一个项目从开发到上线一般都会经历三个过程，从开发版本到测试版本到生产版本。每个版本都有对应的相同的或者不同的后端数据库、api地址、前端编译环境。用于区分不同环境作用，拿前端编译环境来说，开发环境需要启动热更新、不需要启动代码压缩混淆；生产版本不需要启动热更新，需要启动代码压缩混淆。
2、请求前缀
  日常开发过程，都会封装请求方法类，其他模块直接调用请求方法类传递请求头、请求参数等即可发送http请求。
  然而真实的开发过程每个版本都会对应一个不同的请求地址。
3、其他
  系统基础信息
  系统访问异常返回信息
  其他在不同环境上单独配置的信息

#### 原理
采用nodejs顶层对象中process基础类下process.env属性，返回包含用户环境的对象。根据各个环境的配置文件区分和控制

##### node环境变量
node环境变量指nodejs执行环境的环境变量,此处的nodejs指的是webpack的编译环境,通过npm script中的set属性设置
```js
  //package.json
"scripts": {
    "dev": "set NODE_ENV=develop &&  set PORT=8085 && webpack-dev-server --inline --progress  --config  build/webpack.dev.conf.js ",
    "build": "node build/build.js"
  },
```
在webpack编译过程中获取
```js
  //webpack.dev.conf.js
  const PORT = process.env.PORT && Number(process.env.PORT)
  console.log(process.env.NODE_ENV,process.env.PORT)
  // develop 8085
```
##### 客户端环境变量
客户端环境变量指在本地运行的代码中获取到的环境变量,通过webpack插件DefinePlugin 配置
```js
  //webpack.dev.conf.js
  plugins: [
    new webpack.DefinePlugin({
      'process.env': require('../config/dev.env')
    })
  ]
```
```js
  //dev.env.js
  'use strict'
  const merge = require('webpack-merge')
  module.exports = merge({
    NODE_ENV: '"development"'
  })
```
本地运行代码获取
```js
  created() {
    console.log(process.env)
    //{NODE_ENV: "development"}
  }
```
客户端环境变量只能通过console.log()打印，断点无法获取变量值