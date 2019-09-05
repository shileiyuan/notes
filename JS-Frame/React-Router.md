### 我们为什么需要路由

没有路由，我们可以自己用状态控制组件的显隐，也不是不行。
但是路由做了这么几件事：
1. 通过比较Route的path属性和当前的location，来进行路由匹配，如果匹配就会渲染组件，不匹配就返回null
2. 路由变化会反应到浏览器的地址栏中，更加直观
3. 当在一个特定的路由下刷新的时候，由于地址栏不会改变，所以路由的状态可以保持，所以组件的状态也可以保持。

#### Switch 的作用

```javascript
<Switch>
  <Route exact path="/" component={Home}/>
  <Route path="/about" component={About}/>
  <Route path="/:user" component={User}/>
  <Route component={NoMatch}/>
</Switch>
```

如果没有`Switch`，则 `/about` 会匹配 `About`、`User` 和 `NoMatch`。
加上 `Switch` 后，找到第一个匹配项后，就会停止寻找下一个。

### exact的作用
还是上一段代码，如果没有 `exact`，`/about` 也会匹配 `Home`。

**一个没有path属性的 `Route` 组件，可以匹配任何路径。**


### Route 的用法

有三种方法可以用Route来渲染组件
component、 render 和 children
render接受一个inline function，只应该在需要给组件传变量的时候使用。
否则应该使用component

```javascript
const someVariable = true
// 需要传一个extra变量给About的时候，使用render
<Route
  path="/about"
  render={props => <About {...props} extra={someVariable} />}
/>
```

### Navigation
Link NavLink Redirect

### withRouter
withRouter 会将 `match`, `location`, 和 `history` 传给被包裹的组件。

可以使用history手动实现导航：

```javascript
history.push('/posts')
history.replace('/posts')
```

### Redux Blocked Updates
react-router 跟 redux 一起使用时，可能会出现url改变了，组件却不更新的问题。

这个问题出现的条件是：
1. 这个组件是非路由组件，也就是说它不是这样的：`<Route component={Component} />`
2. 并且这个组件被redux的connect包裹住： `connect()(Component)`

理由：
组件是connect的，redux 实现了shouldComponent，当props没有任何改变的时候，redux 就不会更新组件。
因为不是路由组件，所以react-router不会给组件注入location、match等props。
当url改变的时候，组件的props没有任何改变，所以redux不会更新组件。
****
解决方法：
在组件外层再包裹一层withRouter，这样就给组件注入了props，当url改变时，props也会改变，组件就会更新了。
示例：

```javascript
// About是个路由组件，AboutTemp是个非路由组件
export default class About extends Component {
  render() {
    return (
      <div>
        <h3>About</h3>
        <AboutTemp />
      </div>
    );
  }
}

class AboutTemp extends Component {
  render() {
    return (
      <div>
        <ul>
          <li><Link to='/about/Test1'>test1</Link></li>
          <li><Link to='/about/Test2'>test2</Link></li>
        </ul>
        <Route path='/about/Test1' component={Test1} />
        <Route path='/about/Test2' component={Test2} />
      </div>
    );
  }
}
// 不会更新
export default connect(R.prop('first'))(AboutTemp)

// 会更新
export default withRouter(connect(R.prop('first'))(AboutTemp));
```