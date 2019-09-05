## 功能
用VScode调试，用babel编译，可生成sourcemap，用nodemon做热加载，不需要webpack

## 注意
该项目用来作为开发web程序的starter，**不能用来开发命令行工具**。
因为nodemon已经在运行node程序了，vscode的debugger只是附加到nodemon上而已。

开发命令行工具请看cli-starter

```bash
"dev": "babel src --out-dir ./lib --watch --source-maps"
```

## 配置

### 1. 安装依赖
> yarn add -D @babel/core @babel/plugin-transform-runtime @babel/preset-env @babel/register babel-eslint eslint eslint-config-standard eslint-plugin-import eslint-plugin-node eslint-plugin-promise eslint-plugin-standard nodemon

> yarn add @babel/runtime-corejs2

### 2. babel.config.js

```javascript
module.exports = {
  presets: [
    [
      '@babel/env',
      {
        targets: {
          node: '10'
        },
        // usage会使 polyfill 自动按需引入
        // 如果设置了plugin-tranform-runtime，
        // 那么静态方法的按需引入由transform-runtime完成
        // 实例方法的按需引入仍然由这个usage完成
        useBuiltIns: 'usage'
      }
    ]
  ],
  plugins: [
    [
      '@babel/plugin-transform-runtime', {
        'corejs': 2, // 默认为false，使用@babel/runtime， 2就是使用@babel/runtime-corejs2
        'helpers': true,
        'regenerator': true,
        // babel的helpers方法，默认会被转换成commonjs的，如果useESModules为true，就不会转换
        'useESModules': false
      }
    ]
  ],
  sourceMaps: true,
  retainLines: true
}
```

### 3. 入口文件start.js
```javascript
require('@babel/register')
// 有可能需要babel-polyfill 不确定
require('./index.js')
```

### 4. launch.json
```json
{
  "type": "node",
  "request": "attach",
  "name": "Node: Nodemon",
  "port": 9230,
  "restart": true,
}
```

### 5. package.json
```json
"scripts": {
  "dev": "nodemon --inspect=9230 src/start.js"
}
```