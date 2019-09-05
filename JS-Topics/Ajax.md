### xhr
```javascript
var xhr = new XMLHttpRequest()
xhr.setRequestHeader('Content-Type', 'text/plain')   // 设置请求头
xhr.getResponseHeader()  // 获取响应头
xhr.getAllRequestHeaders()  // 获取所有响应头   格式是\n分隔的字符串
```

### agent运行原理
在想要被收集数据的网站A上部署代码， 代码做的事情是加载 http://web.uyundev.cn/buriedPoint/YYRUM.js 文件，然后就可以收集数据上报了。
可视化埋点的时候，在UEM应用中用一个iframe加载网站A的文档，网站A的文档上已经有YYRUM.js这个文件了，所以这两个文档可以通过postMessage通信


### 跨域问题
**同源策略限制的是文档的来源， 而不是脚本本身的来源**
**解释**：如果文档引入另一个来源的脚本，则该脚本拥有对该文档的所有权限；但是当脚本打开另一个窗口并载入来自另一个主机的文档时，同源策略就会发挥作用而阻止脚本访问这个文档。

同源策略对XMLHttpRequest对象的限制：允许客户端JavaScript生成任意的HTTP请求到脚本所属文档的Web服务器，但是不允许脚本和其他Web服务器通信。

解决跨域问题的方法：
1. 适用于有多个子域的大站点： 默认情况下，document.domain存放的是载入文档的服务器的主机名。可以设置这个值：如将developer.mozilla.org 设置为mozilla.org,这两个文档就有了同源性。
2. 跨域资源共享技术（CORS）: 这个标准草案使用新的"Origin"请求头和新的Access-Control-Allow-Origin响应头来扩展HTTP。它允许服务器用头信息显式地列出源，或使用通配符来匹配所有的源，并允许从任何地址请求文件。
3. 跨文档消息：使用window对象上的postMessage方法，可以在两个不同源的文档中传递消息。

``'Access-Control-Allow-Origin': request.headers.origin || '*',
'Access-Control-Allow-Credentials': true``

### 自己封装的ajax方法：
```javascript
function ajax(url, config) {
  config = config || {};
  var withCredentials = config.withCredentials || false;
  var method = config.method ? config.method.toLowerCase() : 'get';
  var data = config.data;
  var headers = config.headers;
  var callback = config.callback;

  var xhr = new XMLHttpRequest()
  if (withCredentials) {
    xhr.withCredentials = true;
  }
  if (method === 'get') {
    if (data) {
      url = url + '?data=' + encodeURIComponent(JSON.stringify(data));
    }
  } else if (method === 'post') {
    if (data) {
      data = JSON.stringify(data);
    }
  }
  if (typeof callback === 'function') {
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4) {
        if (xhr.status >= 200 && xhr.status < 300 || xhr.status === 304) {
          callback(xhr.responseText);
        }
      }
    }
  }
  xhr.open(method, url, true);
  if (headers) {
    for (var key in headers) {
      xhr.setRequestHeader(key, headers[key]);
    }
  }
  xhr.send(data || null);
}
```

### JSONP
**jsonp可以实现的基本前提**：html页面可以引用远程js文件而不担心跨域问题。

**过程：** html页面有一个函数，var localfunc = function(data){ }; 远程服务器有一个js文件remote.js, 其内容为 localfunc({"name": "Jenny"})，则当html页面中的一个script标签请求remote.js时，就会执行localfunc, 从而实现了jsonp取数据的功能。

**优化：** 可以将函数的名称通过script标签的src属性的查询字符串传递给服务器。

**服务器端返回的数据：**res.write(func + '(' + JSON.stringify(responseObj) + ')');

**agent jsonp实现：**
```javascript
function jsonp(url, params, jsonp) {
  var encodeParams = encodeURIComponent(JSON.stringify(params)),
    encodeParamsLen = encodeParams.length, //字符长度
    totalArea = Math.ceil(encodeParamsLen / 2000), //如果参数过长需要分段
    uuid = util.uuid(); //同一段数据 这个用时间戳

  var sendScript = function (data, total, index, uuid) {
    var baseURL = url;
    var element = document.createElement('script');
    element.type = 'text/javascript';
    baseURL += '&data=' + data + '&total=' + total + '&index=' + i + '&uuid=' + uuid;
    if (jsonp) {
      baseURL += '&jsonp=' + jsonp
    }
    element.src = baseURL;
    var firstScript = document.getElementsByTagName('script')[0];
    firstScript.parentNode.insertBefore(element, firstScript);
    return element
  }
  var startIndex = 0,
    lastIndex = 0; //保存当前索引
  for (var i = 0; i < totalArea; i++) {
    lastIndex = lastIndex + 2000
    // decodeURIComponent('%')会报错
    while (encodeParams[lastIndex - 1] == '%' || encodeParams[lastIndex - 2] == '%') {
      lastIndex = lastIndex + 2;
    }
    var s = sendScript(encodeParams.substring(startIndex, lastIndex), totalArea, i, uuid);
    s = null; //释放内存
    startIndex = lastIndex;
  }
}
```

### 给没有实现ontime方法的浏览器实现超时功能
```javascript
function getText(url, timeout, callback) {

  var xhr = new XMLHttpRequest();
  var timedout = false;

  // 启动计时器，在timeout毫秒之后终止请求
  var timer = setTimeout(function () {
    timedout = true;
    xhr.abort();
  }, timeout);

  xhr.open('GET', url);
  // xhr.setRequestHeader('Content-Type', 'text/plain;charset=UTF-8');

  xhr.onreadystatechange = function () {
    if (xhr.readyState !== 4) {
      console.log(xhr.readyState);
      return;
    }
    if (timedout) {
      console.log('已经超时');
      return;
    }
    clearTimeout(timer);
    if (xhr.status === 200) {
      callback(xhr.responseText);
    } else {
      console.log('status: ', xhr.status);
    }
  }

  xhr.send(null)
}

var btn = document.getElementById('request');
btn.addEventListener('click', function () {
  getText('http://localhost:3003/api', 10, function (data) {
    console.log('data: ', data);
  });
})
```

### 请求队列
```javascript
// 仅当前一个请求返回之后才调用后一个请求
var RequestQueue = function () {
  var queue = [];
  var isPosting = false;
  var run = function () {
    if (queue.length > 0) {
      isPosting = true;
      var item = queue.shift();
      var callback = item.config.callback;
      item.config.callback = function (response) {
        try {
          if (typeof callback === 'function') {
            callback(response);
          }
        } finally {
          isPosting = false;
          run();
        }
      };
      ajax(item.url, item.config);
    }
  }
  var instance = {
    push: function (url, config) {
      queue.push({ url: url, config: config });
      if (isPosting === false) {
        run();
      }
    }
  }
  return instance;
}
```