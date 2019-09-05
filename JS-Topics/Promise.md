### 异步串联执行
```javascript
let i = 0
function run() {
  return new Promise(resolve => {
    setTimeout(() => {
      console.log('run', i++)
      resolve()
    }, 1000);
  })
}

const queue = [
  run, run, run
]

// 如果不给reduce一个初始值，就要自己判断了
 queue.reduce((promise, fn) => {
   if(typeof promise === 'function') {
     return promise().then(fn)
   } else {
     return promise.then(fn)
   }
 })

// 给reduce一个初始值会简单很多
queue.reduce((promise, fn) => promise.then(fn), Promise.resolve())

// 与上面的意思一样，但是上面的更加简洁
let p = Promise.resolve()
queue.forEach(fn => {
  p = p.then(fn)
})

// 多个await连着写，本来就是串联的意思
async function test(queue) {
  for(let p of queue) {
    await p()
  }
}
```

[Promises/A+标准](https://promisesaplus.com/)

### 说明
1. promise有一个then方法，而then方法返回一个promise对象，所以promise可以链式调用。
2. 而且，同一个promise的then方法可以被调用多次，它们处于同一层级，所以promise可以“并行链式调用”。
    ```javascript
    var p = new Promise()
    p.then().then()
    p.then().then()
    ```
3.  If the downstream promise doesn't specify a rejection handler, the rejection will be propagated further downstream.
4.  如果then方法中的onReject或onResolve方法返回了一个非promise对象a，then方法还是会返回一个新的promise，且a作为新promise对象的then方法的onResolve方法的参数。
5.  如果then方法中的onReject或onResolve方法返回了一个promise对象a，则then方法会返回这个promise对象a，后面的then方法的onResovle或onReject方法的参数由promise对象a来决定
6.  'catch' is simply sugar for 'then(null, onRejection)' 
7.  Promise.all: 全部完成才会返回
    ```javascript
    var P1 = new Promise(resolve => {
      setTimeout(resolve, 500, 'P1')
    })
    
    var P2 = new Promise(resolve => {
      setTimeout(resolve, 600, 'P2')
    })
    
    Promise.all([P1, P2]).then(results => {
      console.log(results); // ['P1', 'P2']
    })
    ```
8. Promise.race(): 返回执行最快的那个 => 'P1'

### 例子
```javascript
// basic example
var p = new Promise(function (resolve, reject) {
  var timeout = Math.random() * 2;
  console.log('set timeout to:', timeout, 'seconds');
  setTimeout(function () {
    if (timeout < 1) {
      console.log('call resolve');
      resolve('200 OK')
    } else {
      console.log('call reject')
      reject('timeout in ' + timeout + ' seconds')
    }
  }, timeout * 1000)
});

p.then(result => {
  console.log('成功： ' + result)
  return '成功： ' + result;
}, reason => {
  console.log('失败： ' + reason)
  // return '失败： ' + reason; // 如果只是return，会进入下一个then的onResolve方法
  throw new Error('失败： ' + reason)  // 如果throw error就会进入下一个then的onReject方法
}).then(result => {
  console.log(result)
}, reason => {
  console.log(reason.message)
})

// 异步串联执行
function add(i) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log(i++)
      resolve(i)
    }, 1000)
  })
}

add(1).then(add).then(add).then(add)

//ajax的简单promise封装
function ajax(method, url, data) {
  var xhr = new XMLHttpRequest();
  return new Promise(function (resolve, reject) {
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4) {
        if (xhr.status >= 200 && xhr.status < 300 || xhr.status === 304) {
          resovle(xhr.responseText);
        } else {
          reject(xhr.responseText);
        }
      }
    }
    xhr.open(method, url);
    xhr.send(data)
  })
}
```

### es6-promise源码分析
考虑如下代码调用代码：
```javascript
var p = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('data')
  }, 100)
})
p.then(data => `then ${data}`).then(data => {
  console.log(data)
})
```
#### 调用栈

![image-20190619160714526](/Users/ysl/Library/Application Support/typora-user-images/image-20190619160714526.png)



**resolve -> fulfill -> publish -> invokeCallback -> resolve**

promise 对象的数据结构
![image-20190619160744003](/Users/ysl/Library/Application Support/typora-user-images/image-20190619160744003.png)


promise源码执行流程描述：
在promise构造函数中初始化promise对象的属性： _result, _state, _subscribers
```javascript
constructor(resolver) {
  this[PROMISE_ID] = nextId();
  this._result = this._state = undefined;
  this._subscribers = [];

  // 这里为什么要判断noop呢？
  // 因为Promise.resolve里要调用这个构造函数，但是不需要调用下面的initializePromise方法
  // 所以给Promise.resovle传入一个noop参数来判断 
  if (noop !== resolver) {
    typeof resolver !== 'function' && needsResolver();
    this instanceof Promise ? initializePromise(this, resolver) : needsNew();
  }
}
  
function initializePromise(promise, resolver) {
  try {
    resolver(function resolvePromise(value) {
      resolve(promise, value);
    }, function rejectPromise(reason) {
      reject(promise, reason);
    });
  } catch (e) {
    reject(promise, e);
  }
}
```
然后按顺讯执行.then函数，每个.then函数都返回一个新的promise对象，根据parent.\_state的状态决定是直接执行回调函数还是将回调函数注册进parent.\_subscribers数组。如果是在异步代码中resovle的promise，那\_state就是PENDDING，否则就是FULFILLED或者REJECTED，这个例子是PENDING。
```javascript
export default function then(onFulfillment, onRejection) {
  const parent = this;
  const child = new this.constructor(noop);
  // 不懂这里的判断，按我理解child是new出来的promise，child[PROMISE_ID]不可能是undefined
  if (child[PROMISE_ID] === undefined) {
    makePromise(child);
  }
  const { _state } = parent;
  if (_state) {
    // 这里是根据_state的值，来决定是调用onFulfillment还是onRejection（奇技淫巧）
    const callback = arguments[_state - 1];
    // 如果parent的状态已经确定了，就尽快执行callback吧
    asap(() => invokeCallback(_state, child, callback, parent._result));
  } else {
    subscribe(parent, child, onFulfillment, onRejection);
  }
  return child;
}

function subscribe(parent, child, onFulfillment, onRejection) {
  let { _subscribers } = parent;
  let { length } = _subscribers;

  parent._onerror = null;

  _subscribers[length] = child;
  _subscribers[length + FULFILLED] = onFulfillment;
  _subscribers[length + REJECTED] = onRejection;
  
  // 这一段按目前来看也是没用的，因为执行这个函数的时候parent._state一直是0
  if (length === 0 && parent._state) {
    asap(publish, parent);
  }
}
```
之后，等异步操作返回，就会执行resolve
```javascript
function resolve(promise, value) {
  if (promise === value) {
    reject(promise, selfFulfillment());
  } else if (objectOrFunction(value)) {
    handleMaybeThenable(promise, value, getThen(value));
  } else {
    fulfill(promise, value);
  }
}
```
然后执行fulfill，设置promise的\_result和\_state，然后执行asap(publish, promise)
```javascript
function fulfill(promise, value) {
  if (promise._state !== PENDING) { return; }

  promise._result = value;
  promise._state = FULFILLED;

  if (promise._subscribers.length !== 0) {
    asap(publish, promise);
  }
}

// publish读取promise.subscribers，然后调用invokeCallback，执行所有subpromise的回调函数
function publish(promise) {
  let subscribers = promise._subscribers;
  let settled = promise._state;

  if (subscribers.length === 0) { return; }

  let child, callback, detail = promise._result;

  for (let i = 0; i < subscribers.length; i += 3) {
    child = subscribers[i];
    callback = subscribers[i + settled];

    if (child) {
      invokeCallback(settled, child, callback, detail);
    } else {
      callback(detail);
    }
  }
  promise._subscribers.length = 0;
}
```

invokeCallback再根据执行结果决定执行resolve或fulfill等函数，完成一个闭环
```javascript
function invokeCallback(settled, promise, callback, detail) {
  let hasCallback = isFunction(callback),
    value, error, succeeded, failed;
  if (hasCallback) {
    // 执行callback
    value = tryCatch(callback, detail);
    if (value === TRY_CATCH_ERROR) {
      failed = true;
      error = value.error;
      value.error = null;
    } else {
      succeeded = true;
    }
    if (promise === value) {
      reject(promise, cannotReturnOwn());
      return;
    }
  } else {
    value = detail;
    succeeded = true;
  }
  if (promise._state !== PENDING) {
    // noop
    // 这个条件是不可能进来的，执行父promise的then方法的时候，子promise的状态肯定还是PENDING
  } else if (hasCallback && succeeded) {
    // 如果callback执行成功了，就要把这个返回值给子promise并resolve
    resolve(promise, value);
  } else if (failed) {
    reject(promise, error);
  } else if (settled === FULFILLED) {
    fulfill(promise, value);
  } else if (settled === REJECTED) {
    reject(promise, value);
  }
}
```