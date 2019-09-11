## 例子

```react
import React, { useState, useEffect } from 'react'
import { Button } from '@uyun/components'

function Hooks() {
  const [count, setCount] = useState(0)
  useEffect(() => {
    document.title = `You clicked ${count} times`
  })
  return (
    <div>
      <p>You clicked {count} times</p>
      <Button onClick={() => setCount(count + 1)}>
        Click me
      </Button>
    </div>
  )
}

export default Hooks

```



effect 会在每次render之后调用。

可以手动告诉React，当某个属性没有变化的时候，render的时候就不用执行这个efffect了。

```react
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // Only re-run the effect if count changes
```



## Hooks 规则

**只在React Function的最顶层使用Hooks**

不要在循环、条件或嵌套函数中调用Hooks

因为React依赖hooks被调用的顺序，来确定哪个state对应哪个useState。

如果你想根据不同的条件来决定是否执行一个effect，你可以把这个条件写到effect里面。

自己的理解： 初始化的时候，react定义了一些列的useState和useEffect，当rerender的时候，react怎么知道哪个state对应的是哪个useState呢？答案是，react依赖定义的时候的相对顺序，react把这些放入了一个队列里面，rerender的时候，根据队列顺序取数据即可。



**只在React Functions中调用Hooks**



### Demo2

```react
import React, { useState, useEffect } from 'react'

export default function HookDemo() {
  const [count, setCount] = useState(0)
  useEffect(() => {
    const timer = setInterval(() => {
      // 此处不依赖外部的count变量
      setCount(c => c + 1)
    }, 1000)
    return () => {
      clearInterval(timer)
    };
    // 这里是个空数组
  }, [])
  return (
    <h3>{count}</h3>
  )
}
```



### Context

```react
const MyContext = React.createContext(defaultValue);

<MyContext.Provider value={/* some value */}>
</MyContext.Provider>
  
// 当Provider的value属性改变的时候，所有是Provider后代的Consumer都会重新渲染

// class组件使用context
class MyClass extends React.Component {
  // 也可以通过静态类属性来声明contextType
  // static contextType = MyContext
  componentDidMount() {
    let value = this.context;
    /* perform a side-effect at mount using the value of MyContext */
  }
  componentDidUpdate() {
    let value = this.context;
    /* ... */
  }
  componentWillUnmount() {
    let value = this.context;
    /* ... */
  }
  render() {
    let value = this.context;
    /* render something based on the value of MyContext */
  }
}
// 声明静态类属性，来使用this.context
MyClass.contextType = MyContext;



// function组件使用context
<MyContext.Consumer>
  {value => /* render something based on the context value */}
</MyContext.Consumer>
```



### useReducer

```react
const initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
    </>
  );
}
```



### useRef

`useRef()` 和 `{current: ...}` 唯一的区别就是，`useRef()` 在每次render时会返回同一个对象

```react
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` points to the mounted text input element
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}

```



