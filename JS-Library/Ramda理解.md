### 	操作数组的常用方法

**without:** 在第二个数组中存在，在第一个数组中不存在的值
```javascript
R.without([1, 2], [1, 2, 1, 3, 4]); //=> [3, 4]
```
**difference:** 在第一个数组中存在，在第二个数组中不存在的值
```javascript
R.difference([1,2,3,4], [7,6,5,4,3]); //=> [1,2]
```
**pluck:** 属性名称只能传一个, **project:** 可以传一个属性名组成的数组
```javascript
const arr = [
  {a: 1, b: 3, c: 5}, 
  {a: 2, b: 4, d: 6}
]
R.pluck('a')(arr); //=> [1, 2]
// 等价于
R.map(R.prop('a'))(arr)

R.project(['a', 'b'])(arr)
//等价于
R.map(R.pick(['a', 'b']))(arr)
```
**intersection:** 取两个列表的交集
```javascript
R.intersection([1,2,3,4], [7,6,5,4,3]); //=> [4, 3]
```
**union:** 取并集
```javascript
R.union([1, 2, 3], [2, 3, 4]); //=> [1, 2, 3, 4]
```
**zipWith:** 从两个数组中分别取值，返回操作后的值组成的数组
类似的还有zip和zipObj
```javascript
var f = (x, y) => {
  // ...
};
R.zipWith(f, [1, 2, 3], ['a', 'b', 'c']);
//=> [f(1, 'a'), f(2, 'b'), f(3, 'c')]

R.zip([1, 2, 3], ['a', 'b', 'c']); 
//=> [[1, 'a'], [2, 'b'], [3, 'c']]

R.zipObj(['a', 'b', 'c'], [1, 2, 3]); 
//=> {a: 1, b: 2, c: 3}
```

**fromPairs toPairs**
```javascript
// 将二维数组转换成对象
R.fromPairs([['a', 1], ['b', 2], ['c', 3]]); //=> {a: 1, b: 2, c: 3}

// 将对象转换成二维数组
R.toPairs({a: 1, b: 2, c: 3}); //=> [['a', 1], ['b', 2], ['c', 3]]

// 数组也可以toPairs
R.toPairs(['a', 'b']) //=> [['0', 'a'], ['1', 'b']]

// 将键转换成大写
// 先将对象转换成二维数组，然后再修改数组，最后再将二维数组转换成对象

const data = { a: 1, b: 2, c: 3 }
const result = R.pipe(
  R.toPairs,
  // adjust: 将数组中指定索引处的值替换为经函数变换的值
  R.map(R.adjust(R.toUpper, 0)),
  R.fromPairs
)(data)
console.log(result) // { A: 1, B: 2, C: 3 }

//使用reduce
const upperData1 = Object.entries(data).reduce((acc, [key, value]) => {
  acc[key.toUpperCase()] = value
  return acc
}, {})


const upperData2 = R.reduce(
  (acc, [key, value]) => R.assoc(R.toUpper(key), value, acc),
  {},
  Object.entries(data)
)

// filter an object using keys as well as values
const filterWithKeys = (pred, obj) => R.pipe(
  R.toPairs,
  R.filter(R.apply(pred)),
  R.fromPairs
)(obj)
const result = filterWithKeys(
  (key, value) => key.length === value,
  { red: 3, blue: 5, green: 5, yellow: 2 }
)
console.log(result) //=> {red: 3, green: 5}



```
**groupBy:** 给数组按一定的规则分组，并组合成一个对象
```javascript
const R = require('ramda')

function f(student) {
  var score = student.score;
  return score < 65 ? 'F' :
    score < 70 ? 'D' :
      score < 80 ? 'C' :
        score < 90 ? 'B' : 'A';
}

var students = [
  { name: 'Abby', score: 84 },
  { name: 'Eddy', score: 58 },
  { name: 'Jack', score: 69 }
];
var byGrade = R.groupBy(f);
const result = byGrade(students);
console.log(result)


const result2 = students.reduce((acc, student) => {
  const grade = f(student)
  !Array.isArray(acc[grade]) && (acc[grade] = [])
  acc[grade].push(student)
  return acc
}, {})

console.log(result2)
```

**splitAt lift zipObj:** 改变数据格式
```javascript
const tsv = [
  ['name', 'age', 'drink'],
  ['john', 23, 'wine'],
  ['maggie', 45, 'water']
];
let result = R.pipe(
  R.splitAt(1),
  R.apply(R.lift(R.zipObj))
)(tsv)

console.log(result)
// [
//  { name: 'john', age: 23, drink: 'wine' },
//  { name: 'maggie', age: 45, drink: 'water' }
// ]

// 有时候，可以分成多步来做，不用非得一步到位，因为有时候没办法一步到位
const [[keys], values] = R.splitAt(1, tsv)
const result = R.map(R.zipObj(keys), values)

```

#### 根据数组的id值，改变他的disabled值
```javascript
const newLayout = R.map(R.when(
  R.propEq('id', id),
  R.evolve({ disabled: R.not })
))(layout)

// 根据id，将其对应的disabled值取反
const disabled = R.pipe(
  R.find(R.propEq('id', id)),
  R.prop('disabled'),
  R.not
)(layout)
```

