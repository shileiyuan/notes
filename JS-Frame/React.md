[You Probably Don't Need Derived State](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-uncontrolled-component-with-a-key)

### defaultProps

```java

class Greeting extends React.Component {
  render() { return ( <h1>Hello, {this.props.name}</h1> ); }
}
// Specifies the default values for props:
Greeting.defaultProps = { name: 'Stranger'};

```

-----

### 生命周期函数
**shouldComponent:**
Called to determine whether the change in props and state should trigger a re-render.
Component always returns true.
PureComponent implements a shallow comparison on props and state and returns true if any props or state have changed.
If false is returned,
`Component#render`, `componentWillUpdate` and `componentDidUpdate` will not be called.

**componentDidMount:**
Called immediately after a component is mounted. Setting state here will trigger re-rendering.

**componentDidUpdate:**
Called immediately after updating occurs. Not called for the initial render.

**componentWillMount:**
Called immediately before mounting occurs, and before `Component#render`. Avoid introducing any side-effects or subscriptions in this method.

**componentWillRecevieProps:**
Called when the component may be receiving new props. React may call this even if props have not changed, so be sure to compare new and existing props if you may only want to handle changes.
Calling `Component#setState` generally does not trigger this method.

**componentWillUnmount:**
Called immediately before a component is destroyed. Perform any necessary cleanup in this method, such as cancelled network requests, or cleaning up any DOM elements created in ComponentDidMount.

**componentWillUpdate**:
Called immediately before rendering when new props or state is received. Not called for the initial render.
Note: You cannot call `Component#setState` here.

---

### PureComponent

PureComponent实现了shouldComponentUpdate，会浅比较state和props，如果它们没有变化，就不会更新组件。

**不要修改state或props中的objects和array。** 因为如果一个父组件修改了这些，并将它传给一个Pure类型的子组件，则这个子组件通过比较后，探测不到这个变化，就不会更新。相反，应该总是返回一个新的数组和对象。

**不要在render中给Pure组件使用箭头函数。** 因为父组件每次render都会生成一个新的函数，然后它的Pure类型的子组件就会触发不必要的更新。

**不要在父组件的render中计算数据并把它传给Pure类型的子组件。** 因为父组件每次render都会重新计算这个数据，就算这个数据里面的值没有改变，但是数据的引用变了，还是会导致Pure组件重新render

什么时候使用PureComponent？
当父组件更新了，子组件的state和props即使没变，也要跟着更新的时候。子组件就需要用PureComponent了。

```html
// 不要这样做
<CommentItem likeComment={() => this.likeComment(user.id)} />

// 可以这样做
<CommentItem likeComment={this.likeComment} userID={user.id} />
```


### React diff 算法

react的Virtual DOM对真实dom做了一层抽象，当每次更新虚拟dom后，react会帮我们计算出dom中真正改变的部分，并只针对该部分进行实际的dom操作，从而提高了页面的渲染效率。

#### diff算法基于三个假设：

1. web程序中，DOM节点跨层级的操作足够少，所以React只会对比处于同一层级的节点。
2. 类型相同的组件产生相似的DOM结构，类型不同的组件产生不同的DOM结构。
3. 对于同一层级的一组节点，它们可以通过唯一的key进行区分。

#### 对于不同层级的节点

比如出现了跨层级的移动操作，因为假设1，react不会对比这些节点，而是直接删除这个节点及其子节点，并在新的位置重建所有节点。

#### 对于同一层级的节点对比来说

**如果将要更新的组件类型不同于更新前的组件类型** ，因为假设2，react会直接接删除该节点及其所有子节点，然后在同一个位置创建并插入新的节点。

**如果将要更新的组件类型与更新前的组件类型相同**，但是属性不同，就只更新属性，组件不用重新创建，可以复用。

另外，对于同一层级的一组列表组件来说，必须给每个组件提供一个唯一的`key`属性。当这组组件只是位置变了的时候，如果没有key，就必须要一个一个对比，然后进行删除和创建节点的操作。有了key之后，就可以对比新老集合，一部分创建和删除操作可以简化为移动操作。

#### 给实际开发带来的启示：

1. 可以通过CSS隐藏或显示某些节点，而不是真的移除或添加DOM节点。



### 为什么setState是异步的

setState之后需要做两件事：
1. 更新state
2. 重新渲染组件（把虚拟DOM的更改同步到真实DOM）

最简单的做法就是：这两件事全部同步去做，这样的话，每次都会重新渲染组件，效率低下。
那如果只同步更新state，但是异步渲染组件呢？也不行。
因为props只有在重新渲染父组件的时候才能确定。也就是说，state是同步的，但是props却是异步的。
这会导致必须放弃batching（对性能有很大影响），而且，如果一个state是由state和props共同组成的，这个state也不能得到正确结果。



### Hooks

```javascript
import React, { useState, useEffect } from 'react';

export default function App() {
  const [count, setCount] = useState(0)
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  )
}
```





