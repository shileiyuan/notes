[TOC]



### 1. getTimeList

```javascript
// 自己生成的时刻列表
const getTimeList = (interval = 30) => {
  const parseTime = time => time < 10 ? `0${time}` : `${time}`
  const total = 24 * 60
  const count = Math.ceil(total / interval)
  return [...Array(count)].map((_, i) => {
    const current = interval * i
    const hour = parseTime(Math.floor(current / 60))
    const minute = parseTime(current % 60)
    return `${hour}:${minute}`
  })
}

function getDurationTimes(duration) {
  const start = moment().startOf('day')
  const oneDayMinutes = 60 * 24
  const count = Math.ceil(oneDayMinutes / duration)
  const list = [...Array(count - 1)].map((_, i) => {
    return start.add(duration, 'minute').format('HH:mm')
  })
  return [  
    '00:00',
    ...list
  ]
}
```



### 2. curry

```javascript
// 对任意函数进行柯里化
const curry = (fn, arr = []) => (...args) => {
  const finalArgs = [...arr, ...args]
  return fn.length === finalArgs.length ? fn(...finalArgs) : curry(fn, finalArgs)
}

const curry = (fn, arr = []) => (...args) => {
  return (function(finalArgs) {
    return fn.length === finalArgs.length ? fn(...finalArgs) : curry(fn, finalArgs)
  }([...arr, ...args]))
}

const curry = (fn, arr = []) => (...args) => (finalArgs => fn.length === finalArgs.length ? fn(...finalArgs) : curry(fn, finalArgs))([...arr, ...args])


const curry2 = fn => {
  const finalArgs = []
  const curry = fn => (...args) => {
    finalArgs.push(...args)
    return fn.length === finalArgs.length ? fn(...finalArgs) : curry(fn)
  }
  return curry(fn)
}

function sum(a, b, c, d) {
  return a + b + c + d
}

const currySum = curry(sum)

console.log(currySum(2)(3)(5, 6))
```



### 3. compose

```javascript
// 这种方法代码量比较多，但是符合我的思考过程
// 下面那种方法代码量少，但是比较难理解，也很难想出来，reduceRight执行完，返回的是一个函数，这个函数可以接收任意个参数
const compose = (...fns) => {
  return (...args) => {
    return fns.reduceRight((acc, fn, index) => {
      return index === fns.length - 1 ? fn(...acc) : fn(acc)
    }, args)
  }
}

const compose = (...fns) => fns.reduceRight((acc, fn) => (...args) => fn(acc(...args)))

const compose = (...fns) => fns.reduce((acc, fn) => (...args) => acc(fn(...args)))
```



### 4. throttle 

函数节流：
一个函数要执行，要等待一定的时间间隔，如果在等待的时候又有一个函数要执行，那么上一个函数取消，且下一个函数要等待的时间间隔就是上一个函数剩下的时间

```javascript
function throttle(fn, threshhold, scope) {
  var last = 0; // 上一次fn开始执行的时间
  var timer = null;
  return function() {
    var context = scope || this;
    var now = +new Date();
    var args = arguments;
    var remaining = threshhold - (now - last);
    last = now;
    if(remaining > 0) {
      clearTimeout(timer);
      timer = setTimeout(function() {
        fn.apply(context, args);
      }, remaining);
    } else {
      timer = null;
      fn.apply(context, args);
    }
  }
}
```



### 5. debounce

函数防抖：
一个函数要执行，要等待一定的时间间隔，如果在等待的时候又有一个函数要执行，那么上一个函数取消，且下一个函数重新计时。

```javascript
function debounce(fn, delay, scope) {
  var timer = null;
  return function() {
    var context = scope || this;
    var args = arguments;
    clearTimeout(timer);
    timer = setTimeout(function() {
      fn.apply(context, args);
    }, delay);
  }
}
```



### 6. bind

说明：

1. 在严格模式下，给call和apply传什么参数，this的取值就是什么.

2. 在非严格模式下，基本数据类型会被转换成对象，null和undefined会被替换成全局对象。

