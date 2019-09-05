### npm

**1. dependencies 和 devDependencies 的区别**

https://segmentfault.com/a/1190000008398819

一个项目有很多依赖，这些依赖自己也有很多依赖。
执行npm install时，项目会安装自身的所有dependency和devDependency。
也会安装项目的依赖的dependency，但是不会安装项目的依赖的devDependency

所以，一般webpack、bable等用来测试、打包和转换的包会放在devDependency中，而正常功能所依赖的包就放在devDependency中。这样，当你的项目被别的项目引用时，就不会下载那些devDependency中包了。



**2. peerDependencies**

这一个属性的意思是：使用到本项目的项目，必须要安装peerDependencies下面的依赖，否则程序可能会出现异常。
类似于于webpack下面定义的externals

一个真实的例子是：react-redux，这个包声明了react和redux作为peerDependencies，因为使用react-redux的时候必须要安装这两个包。这两个包在devDependencies中也有定义，这是因为开发的时候要安装它们。



**3. 包**

```bash

yarn add -D babel-core babel-polyfill babel-loader babel-preset-env babel-preset-react babel-preset-stage-0 babel-plugin-transform-class-properties babel-plugin-transform-decorators babel-plugin-transform-object-rest-spread html-webpack-plugin react-hot-loader css-loader html-webpack-plugin webpack webpack-dev-server webpack-merge style-loader webpack-cli

yarn add react react-dom redux react-redux ramda axios
```







## nrm
管理npm的各种registry

<https://www.cnblogs.com/wangmeijian/p/7072053.html>

nrm ls

nrm current

nrm use cnpm

nrm add unpm --registry=<http://rnpm.uyundev.cn>

nrm test npm

nrm del unpm

---
## n
管理node的版本

1. **n**: Output versions installed
2. **n ls**: Output the versions or node availiable
3. **n \<version\>**: Install node \<versoin\>
4. **n stable**: Install or activate the latest stable node release
5. **n latest**: Install or activate the latest node release


https://www.npmjs.com/package/n


## npm-check
自动找出需要升级的npm包，并更新

https://github.com/dylang/npm-check





## npm

#### [介绍](<https://juejin.im/post/5d08d3d3f265da1b7e103a4d#heading-2>)



**1. package.json 中的bin字段：**

表示的是一个可执行文件到指定文件源的映射。

当这个包被当作依赖安装的时候，该文件源会被放到 `node_modules/.bin` 目录中



**2. node_modules中的bin:**

可以在package.json的scripts字段中执行

可以通过npx在终端中执行：[npx](<http://www.ruanyifeng.com/blog/2019/02/npx.html>) 的作用就是为了方便调用项目内部安装的模块

可以直接在终端中用相对路径执行： `./node_modules/.bin/uuid`



在`.bin`目录下执行`ll` 可以查看及具体的软链接指向



**3. 为什么通过`npm run`可以执行局部安装的命令行包呢？**

是因为**每当执行npm run时，会自动新建一个Shell，这个 Shell会将当前项目的node_modules/.bin的绝对路径加入到环境变量PATH中，执行结束后，再将环境变量PATH恢复原样**。

可以执行  `npm run env | grep PATH` 来验证



**4. 串型执行：** `npm run script1 && npm run script2`

> 只要一个命令执行失败，则整个脚本终止。



**5. 并行执行：** `npm run script1 & npm run script2`

> 多个命令可以同时的平行执行



> npm config list



### npm 命令



#### 模块管理

1. 检查当前项目依赖的所有模块，包括子模块，以及子模块的子模块

   > npm list/ls

2. npm info packageName: 查看某个模块的信息

3. npm outdated: 查看项目中可升级的模块

4. npm prune: 整理项目中无关的模块

#### 查看模块文档

1. npm repo packageName: 打开模块的github主页
2. npm docs packageName: 打开模块的文档地址
3. npm bugs packageName: 打开模块的issues地址



#### 项目版本管理

在`package.json`中有个`version`字段代表的是该模块的版本信息。当发布新版本时，`version`字段也要进行相应的改变。虽然可以手动的修改`vsersion`字段，但是为了整个发布的过程的自动化，最好还是使用指令的形式来进行修改：

>npm version major // 大版本号加1
>npm version minor // 小版本号字加1
>npm version patch // 补丁号加1



#### 模块全局化

假设你在开发一个模块`A`，同时需要在另外一个项目`B`中测试它，当然你可以将该模块的代码拷贝到需要使用它的项目中，但这也不是理想的方法，可以在模块`A`的目录下执行：`npm link`

`npm link`命令通过链接目录和可执行文件，实现任意位置的`npm`包命令的全局可执行。

`npm link`主要做了两件事：

1. 为目标`npm`模块创建软链接，将其链接到全局`node`模块安装路径`/usr/local/lib/node_modules/`
2. 为目标`npm`模块的可执行`bin`文件创建软链接，将其链接到全局`node`命令安装路径`/usr/local/bin/`



### 实用命令

1. `npm pack jquery`: 会将jquery以.tgz包的形式下载下来，然后可以用`tar -xzf jquyery.tar` 解压到本地，适用于临时需要一个包的情况。

