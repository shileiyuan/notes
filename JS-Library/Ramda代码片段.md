### 1.  zipWith useWith

```javascript
const R = require('ramda')
const { red, green, blue } = require('chalk');

// 方法一
// zipWith对两个列表对应位置上的元素进行操作
const disco = R.pipe(
  R.zipWith(R.call, [red, green, blue]),
  R.join(' ')
);
console.log(disco(['foo', 'bar', 'xyz']));

// 方法二
// useWith：对输入的参数分别进行转换
const trans = R.apply(R.useWith(
  R.unapply(R.join(' ')),
  [red, green, blue]
))

console.log(trans(['foo', 'bar', 'xyz']))
```



### 2. call repeat

```javascript
const R = require('ramda')
const arr = R.map(R.call, R.repeat(Math.random, 5))
console.log(arr)
```



### 3. pipe

```javascript
// 找出特定年份出版的所有书籍的标题
const publishedInYear = R.curry((year, book) => book.year === year)
const titlesForYear = (year, books) => {
  return R.pipe(
    R.filter(publishedInYear(year)),
    R.map(R.prop('title'))
  )(books)
}
```