3. 如果给一个函数连续bind两次，经过自己分析得出，this取的是第一次bind的参数。

```javascript
// 简单版
Function.prototype.bind = Function.prototype.bind || function(context) {
  if(typeof this !== 'function') {
    throw new TypeError('what is trying to be bound is not callable');
  }
  var that = this;
  var args = Array.prototype.slice.call(arguments, 1);
  return function(){
    var innerArgs = Array.prototype.slice.call(arguments);
    var finalArgs = args.concat(innerArgs);
    return that.apply(context, finalArgs);
  };
}

// 完整版
//一个绑定函数也能使用new操作符创建对象：这种行为就像把原函数当成构建器。提供的context值被忽略，同时调用时的参数被提供给模拟函数。

Function.prototype.bind = Function.prototype.bind || function(context) {
  // this指的是调用bind的函数
  if (typeof this !== 'function') {
    throw new TypeError('what is trying to be bound is not callable');
  }
  var that = this;
  var args = Array.prototype.slice.call(arguments, 1);
  var bound = function() {
    var innerArgs = Array.prototype.slice.call(arguments);
    var finalArgs = args.concat(innerArgs);
    // 如果是当作构造函数来用的话，context就被忽略
    // 然后也要注意，构造函数本来是不需要return的
    // 但是，构造函数如果没有返回值或者返回的不是对象，new 一个构造函数的时候，就会返回this的值
    // 所以，此处还是需要返回构造函数返回的值的
    var finalContext = this instanceof bound ? this : context;
    return that.apply(finalContext, finalArgs);
  }
  var F = function() {}
  F.prototype = this.prototype
  bound.prototype = new F()
  return bound
}


```



### 7. classOf

获取对象的类型

```javascript
 //Null, Undefined, Number, String, Boolean, Object, Function, Array, Regexp, Date, Window    (NaN属于Number类型)
 function classOf(o) {
  if (o === null) return 'Null';
  if (o === undefined) return 'Undefined';
  return Object.prototype.toString.call(o).slice(8, -1);   // [Object Array]
}
```



### 8. after

```javascript
// after: 接受两个参数， 一个次数n，一个回调函数cb，返回一个函数f，当f执行了n次之后，就会执行回调函数cb
// 这是js-agent里的一段代码，根据一个url的列表请求数据，然后组装数据，因为不能用promise，所以只能用after函数
function assembleList(list) {
  var urls = [];
  list.forEach(function (item) {
    if (urls.indexOf(item.url) === -1) {
      urls.push(item.url)
    }
  });
  var len = urls.length;
  var after = function (times, func) {
    return function () {
      if (--times < 1) {
        return func.apply(this, arguments);
      }
    };
  }
  // func执行len次后，就会执行给after传入的回调函数
  var func = after(len, function () {
    list.forEach(function (item) {
      assembleData(item)
    })
  })
  urls.forEach(function (url) {
    loadSource(url, function (source) {
      sourceCache[url] = source;
      func();
    })
  })
}
```



### 9.  shuffle

```javascript
// 打乱数组的顺序
function shuffle(arr) {
  var length = arr.length;
  var shuffled = Array(length);
  for (var index = 0, rand; index < length; index++) {
    rand = Math.floor(Math.random() * (index + 1))
    if (rand !== index) shuffled[index] = shuffled[rand];
    shuffled[rand] = arr[index];
  }
  return shuffled;
};

// 原理就是交换随机数和index的值
var shuffle2 = function(arr) {
  for(var i = 0, len = arr.length; i < len; i++) {
    var rand = Math.floor(Math.random() * (i + 1))
    if(rand !== i) {
      var t = arr[i]
      arr[i] = arr[rand]
      arr[rand] = t
    }
  }
  console.log(arr)
}
```



### 10.  getRandomColor

```javascript
// 随机生成6位16进制颜色
var getRandomColor = function(){
  // return '#'+('00000'+(Math.random()*0x1000000<<0).toString(16)).slice(-6);
  return (Math.random() * 0x1000000 << 0).toString(16).padStart(7, '#000000')
};

// 给元素添加轮廓
[].forEach.call($$('*'), function (a) {
  a.style.outline = '1px solid ' + getRandomColor()
});
```



