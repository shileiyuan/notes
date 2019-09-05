1. 直接运行： bable my-file.js

2. 将结果写入到指定文件： babel my-file.js -o(--out-file) complied.js

3. 将结果写入到指定目录： babel my-file.js -d(--out-dir) lib



**presets是一组plugin的意思**



**polyfill**

有些特性如Array.from IE不支持，就需要兼容性补丁polyfill。安装bable-polyfill，然后只需要在文件顶部导入import 'babel-polyfill' 即可。或者在webpack.config中添加entry: ['babel-polyfill', '.app.js']



**babel-runtime**

babel翻译代码的时候需要一些工具方法，这些方法默认都会加到用到他们的文件的开头，有时这些方法会很长。

babel-runtime就是为了优化这种情况的，将这些工具方法抽离出来，不用每个文件都引入

安装babel-runtime 和 babel-plugin-transform-runtime， 然后更新.babelrc: {"plugins": ["transform-runtime"]}



**根据环境变量来设置**：

process.env.BABEL_ENV，如没有，将替换成NODE_ENV, 如也没有,默认为development

windows下设置： set BABEL_ENV=production, 然后在代码中就可以通过process.env.BABEL_ENV来得到该值了



**在npm中搜索babel-plugin-react-transform可以用来做热加载**



**使用es7的async和await语法：**

安装babel-plugin-syntax-async-functions 和 bable-plugin-transform-regenerator

更新.babelrc {"plugins": ["syntax-async-functions", "transform-regenerator"]} 



## bable7 理解

@babel/polyfill: 引用两个包 `core-js` 和 `regenerator-runtime`

@babel/runtime: 引用一个包`regenerator-runtime`, 它的devDependencies引用`@babel-helpers`

@babel/runtime-corejs2: 引用两个包`core-js`和`regenerator-runtime`, devDependencies引用`@babel/helpers`

core-js 是各种polyfill的集合，比如Set、Promise等，用来提供es6的环境

regenerator-runtime 用来实现generator和async/await

@babel-helpers 是babel需要的一些函数，默认是放在每个文件的头部的，现在将其抽出来放到一起供各个文件引用这些函数，可以减小代码体积。

runtime-corejs2 是polyfill和runtime的并集，所以只需要引入@babel/runtime-corejs2就可以替代@babel/polyfill和@babel/runtime了，然后要配置一下
`["@babel/plugin-transform-runtime", {"corejs": 2}]`

@babel/plugin-transform-runtime 是用来和@babel/runtime或者@babel/runtime-corejs2搭配使用的

@babel/preset-env： stage-x plugin都是一些还没有成为es标准的提案，env是不支持这些plugin的

targets： 如果不设置，就相当于声明了所有ES2015+的plugin
modules： 用来设置要将ES6模块转换成的模块系统，可选值有：amd umd commonjs 等，如果设置为false，就不会转换
useBuiltIns: 用来按需引入core-js的， 可选值： entry usage false
  entry：根据环境引入不同的polyfills
  usage：还处于实验阶段，按需引入，不需要手动导入polyfills了
  false：不做任何处理，会引入全部的polyfills

所以如果自己写一个node程序的话，需要安装的包：
`yarn add -D @babel/cli @babel/core @babel/plugin-transform-runtime @babel/preset-env`

`yarn add @babel/runtime-corejs2`

配置文件babel.config.js
```javascript
const presets = [
  [
    "@babel/env",
    {
      targets: {
        node: "10"
      },
      // usage会使 polyfill 自动按需引入
      // 如果设置了plugin-tranform-runtime，
      // 那么静态方法的按需引入由transform-runtime完成
      // 实例方法的按需引入仍然由这个usage完成
      useBuiltIns: "usage",
    },
  ],
];
const plugins = [
  [
    "@babel/plugin-transform-runtime", {
      "corejs": 2, // 默认为false，使用@babel/runtime， 2就是使用@babel/runtime-corejs2
      "helpers": true,
      "regenerator": true,
      // babel的helpers方法，默认会被转换成commonjs的，如果useESModules为true，就不会转换
      "useESModules": false
    }
  ]
]

module.exports = { presets, plugins }
```

loose: 默认为false，如果为true，babel就会尽量少转换

debug：会打印targets和用到的plugins


@babel-register: 引用了 core-js , 会在代码运行的时候，hook require(), 用来开发时，动态的转换js
使用:
```javascript
require("@babel/register");
```

`@babel`/plugin-tranform-runtime` 只能转换静态方法和constructors, 不能转换实例方法。因为静态方法可以可以返回一个引用，只在当前的模块中引用而不污染全局环境。实例方法没办法通过返回引用的方式转换，只能通过polyfill的方式转换。影响只是会污染全局变量，而不是不可以用。