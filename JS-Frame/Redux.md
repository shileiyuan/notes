### 操作数组的方法

```javascript
function insertItem(array, action) {
  return [
    ...array.slice(0, action.index),
    action.item,
    ...array.slice(action.index)
  ]
}

function removeItem(array, action) {
  return [
    ...array.slice(0, action.index),
    ...array.slice(action.index + 1)
  ];
}
```


### compose pipe

```javascript
function compose(...funcs) {
  if (funcs.length === 0) {
    return arg => arg
  }
  if (funcs.length === 1) {
    return funcs[0]
  }
  return funcs.reduce((first, next) => (...args) => first(next(...args)))
}

function pipe(...funcs) {
  if(funcs.length === 0) {
    throw new Error('pipe requires at least one argument')
  }
  return funcs.reduceRight((last, prev) => (...args) => last(prev(...args)))
}


```