### 11. chunk

原理： 将要处理的任务分块，并放入一个队列中，使用定时器取出下一个要处理的项目进行处理，接着再设置另一个定时器。

作用：将多个项目的处理在执行队列上分开，在每个项目处理之后，给予其他任务运行的机会，避免长时间运行脚本错误。

```javascript
// 数组分块技术
function chunk(array, process, context) {
  setTimeout(function time() {
    var item = array.shift();
    process.call(context, item);
    if(array.length > 0) {
      setTimeout(time, 100)
    }
  })
}
```



### 12. 单例模式

```javascript
// 普通单例模式
var singleton = (function(){
  // 私有成员
  var attr1 = false
  var method1 = function() {
    
  }
  return {
    // 公有成员
  }
}());

// lazy loading singleton
var singleton = (function() {
  var instance = null
  // 这个函数里写业务代码
  function constructor() {
    // 私有成员
    var attr1 = false
    var method1 = function() {
      
    }
    return {
      // 公有成员
    }
  }
  return {
    getInstance: function() {
      if(!instance) {
        instance = constructor()
      }
      return instance
    }
  }
})
```



### 13. 作用域安全的构造函数

```javascript
function Person(name, age, job) {
  if(this instanceof Person) {
    this.name = name;
    this.age = age;
    this.job = job;
  } else {
    return new Person(name, age, job);
  }
}
```



### 14. setTimeout polyfill

```javascript
// 有的浏览器setTimeout不能传入额外的参数，针对这个可以写一个polyfill
// 可以借鉴这里的特性检测
setTimeout(function (arg1) {
  if (arg1 === 'test') {
    // feature test is passed, no need for polyfill
    return;
  }
  var __nativeST__ = window.setTimeout;
  window.setTimeout = function (vCallback, nDelay /*, argumentToPass1, argumentToPass2, etc. */) {
    var aArgs = Array.prototype.slice.call(arguments, 2);
    return __nativeST__(vCallback instanceof Function ? function () {
      vCallback.apply(null, aArgs);
    } : vCallback, nDelay);
  };
}, 0, 'test');
```



### 15. 模拟常量

```javascript
// 只能读取，不能修改
var Class = (function () {
  var constants = {
    UPPER_ROUND: 100,
    LOWER_ROUND: -100
  };
  
  // 这个函数作为Class的构造函数
  var GouZao = function () { }
  
  //对私有变量只提供accessor, 不提供mutator 
  GouZao.getConstant = function (name) {
    return constants[name];
  }
  
}());
Class.getConstant('UPPER_ROUND')
```



### 16. Sigil.js

```javascript
(function (factory) {
  // Establish the root object, 'window' in the browser, or 'exports' in the server.
  var root = this;
  if (typeof exports !== 'undefined') {
    factory(exports);
  } else {
    root.Sigil = factory({});
  }
}(function (Sigil) {
  Sigil.version = '1.0.1';
  return Sigil;
}));
```



### 17. ES5 Class

```javascript
var Book = (function () {
  // private static attributes
  var numOfBooks = 0;
  
  // private static method
  function checkIsbn(isbn) { }
  
  // return the constructor
  return function (newIsbn, newTitle) {
    // private attributes
    var isbn, title;
    
    // privileged methods
    this.getIsbn = function () { 
      return isbn; 
    } 
    this.setIsbn = function (newIsbn) {
      if (!checkIsbn(newIsbn)) { throw new Error('.....') };
      isbn = newIsbn;
    };
    this.getTitle = function () { return title; };
    this.setTitle = function (newTitle) { title = newTitle; }

    //constructor code
    numOfBooks++; // 跟踪实例化的书的总数
    if (numOfBooks > 50) {
      throw new Error('Book: only 50 instances of Book can be ceated');
    }
    this.setIsbn(newIsbn);
    this.setTitle(newTitle);
  }
}());

//public static method
Book.toTitleCase = function () { };

//public none-privileged methods
Book.prototype = {
  display: function () { }
}
```





























































