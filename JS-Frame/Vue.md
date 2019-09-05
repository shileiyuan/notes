## 生命周期
每个Vue实例在被创建时都要经过一系列的初始化过程，例如，需要设置数据监听、编译模版、将实例挂载在DOM，并在数据变化时更新DOM。

```javascript
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
})
// => "a is: 1"
```
beforeCreate  created beforeMount mounted beforeUpdate updated beforeDestroy destroyed

---
## 模版语法


### `v-bind` 缩写

```javascript
<!-- 完整语法 -->
<a v-bind:href="url">...</a>

<!-- 缩写 -->
<a :href="url">...</a>
```

### `v-on` 缩写

```javascript
<!-- 完整语法 -->
<a v-on:click="doSomething">...</a>

<!-- 缩写 -->
<a @click="doSomething">...</a>

```

### v-html
双大括号会将数据解释为普通文本，而非 HTML 代码。为了输出真正的 HTML，你需要使用 v-html 指令
相当于react的 `dangerouslySetInnerHTML`.

---

## 数据与方法

### 数据属性 data
当一个 `Vue` 实例被创建时，它将 `data` 对象中的所有的属性加入到 Vue 的响应式系统中。当这些属性的值发生改变时，视图将会产生“响应”，即匹配更新为新的值。

### 实例属性与方法
它们都有前缀 $，以便与用户定义的属性区分开来
```javascript
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // => true
vm.$el === document.getElementById('example') // => true

// $watch 是一个实例方法
vm.$watch('a', function (newValue, oldValue) {
  // 这个回调将在 `vm.a` 改变后调用
})
```
---

## 计算属性

类似于mobx的@computed

```javascript
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
})
```

### 计算属性的setter

计算属性默认只有 getter ，不过在需要时你也可以提供一个 setter 

```javascript
// ...
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```

### 侦听器

当需要在数据变化时执行异步或开销较大的操作时，使用侦听器比使用计算属性更加合适。

```html
<div id="watch-example">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
```

```javascript
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    // 如果 `question` 发生改变，这个函数就会运行
    question: function (newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    }
  },
  created: function () {
    // `_.debounce` 是一个通过 Lodash 限制操作频率的函数。
    // 在这个例子中，我们希望限制访问 yesno.wtf/api 的频率
    // AJAX 请求直到用户输入完毕才会发出。想要了解更多关于
    // `_.debounce` 函数 (及其近亲 `_.throttle`) 的知识，
    // 请参考：https://lodash.com/docs#debounce
    this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
  },
  methods: {
    getAnswer: function () {
      if (this.question.indexOf('?') === -1) {
        this.answer = 'Questions usually contain a question mark. ;-)'
        return
      }
      this.answer = 'Thinking...'
      var vm = this
      axios.get('https://yesno.wtf/api')
        .then(function (response) {
          vm.answer = _.capitalize(response.data.answer)
        })
        .catch(function (error) {
          vm.answer = 'Error! Could not reach the API. ' + error
        })
    }
  }
})
```


## Class与Style绑定

### 对象语法

```javascript
<div v-bind:class="{ active: isActive }"></div>

// v-bind:class 指令也可以与普通的 class 属性共存
<div class="static" v-bind:class="{ active: isActive, 'text-danger': hasError }"></div>

//绑定的数据对象可以不必内联定义在模板里
<div v-bind:class="classObject"></div>

data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

### 数组语法
```javascript
<div v-bind:class="[activeClass, errorClass]"></div>

data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}

// 渲染为
<div class="active text-danger"></div>
```

### 用在组件上

当在一个自定义组件上使用 class 属性时，这些类将被添加到该组件的根元素上面。这个元素上已经存在的类不会被覆盖。

### 绑定内联样式

```javascript
// 可以直接绑定一个对象
<div v-bind:style="styleObject"></div>

data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}

// v-bind:style 的数组语法可以将多个样式对象应用到同一个元素上
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

## 条件渲染

###  v-if v-else-if v-else

```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```


### 用key管理可复用的元素

Vue默认会尽可能地复用已有元素而不是从头开始渲染。
这有时会导致一些问题，比如在切换状态时，input标签之前的输入值不会被清空。
为了避免这种可能的问题，可以在该标签上手动添加一个唯一的**key**属性即可。


### v-show
带有v-show的元素始终会被选在并保留在DOM中。v-show只是简单地切换元素的CSS属性的display。

```html
<h1 v-show="ok">Hello!</h1>
```



## 列表渲染

### 遍历数组

```html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>

```
```javascript
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

`in 可以替换为 of, 两者等价`

### 遍历对象

v-for可以用来遍历一个对象的属性

```javascript
<div v-for="(value, name, index) in object">
  {{ index }}. {{ name }}: {{ value }}
</div>


new Vue({
  el: '#v-for-object',
  data: {
    object: {
      title: 'How to do lists in Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
})
```

### 维护状态

key的问题

```html
<div v-for="item in items" v-bind:key="item.id">
  <!-- 内容 -->
</div>
```

## 数组更新检测

### 变异方法（mutation method）会改变原数组的方法
Vue 将被侦听的数组的变异方法进行了包裹，所以它们也将会触发视图更新。这些被包裹过的方法包括： 
`push pop shift unshift splice sort reverse`

### 替换数组 返回一个新数组的方法
如 `filter concat slice` 等

### 注意事项
由于JavaScript的限制，Vue不能检测以下数组的变动：

1. 当你利用索引直接设置一个数组项时，例如：`vm.items[indexOfItem] = newValue`
2. 当你修改数组的长度时，例如：`vm.items.length = newLength`

为了解决第一类问题：可以使用下面两种方法：

```javascript
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)

// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
```

为了解决第二类问题，可以使用splice：
```javascript
vm.items.splice(newLength)
```



### 对象变更注意事项
还是由于JavaScript的限制，Vue不能检测对象属性的添加或删除

可以这样做
```javascript
Vue.set(vm.userProfile, 'age', 27)
```