---
### 操作对象的常用方法
**prop, props, pick pickBy**
```javascript
// prop: 传入一个字符串和一个对象，返回值是一个对象

// props：传入一个数组和一个对象，返回值是一个对象的值组成的数组
var fullName = R.pipe(R.props(['first', 'last']), R.join(','))
console.log(fullName({last: '三', age: 33, first: '张'})); // 张,三

// pick：传入一个数组和一个对象，返回值是一个对象
R.pick(['first', 'last'])({last: '三', age: 33, first: '张'})

// pickBy: 传入一个函数和一个对象，返回满足条件的对象
var isUpperCase = (val, key) => key.toUpperCase() === key;
R.pickBy(isUpperCase, {a: 1, b: 2, A: 3, B: 4}); //=> {A: 3, B: 4}
```

**forEachObjIndexed, map:** 遍历对象
```javascript
var cssQuery = R.invoker(1, 'querySelectorAll');
var setStyle = R.curry(
  (style, tag) => R.forEachObjIndexed((value, key) => tag.style[key] = value, style)
);

R.pipe(
  cssQuery('a, p'),
  R.forEach(setStyle({ color: 'green', border: '1px solid red' })),
  // R.forEach(tag => {
  //   Object.assign(tag.style, {color: 'red', border: '1px solid red'})
  // }),
)(document)
```

---
### 常用的工具方法
**apply, unapply**
```javascript
var nums = [1, 2, 3, -99, 42, 6, 7];
R.apply(Math.max, nums); //=> 42

var list = R.unapply(R.identity);
list(1, 2, 3); // => [1, 2, 3]
```

**invoker**: 传入一个参数数量，和一个函数的名字，返回一个函数
```javascript
var sliceFrom = R.invoker(1, 'slice');
sliceFrom(6, 'abcdefghijklm'); //=> 'ghijklm'
var sliceFrom6 = R.invoker(2, 'slice')(6);
sliceFrom6(8, 'abcdefghijklm'); //=> 'gh'
```

**chain**
参数是n个函数和一个待操作的数据
函数从右向左执行，待操作的数据和右边函数的返回值会一起当作参数传递给左边的函数，然后依次执行。
```javascript
// 将数组整理成以id作为key的对象
const list = [{ id: 'foo', name: 'John' }, { id: 'bar', name: 'Jane' }]

// 原生的reduce
list.reduce((acc, item) => {
  acc[item.id] = item
  return acc
}, {})

// 直接使用zipObj   R.pluck('id') 等价于 R.map(R.prop('id'))
R.zipObj(R.pluck('id', list), list)

// 使用了chain的zipObj
R.chain(R.zipObj, R.pluck('id'), list)

// 如果chain的参数只有一个函数和一个带操作的数据，就会遍历数据
// chain maps a function over a list and concatenates the results.
// chain is also known as flatMap in some libraries
var duplicate = n => [n, n];
R.chain(duplicate, [1, 2, 3]); //=> [1, 1, 2, 2, 3, 3]

// 有两个或两个以上的参数就会是开头的说明
R.chain(R.append, R.head)([1, 2, 3]); //=> [1, 2, 3, 1]
```

### snippets
```javascript
const obj = {
  a: 1,
  b: { c: 3 },
  d: {
    e: { f: 6 },
    g: [
      { h: 8, i: 9 },
      0
    ]
  }
}
const flattenObj = obj => {
  const go = obj_ => R.chain(([k, v]) => {
    if (R.contains(R.type(v), ['Array', 'Object'])) {
      return R.map(([k_, v_]) => [`${k}.${k_}`, v_], go(v))
    } else {
      return [[k, v]]
    }
  }, R.toPairs(obj_))

  return R.fromPairs(go(obj))
}

const result = flattenObj(obj)
// {"a": 1, "b.c": 3, "d.e.f": 6, "d.g.0.h": 8, "d.g.0.i": 9, "d.g.1": 0}

```

### ramda与lodash的区别
1. ramda所有函数全部自动柯里化；
2. 数据放在函数的最后一个参数；
3. 派生出的一个区别就是：柯里化导致函数参数的个数很重要，不能随意增减。所以lodash中一些根据参数个数完成类似工作的函数，在ramda中将会由不同的函数完成。

### 难点在哪里
难点在我们处理数据的时候，需要知道待处理的和期待得到的数据的结构，但是ramda把数据的结构隐藏了，就使得这种计算过程不容易被看懂。

### 缺陷
一旦报错，报错信息参考价值不大，给排错造成困难


### 对象的判断
R.where


## debug
```javascript

const trace = message => R.tap(data => console.log(message, data))

const trace2 = R.curry((tag, x) => {
  console.log(tag, x)
  return x
})

var dasherize = R.compose(
  R.join('-'),
  R.map(R.toLower),
  // 打印出来split之后的数据
  // R.tap(console.log),
  trace('after split'),
  R.split(' '),
  R.replace(/\s{2, }/ig, ' ')
)
const result = dasherize('The world is a vampire')
```

### 声明式代码与命令式代码
声明式代码指出我们明确希望得到的结果
命令式代码需要写一步一步的指示


### 类型签名
类型签名在函数式编程中扮演着非常重要的角色，不但可以用于编译时检测，还是最好的文档