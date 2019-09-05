### entry
多入口

### output


[Writing a Loader](https://webpack.js.org/contribute/writing-a-loader/)

如果只用lodash中的一个方法，不用非得导入整个库。 treeshake


cache: [Mainfest](https://webpack.js.org/concepts/manifest/)

### HMR
Similarly, when implementing the HMR interface in a module, you can describe what should happen when the module is updated. However, in most cases, it's not mandatory to write HMR code in every module. If a module has no HMR handlers, the update bubbles up. This means that a single handler can update a complete module tree. If a single module from the tree is updated, the entire set of dependencies is reloaded.

### webpack-dev-server
**contentBase:** 这个选项只在你需要serve静态文件的时候才是必要的。 devServer.publicPath will be used to determine where the bundles should be served from, and takes precedence. 默认值是当前工作目录。


**publickPath:** The bundled files will be available in the browser under this path. 默认值是/。publicPath前后都必须有一条斜杠： /abc/

**historyApiFallback：** When using the HTML5 History API, the index.html page will likely have to be served in place of any 404 responses.

**headers:** Adds headers to all responses

**host:** Specify a host to use. By default this is localhost. If you want your server to be accessible externally, specify it like this: '0.0.0.0'

**proxy:**
```javascript
// /api/users --> http://localhost:3000/api/users
proxy: {
  '/api': 'http://localhost:3000'
}

// /api/users --> http://localhost:3000/users
proxy: {
 '/api': {
    target: 'http://localhost:3000',
    pathRewrite: {'^/api' : ''}
  }
}

// 多个路径代理到同一个target的情况
proxy: [{
  context: ['/auth', '/api'],
  target: 'http://localhost:3000',
}]

// A backend server running on HTTPS with an invalid certificate will not be accepted by default. If you want to, modify your config like this:
proxy: {
  '/api': {
    target: 'https://other-server.example.com',
    secure: false
  }
}
```

### Hot Module Replacement
https://webpack.js.org/guides/hot-module-replacement/
https://github.com/gaearon/react-hot-loader

### Tree Shaking
去掉所有的dead code