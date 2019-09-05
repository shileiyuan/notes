### 简单服务器

```javascript
const http = require('http');

const HOSTNAME = '127.0.0.1';
const PORT = 3000;
const server = http.createServer((request, response) => {

  const { headers, method, url } = request;
  let body = [];
  request.on('error', (err) => {
    console.error(err);
  }).on('data', (chunk) => {
    body.push(chunk);
  }).on('end', () => {
    body = Buffer.concat(body).toString();
    // BEGINNING OF NEW STUFF

    response.on('error', (err) => {
      console.error(err);
    });

    response.statusCode = 200;
    response.setHeader('Content-Type', 'application/json');
    // Note: the 2 lines above could be replaced with this next one:
    // response.writeHead(200, {'Content-Type': 'application/json'})

    const responseBody = { headers, method, url, body };

    response.write(JSON.stringify(responseBody));
    response.end();
    // Note: the 2 lines above could be replaced with this next one:
    // response.end(JSON.stringify(responseBody))

    // END OF NEW STUFF
  });
});

server.listen(PORT, HOSTNAME, () => {
  console.log(`server is running at http://${HOSTNAME}:${PORT}/`)
})
```



### 路由

一个只用来做路由的库： [router](https://www.npmjs.com/package/router)

```javascript
// 自己写的路由的例子
const http = require('http');
const urlUtil = require('url');

const HOSTNAME = '127.0.0.1';
const PORT = 3000;
const log = console.log.bind(console);

const router = {
  gets: {},
  posts: {},
  init(request, response) {
    const { headers, method, url, body } = request;
    const pathname = urlUtil.parse(url).pathname;
    const lowerMethod = method.toLowerCase();
    let callback;
    switch (lowerMethod) {
      case 'get':
        callback = this.gets[pathname];
        break;
      case 'post':
        callback = this.posts[pathname];
        break;
      default:
        response.writeHead(400);
        response.end(`不支持的请求方式: ${method}`);
    }
    if (typeof callback === 'function') {
      callback(request, response);
    } else {
      response.writeHead(404);
      response.end(`找不到${url}`);
    }
  },
  get(url, callback) {
    const path = url;
    this.gets[path] = callback;
  },
  post(url, callback) {
    const path = url;
    this.posts[path] = callback;
  }
}

const server = http.createServer((request, response) => {
  let body = [];
  request.on('error', (err) => {
    console.error(err);
  }).on('data', (chunk) => {
    body.push(chunk);
  }).on('end', () => {
    body = Buffer.concat(body).toString();
    response.on('error', (err) => {
      console.error(err);
    });
    request.body = body;
    router.init(request, response);
  });
});

router.get('/message', (request, response) => {
  response.writeHead(200);
  response.end('Hello World')
});

server.listen(PORT, HOSTNAME, () => {
  console.log(`server is running at http://${HOSTNAME}:${PORT}/`)
})
```