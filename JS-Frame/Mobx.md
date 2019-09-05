Mobx背后的哲学：任何源自应用状态的东西都应该自动获得。
其中包括UI、数据序列化、服务器通讯等。


### @action
action只影响当前被调用的函数，如果有异步的回调函数，而且这个函数中也要改变store中的数据，那么这个函数也需要被action包起来。
但是如果你用了async/await，就没有办法用action包起来异步函数体了，此时就需要用到runInAction了。
runInAction(f) 只是 action(f)() 的语法糖。

### @observer
observer 函数 / 装饰器可以将React组件转换为可响应MobX的组件。 它将components的render函数使用 MobX.autorun 包裹，以确保任何依赖数据的更新都会触发重渲染。 它是通过独立的 mobx-react 包提供的。
所以，如果在render之外的代码中，把observable的变量赋值给一个其他的变量a，然后在render中使用这个a变量，则render不会捕获变化。

Tip: when observer needs to be combined with other decorators or higher-order-components, make sure that observer is the innermost (first applied) decorator; otherwise it might do nothing at all.

### 用mobx替代React的state
在组件里更新mobx的store，不会调用组件的除componentWillUpdate 和 componentDidUpdate之外的其他生命周期函数，如果你需要这些生命周期函数，就必须使用react自己的state了

**componentWillReact** ：for mobx-react@4+,  the hook will fire when receiving new props and after setState calls
```javascript
import {observer} from "mobx-react"import {observable} from "mobx"

@observer class Timer extends React.Component {
  @observable secondsPassed = 0
  
  componentWillMount() {
    setInterval(() => {
      this.secondsPassed++
    }, 1000)
  }
  
  render() {
    return (<span>Seconds passed: { this.secondsPassed } </span> )
  }
})

React.render(<Timer />, document.body)
```

### 严格模式
```javascript
import {observable, action, useStrict} from 'mobx';
useStrict(process.env.NODE_ENV !== 'production')
```

### What does Mobx react to?
`MobX reacts to any existing observable property that is read during the execution of a tracked function.`

```javascript
let message = observable({
    title: "Foo",
    author: {
      name: "Michel"
    },
    likes: [
      "John", "Sara"
    ]
})

// 1. Incorrect: changing a non-observable reference
// message 是一个指向observable的变量，但是message这个变量自己不是observable的，所以并不会打印
autorun(() => {
  console.log(message.title)
})
message = observable({ title: "Bar" })

// 2. Incorrect: dereference outside a tracked function
// message.title在autorun外面被赋值给了变量title，而title并不是observable的
var title = message.title;
autorun(() => {
  console.log(title)
})
message.title = "Bar"

//3. Incorrect: store a local reference to an observable object without tracking
const author = message.author;
autorun(() => {
  console.log(author.name)
})
// 这一行改变会被捕获到，因为author和message.author是同一个对象
message.author.name = "Sara";
// 这一行不会被捕获到，the message.author relation is not tracked by the autorun
message.author = { name: "John" };

// 4. Common pitfall: console.log

// 5. [MobX 4 and lower] Incorrect: using not yet existing observable object properties
// This will not react. MobX will not react to observable properties that did not exist when tracking // started. If the two statements are swapped, or if any other observable causes the autorun to re-run, // the autorun will start tracking the postDate as well.
autorun(() => {
  console.log(message.postDate)
})
extendObservable(message, {
  postDate: new Date()
})

// 6. Correct: using not yet existing map entries
// This will react. Observable maps support observing entries that may not exist. Note that this will // initially print undefined. You can check for the existence of an entry first by using twitterUrls.has("Sara"). So for dynamically keyed collections, always use observable maps.
const twitterUrls = observable.map({
  "John": "twitter.com/johnny"
})

autorun(() => {
  console.log(twitterUrls.get("Sara"))
})
twitterUrls.set("Sara", "twitter.com/horsejs")


```

### 遇到的坑
1. autorun里面不能调用action，因为容易造成循环引用。此时必须用reaction
  https://github.com/mobxjs/mobx/issues/490





### 使用reaction的最佳实践

在组件的componentDidMount中开始reaction，需要先请求一遍数据，再startReaction，否则一开始不会有数据
```javascript
componentDidMount() {
  const { querySourceList, startQueryReaction } = this.props.transferStore
  querySourceList().then(() => {
    startQueryReaction()
  })
}
```
在store中定义reaction，因为开发的时候热加载，每次修改都会触发一次componentDidMount，如果不做处理，就会多次触发reaction，所以定义的时候，注意判断，当reaction没有被定义过的时候才定义
```javascript
queryReaction = null
startQueryReaction = () => {
  if (this.queryReaction) return
  this.queryReaction = reaction(
    () => R.pick(['sourceIndex', 'sourceText', 'targetList'], this),
    () => {
      console.log('reaction....')
      if (this.visible) {
        this.querySourceList()
      }
    }
  )
}
```

在componentWillUnmount中清理reaction，如果不清理，问题不是很大，只是可能会在组件再一次Mount的时候，发两次请求，之后会正常。但是如果决定要清理，一定要注意，不要仅仅执行queryReaction，一定也要将queryReaction设置为null。否则，等组件再一次加载的时候，queryReaction不是null，就不会定义reaction了

```javascript
componentWillUnmount() {
  const { queryReaction, setProps } = this.props.transferStore
  if (typeof queryReaction === 'function') {
    queryReaction()
    setProps({ queryReaction: null })
  }
}
```

如果在组件的方法中调用了store中的两个方法分别用来修改store中的两个数据，且这两个数据都被reaction监听了，那reaction就会执行两遍。为了避免执行两遍reaction，就必须在store中将这两个方法封装成一个方法，再由组件调用。