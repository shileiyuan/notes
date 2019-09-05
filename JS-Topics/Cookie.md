浏览器不管谁发起的请求，只看请求的地址。

无论在哪个域下，请求一个接口，都会将该接口地址下的cookie，发送给该接口。

cookie的domain只看主机地址，端口号不起作用。

某一个域A向另一个域B发请求，B的响应设置了cookie，这个cookie的domain是域B，而不是域A
而且在A下是看不到这个cookie的，在B下才可以看到。

一个接口设置了cookie，这个cookie是在这个接口的域下面。
其他的域如果请求了这个接口，就会带上这个cookie
```javascript
response.writeHead(200, {
  'content-type': 'application/json;charset=utf-8',
  'Access-Control-Allow-Origin': request.headers.origin || '*',
  'Access-Control-Allow-Credentials': true,
  'Set-Cookie': ["name=abc;HttpOnly;"，"food=fish;Max-Age=1000"],
})
response.end('s1')
```

http协议的header是不区分大小写的: HttpOnly与HttpOnLY是一样的效果

cookie默认是对同目录和子目录的其他path可见
如 http://localhost/abc/index.html 对 http://localhost/abc/order.html 和 http://localhost/abc/abc/index.html 可见
但是对 http://localhost/def/index.html 不可见

coookie的path，name（可能有domain但是没试过），用来唯一确定一个cookie
httponly， max-age都只是cookie的属性，用来控制cookie的表现，而不能唯一标识一个cookie
如，"name=abc;path=/" 和 "name=abc;path=/abc"是两个不同的cookie，它们可以共存
而"name=abc;httponly" 和 "name=abc;"是同一个cookie，后面那一个会覆盖前面那一个。

小范围可以设置为大范围，大范围不能设置为小范围。但是如果这个小范围本身就存在，就可以设置这个小范围。
客户端设置path和服务器端设置domain都是这个道理。
设置的cookie的默认的path为/。
如果在/abc下有一个name为food的cookie，那么就可以设置一个cookie，它的path为/abc，name为age（任意），但是不可以设置path为/def的cookie

max-age的单位是秒



```javascript
function cookieStorage(maxage, path) {
  var cookie = (function () {
    var cookie = {}
    var all = document.cookie
    if (all !== '') {
      all.split(/;\s*/).forEach(str => {
        const item = str.split('=')
        cookie[item[0]] = decodeURIComponent(item[1])
      })
    }
    return cookie
  }())

  var keys = Object.keys(cookie)
  return {
    length: keys.length,
    key(n) {
      if (n < 0 || n > keys.length) return null
      return keys[n]
    },
    getItem(name) {
      return cookie[name] || null
    },
    setItem(key, value) {
      if (!(key in cookie)) {
        keys.push(key)
        this.length++
      }
      cookie[key] = value
      var cookieStr = key + '=' + encodeURIComponent(value)
      if (maxage) cookie += '; max-age=' + maxage
      if (path) cookie += '; path=' + path
      document.cookie = cookieStr
    },
    removeItem(key) {
      if (!(key in cookie)) return;
      delete cookie[key]
      var index = keys.indexOf(key)
      keys.splice(index, 1)
      this.length--
      document.cookie = key + '=; max-age=0'
    },
    clear() {
      for (var i = 0, len = keys.length; i < len; i++) {
        document.cookie = keys[i] + '=; max-age=0'
      }
      cookie = {}
      keys = []
      this.length = 0
    }
  }
}

```