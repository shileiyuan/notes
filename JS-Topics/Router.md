```javascript
// 不带前进后退功能的HashRouter，详情请看hash.js
class HashRouter {
  constructor() {
    // 储存hash和callback键值对
    this.routes = {}
    this.currentUrl = ''
    this.handleHashChange = this.handleHashChange.bind(this)
    window.addEventListener('load', this.handleHashChange, false)
    window.addEventListener('hashchange', this.handleHashChange, false)
  }

  // 注册路由
  route(path, callback) {
    this.routes[path] = typeof callback === 'function' ? callback : function () { }
  }
  handleHashChange() {
    this.currentUrl = location.hash.slice(1) || '/';
    const callback = this.routes[this.currentUrl]
    typeof callback === 'function' && callback()
  }
}

const screen = document.querySelector('#screen')
const forwardBtn = document.querySelector('#forward')
const backoffBtn = document.querySelector('#backoff')
const router = new HashRouter(forwardBtn, backoffBtn)
window.router = router

function changeBgColor(color) {
  screen.style.backgroundColor = color
}
const routes = [
  {
    path: '/',
    color: 'yellow'
  },
  {
    path: '/blue',
    color: 'blue'
  }
]
routes.forEach(function (item) {
  router.route(item.path, function () {
    changeBgColor(item.color)
  })
})
```



```javascript
// History API
class Routers {
  constructor() {
    this.routes = {};
    // 在初始化时监听popstate事件
    this._bindPopState();
  }
  // 初始化路由
  init(path) {
    history.replaceState({path: path}, null, path);
    this.routes[path] && this.routes[path]();
  }
  // 将路径和对应回调函数加入hashMap储存
  route(path, callback) {
    this.routes[path] = callback || function() {};
  }

  // 触发路由对应回调
  go(path) {
    history.pushState({path: path}, null, path);
    this.routes[path] && this.routes[path]();
  }
  // 监听popstate事件
  _bindPopState() {
    window.addEventListener('popstate', e => {
      const path = e.state && e.state.path;
      this.routes[path] && this.routes[path]();
    });
  }
}
```



```html
// hash.html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <style>
    #screen {
      width: 100px;
      height: 100px;
      border: 1px solid black;
      margin-left: 200px;
    }
  </style>
</head>

<body>
  <div id='screen'></div>
  <ul>
    <li>
      <a href="#/">turn yellow</a>
    </li>
    <li>
      <a href="#/blue">turn blue</a>
    </li>
    <li>
      <a href="#/green">turn green</a>
    </li>
    <li>
      <a href="#/grey">turn grey</a>
    </li>
    <li>
      <a href="#/red">turn red</a>
    </li>
    <li>
      <a href="#/black">turn black</a>
    </li>
  </ul>
  <button id='backoff'>back</button>
  <br />
  <button id='forward'>forward</button>
  <script src='hash.js'></script>
</body>

</html>
```



```javascript
// hash.js
/**
 * 浏览器原生的逻辑：
 * 每次新点击一个hash，堆栈中大于当前index的记录会被清空，然后新的hash被添加到当前index的后面
 */
class HashRouter {
  constructor(forwardBtn, backoffBtn) {
    // 储存hash和callback键值对
    this.routes = {}
    // 当前hash
    this.currentUrl = ''
    // 历史记录
    this.history = []
    this.currentIndex = -1
    // 是否要触发hashchange事件
    this.canTrigger = true
    this.innerCanBackoff = false
    this.innerCanForward = false
    Object.defineProperty(this, 'canBackoff', {
      set(val) {
        this.innerCanBackoff = val
        backoffBtn.disabled = !val
      },
      get() {
        return this.innerCanBackoff
      }
    })
    Object.defineProperty(this, 'canForward', {
      set(val) {
        this.innerCanForward = val
        forwardBtn.disabled = !val
      },
      get() {
        return this.innerCanForward
      }
    })
    this.backoff = this.backoff.bind(this)
    this.forward = this.forward.bind(this)
    this.handleHashChange = this.handleHashChange.bind(this)
    this.pageLoad = this.pageLoad.bind(this)
    window.addEventListener('load', this.pageLoad, false)
    window.addEventListener('hashchange', this.handleHashChange, false)
  }

  // 注册路由
  route(path, callback) {
    this.routes[path] = typeof callback === 'function' ? callback : function () { }
  }
  pageLoad() {
    this.handleHashChange()
    this.canBackoff = false
  }
  handleHashChange() {
    if (this.canTrigger) {
      // 如果可以触发处理函数，说明是新增的hash
      this.setCurrentUrl(location.hash.slice(1) || '/')
      this.invokeCallback()
      this.currentIndex++
      this.history = this.history.slice(0, this.currentIndex)
      this.history.push(this.currentUrl)
      this.canBackoff = true
      this.canForward = false
    } else {
      this.canTrigger = true
    }
  }

  setCurrentUrl(currentUrl) {
    if (currentUrl) {
      this.currentUrl = currentUrl
    } else {
      this.currentUrl = this.history[this.currentIndex]
      location.hash = this.currentUrl
    }
  }
  invokeCallback() {
    document.title = this.currentUrl
    const callback = this.routes[this.currentUrl]
    typeof callback === 'function' && callback()
  }
  /**
   * 1. 点击前进后退的时候，要设置hash，会触发hashchange事件
   * 2. 在hashchange的处理事件中，我们会在history中添加一条记录
   * 3. 但是在点击前进后退的时候，不需要在history中添加记录
   * 所以，需要区分是点击前进后退，还是其他操作导致的hash改变
   * 但是还有一个问题没办法解决：当点击浏览器自带的前进和后退按钮的时候，history也要做相应的改变
   * 但是并没有事件触发
   */
  backoff() {
    if (!this.canBackoff) return
    this.canTrigger = false
    this.currentIndex--
    this.setCurrentUrl()
    this.invokeCallback()
    this.canBackoff = (this.currentIndex > 0)
    this.canForward = true
  }
  forward() {
    if (!this.canForward) return
    this.canTrigger = false
    this.currentIndex++
    this.setCurrentUrl()
    this.invokeCallback()
    this.canBackoff = true
    this.canForward = (this.currentIndex < this.history.length - 1)
  }
}

const screen = document.querySelector('#screen')
const forwardBtn = document.querySelector('#forward')
const backoffBtn = document.querySelector('#backoff')
const router = new HashRouter(forwardBtn, backoffBtn)
window.router = router

function changeBgColor(color) {
  screen.style.backgroundColor = color
}
const routes = [
  {
    path: '/',
    color: 'yellow'
  },
  {
    path: '/blue',
    color: 'blue'
  },
  {
    path: '/green',
    color: 'green'
  },
  {
    path: '/grey',
    color: '#ccc'
  },
  {
    path: '/red',
    color: 'red'
  },
  {
    path: '/black',
    color: 'black'
  },
]
routes.forEach(function (item) {
  router.route(item.path, function () {
    changeBgColor(item.color)
  })
})
forwardBtn.addEventListener('click', router.forward)
backoffBtn.addEventListener('click', router.backoff)
forwardBtn.disabled = !router.canForward
backoffBtn.disabled = !router.canBackoff

```





