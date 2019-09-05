### 使用JSON.stringify和JSON.parse的方式存在的问题：
#### JS中存在，JSON中不存在的数据类型不能正确克隆
1. 值为undefined的属性会被忽略

2. [0, ,2]和[0, undefined, 2]这样的数组中的undefined会转换为null

3. 函数会被忽略

4. 正则对象会被转换成一个空对象{}

5. 克隆的对象的原型全都指向Object.prototype

6. 对象中存在循环引用时,JSON.stringify()会报错

  ![image-20190619155258220](/Users/ysl/Library/Application Support/typora-user-images/image-20190619155258220.png)

### 更深入的问题
1. 属性的特性要克隆
2. JS为什么不出一个语言层面的原生的克隆啊？

----
```javascript
function classOf(o) {
  //Null, Undefined, Number, String, Boolean, Object, Function, Array, Regexp, Date, Window
  if (o === null) return 'Null'
  if (o === undefined) return 'Undefined'
  return Object.prototype.toString.call(o).slice(8, -1)   // [Object Array]
}
function clone(source) {
  const sources = []
  const targets = []
  function _clone(source) {
    // 基本类型和function都直接返回
    if (typeof source !== 'object' || source === null) return source
    var type = classOf(source)
    var target, proto
    switch (type) {
      case 'Array':
        target = []
        break
      case 'Regexp':
        target = new RegExp(source.source, source.flags)
        if (source.lastIndex) {
          target.lastIndex = source.lastIndex
        }
        break
      case 'Date':
        target = new Date(source.getTime())
        break
      default:
        // 将source的构造函数的prototype复制一份赋值给target
        proto = Object.getPrototypeOf(source)
        target = Object.create(proto)
        break
    }
    // 处理属性的循环引用问题
    var index = sources.indexOf(source)
    if (index > -1) {
      // 如果sources数组中存在此对象，说明之前已经被引用过（有可能是循环引用，也可能不是）
      // 那就返回targets中相对应的对象
      return targets[index]
    }
    sources.push(source)
    targets.push(target)
    var keys = Object.getOwnPropertyNames(source)
    keys.forEach(function (key) {
      // 递归处理每一个属性
      target[key] = _clone(source[key])
    })
    return target
  }
  return _clone(source)
}

function clone(source, stack) {
  // 基本类型和function都直接返回
  if (typeof source !== 'object' || source === null) return source
  var type = classOf(source), target, isObj = false, _stack = stack
  while(_stack !== undefined) {
    if(source === _stack.source) {
      return _stack.target
    } else {
      _stack = _stack.parent
    }
  }
  switch (type) {
    case 'Array':
      target = []
      break
    case 'Regexp':
      target = new RegExp(source.source, source.flags)
      if (source.lastIndex) {
        target.lastIndex = source.lastIndex
      }
      break
    case 'Date':
      target = new Date(source.getTime())
      break
    default:
      isObj = true
      target = Object.create(Object.getPrototypeOf(source))
      break
  }
  var keys = Object.getOwnPropertyNames(source)
  keys.forEach(function (key) {
    // 递归处理每一个属性
    target[key] = clone(source[key], isObj ? {
      source,
      target,
      parent: stack
    } : undefined)
  })
  return target
}


function Super(name) {
  this.name = name
  this.color = ['red']
}
Super.prototype.sayName = function () {
  console.log(this.name)
}

function Sub(name, age) {
  Super.call(this, name)
  this.age = age
}
Sub.prototype = Object.create(Super.prototype)
Sub.prototype.constructor = Sub
var obj = {
  sub: new Sub('Jenny', 10),
  p: [{ name: 'abc', o: { age: 10 } }],
  arr: [1, 2, , 3, undefined, 4]
}
obj.a = obj

var newObj = clone(obj)

console.log(newObj.sub.constructor) // [Function: Sub]
newObj.sub.sayName() // Jenny
console.log(newObj.sub.hasOwnProperty('sayName')) // false
console.log(obj.sub.hasOwnProperty('sayName')) // false
console.log(newObj.p[0].o.age)  // 10
console.log(newObj.a.a.a.a.a.sub.color) // ['red']
console.log(newObj.arr) // 1, 2, <1 empty item>, 3, undefined, 4 ]

```