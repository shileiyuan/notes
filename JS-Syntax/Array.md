### 稀疏数组
稀疏数组指 `[0, , 2, 3]`这样的数组，而不是`[0, undefined, 2, 3]`这样的。

Array(3) -> 生成的是稀疏数组 `[ , , ]`

### 会跳过空位（empty slots）的方法
map forEach every some filter reduce

### ES6明确会将空位处理成 undefined
entries keys values find findIndex 都会将空位处理成undefined

ES5的join和toString会将空位视为undefined，而undefined和null会被处理成空字符串

Array.from 和 扩展运算符(...) 会将空位转换为undefined
```javascript
[...['a',,'b']] // -> // [ "a", undefined, "b" ]
```
fill会将空位视为正常的数组位置
```javascript
new Array(3).fill('a') // ["a","a","a"]
```

for...of循环也会遍历空位
```javascript
let arr = [, ,];
for (let i of arr) {
  console.log(1);  // 打印两个1
}
```


### map详解
1. map的callback不会在未赋值的项上被调用。
2. 如果调用map的数组是稀疏数组，则返回的数组也是稀疏数组。
    ```javascript
    var a = [1, 2, , 4]
    a.map(x => x + 1) // [2, 3, , 5]
    ```
    **map执行详解**
    在map的callback被第一次调用之前，map能访问的元素的范围就被设置好了，但是在map执行期间，依然可以对调用map的数组a进行增删改。
    也就是说，在map执行前，数组a的长度是多少，在map执行后，返回的新数组的长度就是多少。
    map是按照数组的位置进行遍历的。

如果在map的callback执行后，给a增加了一个元素，这个元素将不会被callback访问到。
如果在map执行期间，数组a的某一项被改变了，则传入callback的值是map访问该值时候的那个值。也就是说，在map执行期间对数组a进行的修改，会影响到后面要执行的callback
如果在map执行期间，删除了数组a的一些元素，那么这个删除操作，会影响接下来要执行的callback




### 不改变原数组：

**map、filter、reduce、reduceRight、concat、join、slice、flat、flatMap** 共9个

1. `var new_array = old_array.concat([value1[, value2[, ...[, valueN]]]])`
2. `var newArray = arr.filter(callback(element[, index[, array]])[, thisArg])`
3. `var newArray = arr.flat([depth])` 
4. `var new_array = arr.flatMap(function callback(currentValue[, index[, array]]) {
    // return element for new_array
    }[, thisArg])`
5. `arr.join([separator])` // 如果数组是空的，就会返回一个空字符串，separator默认是逗号。如果碰到了空位、undefined或null，会被处理成空字符串
6. map
7. `arr.reduce(callback(accumulator, currentValue[, index[, array]]), [, initialValue])`
8. `arr.reduceRight(callback(accumulator, currentValue[, index[, array]])[, initialValue])`
9. `arr.slice([begin[, end]])`

### 改变原数组
**push、pop、shift、unshift、splice、sort、reverse、fill、copyWithin** 共9个

1. `arr.copyWithin(target[, start[, end]])` 这个方法又难理解又没什么卵用
2. `arr.fill(value[, start[, end]])` -> the modified array
3. `arr.pop()` 弹出数组的最后一个值，并返回这个值，如果数组是空的，就返回undefined
4. `arr.push(element1[, ...[, elementN]])`: 在数组的尾部插入元素，返回新数组的长度
5. `a.reverse()` 返回原数组的引用
6. `arr.shift()`: 删除数组的第一个元素，返回删除的这个元素，如果数组是空的，就返回undefined
7. `arr.sort([compareFunction])` // 比较函数返回一个数字，根据是否大于0来判断
8. `var arrDeletedItems = array.splice(start[, deleteCount[, item1[, item2[, ...]]]])`: 返回被删除的元素组成的数组，如果没有元素被删除，就返回一个空数组
9. `arr.unshift(element1[, ...[, elementN]])`: 在数组的头部插入元素，返回新数组的长度


### 判断、查找、遍历
**forEach、some、every、find、findIndex、indexOf、lastIndexOf、includes** 共8个

1. `arr.every(callback(element[, index[, array]])[, thisArg])`
2. `arr.find(callback[, thisArg])` -> 如果没找到就返回undefined
3. `arr.findIndex(callback(element[, index[, array]])[, thisArg])` -> 没找到就返回-1。另外，callback碰到未赋值的项时也会执行（稀疏数组也会执行callback）
4. `arr.forEach(callback(currentValue [, index [, array]])[, thisArg])`
5. `arr.includes(valueToFind[, fromIndex])`  相等性判断用的是 [SameValueZero](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness#Same-value-zero_equality)
6. `arr.indexOf(searchElement[, fromIndex])`
7. `arr.lastIndexOf(searchElement[, fromIndex])`
8. `arr.some(callback(element[, index[, array]])[, thisArg])`


### 工具方法
Array.from、Array.of、 Array.isArray
keys、 values、 entries

1. `Array.from(arrayLike[, mapFn[, thisArg]])   ->  a new Array instance`
2. `Array.isArray(value)`
3. `Array.of(element0[, element1[, ...[, elementN]]])`
4. `keys values entries`
5. `arr.toLocaleString([locales[, options]]);`: 没啥卵用
6. `arr.toString()` 相当于 arr.join(',')




### Array.of
与Array构造函数只有一点区别，当传一个数字n进去的时候， Array就生成一个长度为n的稀疏数组。
Array.of会生成一个[n]
```javascript
Array.of(7);       // [7] 
Array.of(1, 2, 3); // [1, 2, 3]

Array(7);          // [ , , , , , , ]
Array(1, 2, 3);    // [1, 2, 3]
```

### concat
```javascript
// cancat 可以连接单个元素和一维数组
const letters = ['a', 'b', 'c'];
const alphaNumeric = letters.concat(1, [2, 3]);
console.log(alphaNumeric); 
// results in ['a', 'b', 'c', 1, 2, 3]
```

### fill
```javascript
var array1 = [1, 2, 3, 4];
// fill with 0 from position 2 until position 4
console.log(array1.fill(0, 2, 4));
// expected output: [1, 2, 0, 0]
```

### flat
返回一个根据传入的depth扁平化的数组，depth默认为1，不会改变原数组
```javascript
var arr2 = [1, 2, [3, 4, [5, 6]]];
arr2.flat(); // [1, 2, 3, 4, [5, 6]]

// flat还会把数组中的empty slots去掉（把稀疏数组变为非稀疏数组）
var arr4 = [1, 2, , 4, 5];
console.log(arr4.flat())  // [1, 2, 4, 5]
```

### flatMap
等价于`map`之后，跟一个`depth`为1的`flat`
```javascript
let arr1 = [1, 2, 3, 4];

arr1.map(x => [x * 2]); // [[2], [4], [6], [8]]

arr1.flatMap(x => [x * 2]);// [2, 4, 6, 8]

arr1.map(x => [x * 2]).flat() // [2, 4, 6, 8]
```

### Array.from
```javascript
function range(from, to) {
  return Array.from({ length: to - from }, (v, i) => from + i)
}
console.log(range(1, 8))

Array.from(Array(5).keys())   <==> [...Array(5).keys()]
```

### reduce
如果没有提供initialValue，reduce会从index=1开始执行，跳过index=0
如果没有提供initialValue, 且arr为空，会抛出TypeError
如果没有提供initialValue, 且arr只有一个元素，reduce会直接返回这个元素而不会调用callback
如果提供了initialValue, 且arr为空，reduce会直接返回这个元素而不会调用callback