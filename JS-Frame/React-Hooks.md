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

