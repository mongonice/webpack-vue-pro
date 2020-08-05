![是的，你真的会跳舞，相信自己你真的会跳舞](https://imgkr.cn-bj.ufileos.com/5a820626-482d-4c2c-94aa-6f77c4ff9e21.gif)

项目说明：本工程项目适用于嵌入移动端App中的H5页面，具有简单、一次性(基本无迭代)、可共用一套webpack配置进行编译打包的特点，每次开发新项目页面时，在**src**目录下创建新项目目录，每个新建的项目都有自己单独的入口文件`main.js`，当编译打包的时候执行`npm run dev 项目名称`即可

# 项目目录结构
```bash

.
├── config
│   ├── webpack.common.js  // 通用配置
│   ├── webpack.dev.js     // 开发配置
│   ├── webpack.prod.js    // 生产配置
│   └── webpack.staging.js // 测试环境配置
├── package-lock.json
├── package.json
└── src
    ├── actDemo1   // x同学开发了一个活动名为actDemo1项目
    │   ├── index.html
    │   ├── main.js
    │   └── title.js
    └── actDemo2  // y同学开发了一个活动名为actDemo2项目
        ├── index.html
        └── main.js

```
## 1 webpack4搭建项目初始化

项目之初就做`webpack`的`线上环境`和`开发环境`的分离。

先运行一下命令初始化项目

```bash
npm init -y
npm install webpack webapck-cli webpack-merge -D
npm install html-webpack-plugin -D  // 将编译打包好的js文件插入到html中
npm install -D webpack-dev-server  //起一个本地的服务器
```

再配置 package.json 中的`scripts`脚本命令：

```json
{
  "scripts": {
      "dev": "webpack-dev-server --color --env=development --config ./config/webpack.dev.js --m"
      "build": "webpack --color --env=production --config ./config/webpack.prod.js --m"
  }
}

```

## 2 webpack配置文件分割

```javascript
module.exports = {
  mode: '', // 'development' || 'production' 当前构建的环境
  devtool: '', // 启用sourceMap映射
  entry: '',   // 入口文件(一个页面配置一个入口文件)
  output: {},  // 输出文件
  module: {
      rules: [ // 配置各种加载器的规则
          
      ]
  },
  plugins: [], // 辅助插件
  devServer: {} // 开发环境中对webpack提供的本地server进行配置
}
```
下面操作只针对某个部位进行填充说明。

## 3 配置webpack.common.js

```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = (env, argv) => {
    // env是通过跑npm scripts脚本传过来的参数--env=xxxx
    // argv同env
    let moduleName = argv.m; // 跑了npm run dev actSweet50 那么moduleName = 'actSweet520'
    return {
        entry: path.resolve(__dirname, `../src/${moduleName}/main.js`),
        module: {
            rules: []
        },
        plugins: [
            new HtmlWebpackPlugin({
                template: path.resolve(__dirname, `../src/${moduleName}/index.html`)
            })
        ]
    }
}

```

## 3. 配置webpack.dev.js
- `path.resolve('.', ) `是指当前项目的根目录所在的绝对路径
- `path.resolve(__dirname, )` 是指当前`npm run dev`的`webpack.dev
.js`脚本所在目录的绝对路径

```javascript
const webpack = require('webpack'); //为了使用webpack的热更新
const {
    merge
} = require('webpack-merge'); // 坑，一开始直接是const merge = require('webpack-merge')，打印之后才发现： merge = { merge: f()}
const commonConf = require('./webpack.common.js'); // commonConf是个函数
const path = require('path');

module.exports = (env, argv) => {

    return merge(commonConf(env, argv), {
        mode: 'development',
        output: {
            filename: '[name].js',
            path: path.resolve(__dirname, 'dist')
        },

        plugins: [
            new webpack.HotModuleReplacementPlugin()
        ],

        devServer: {
            host: 'localhost',
            port: 3030,
            open: true,
            hot: true
        }
    })
}
```
写到这，开发环境就可以跑通了，在终端输入`npm run dev actSweet520`


## 4 给webpack.common.js添加loaders
由于loaders是生产和开发环境都需要配置的，并且基本相同，所以是要写到common配置中的，接下来就依依打招呼吧~

### 4.1 babel-loader(必须和babel-core同时安装，因为babel-core是核心代码)

```bash
npm install -D babel-loader @babel/core @babel/preset-env
```

安装之后到webpack.common.js中的`module`下的`rules[]`中添加

```javascript

{
  test: '/\.js$/',
  //除去node_modules不解析
  exclude: /(node_modules|bower_components)/,
  use: {
      loader: 'babel-loader',
      options: {
          presets: ['@babel/preset-env']
      }
  }
}
```

### 4.2 style-loader|css-loader|sass-loader|node-sass

说明：style-loader 和 css-loader是必须安装的，style-loader 是将编译好的css文件插入到header中，而css-loader是用到@import"url", background: url("")这些引入转成require方式引入，即将css引入转成commonJS
sass-loader依赖node-sass
```bash 
npm install -D style-loader css-loader sass-loader node-sass
```
接下来仍然在webpack.common.js中的`module`下的`rules[]`中添加

```javascript

{
    test: /\.(sa|sc|c)ss$/,
    use: [
      'style-loader',
      'css-loader',
      'sass-loader'
    ]
}
```

### 4.3 file-loader url-loader

```

```


### 4.4 postcss-loader autoprefixer


### 4.5 vue-loader(本项目是搭配vue框架构建)

```bash
npm install -D vue-loader vue-template-compiler
npm install -S vue vue-router
```
接下来进行配置吧
```javascript

const VueLoaderPlugin = require('vue-loader/lib/plugin')

{
  test: /\.vue$/,
  use: 'vue-loader'
}

plugins: [
   new VueLoaderPlugin()
]
```

### 4.6 移动端适配需安装 lib-flexible px2rem-loader raw-loader

```
npm install -D lib-flexible px2rem-loader raw-loader
```
配置

```
{

}
```


## 5 配置插件

### 5.1 定义全局变量：webpack.definePlugin

`definePlugin`定义全局变量的时候，键值如果是string类型的时候，一定要经过JSON.stringify处理，键值如果是Number类型或者Boolean类型不需要处理

```javascript

// config.js
module.exports = {
    
    dev: {
       apihost: '//10.238.11.39:1008/act/getList'
    },
    
    prod: {
       apihost: '//www.baidu.com/act/getList'
    }
}
//////////////////////////////////

// webpack.common.js

const handleGlobals = (globals) => {
    let finalGlobals = {};
    
    for(let key in globals) {
        finalGlobals[key] = JSON.stringify(globals[key])
    }
    
    return finalGlobals
}


module.exports = (env, argv) => {
    let _mode = env, moduleName = argv.m;
    
    let {dev, prod} = require(path.resolve('.', `src/${moduleName}/config.js`));
    
    let globalData = {
        "development": dev,
        "production": prod
    };
    
    let finalGlobals = handleGlobals(globalData[_mode])
    
    plugins: [
        new webpack.definePlugin(finalGlobals)
    ]
}
```

### 5.2 将引入的模块定义成全局：webpack.providePlugin

如果在你的js文件中频繁引入某一个第三方模块或者公共js文件，那么让它变成全局引用吧，这里有个小知识点： 定义全局的vue使用哪个版的呢？

- vue.runtime.js【带runtime的都是运行时，不完整版】
- vue.esm.js【完整版，带编译】
- vue.runtime.esm.js【运行时版】

运行时版比完整版体积小30%，但是运行时版不能再实例中使用`template`,如下：

```
// 因为
new Vue({
  
  'template': `<div>{{msg}}</div>`
})
```

```javascript


plugins: [

  new webpack.providePlugin({
      "coreBus": [path.resolve('.', 'src/__common/js/eventBus.js')],
      "utils": [path.resolve('.', 'src/__common/js/util.js')],
      "Vue": ['vue/dist/vue.runtime.esm.js', 'default']
  })
]
```

## 6 优化webpack配置
**针对生产环境production**
1. js代码压缩
2. css代码压缩
3. css从js文件中抽离成单个css文件
4. 启用多线程进行构建
5. polyfill配置
6. cdn配置
7. treeShaking删除没有用到的代码

### 6.1 js代码压缩使用terserWebpackPlugin

```javascript


```

### 6.2 css抽离mini-css-extract-plugin
将编译之后杂糅在js文件中的css代码抽离成单独的css文件

```javascript

plugins: [  
  new MiniCssExtractPlugin({
     filename: modeConf[_mode].cssFilename, // 设置输出的文件名
     chunkFilename: modeConf[_mode].cssChunkFilename
  })
]
```

### 6.3 












```markdown
链接：[文字](链接)
脚注：[文字](脚注解释 "脚注名字")
```

有人认为在[大前端时代](https://en.wikipedia.org/wiki/Front-end_web_development "Front-end web development")的背景下，移动端开发（Android、IOS）将逐步退出历史舞台。

[全栈工程师](是指掌握多种技能，并能利用多种技能独立完成产品的人。 "什么是全栈工程师")在业务开发流程中起到了至关重要的作用。

支持以下语言种类：

```
bash
clojure，cpp，cs，css
dart，dockerfile, diff
erlang
go，gradle，groovy
haskell
java，javascript，json，julia
kotlin
lisp，lua
makefile，markdown，matlab
objectivec
perl，php，python
r，ruby，rust
scala，shell，sql，swift
tex，typescript
verilog，vhdl
xml
yaml
```

```diff
+ 新增项
- 删除项
```
更多文档请参考 [markdown-nice-docs](https://preview.mdnice.com/articles/ "更多文档")

## 遇到的问题
1. 必须使用./app.vue 而非 app.vue
2. 千万不要在 use: [{}, {}]写空的配置，不然会报错：Error: No loader specified
