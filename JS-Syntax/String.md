String 都是 immutable 的

### 常用方法
* localeCompare：使用本地特定的顺序比较两个字符串

  > 当在字符串中使用< 或 > 操作符时，它们只比较这些字符的Unicode编码，而不考虑本地的顺序。
* replace:

  ```javascript
  String.prototype.escape = (function() {
    var entity = {
      quote: '"',
      lt: '<',
      gt: '>'
    }
    return function() {
      return this.replace(/&([^&;])+;/g, function(a, b) {
        var r = entity[b]
        return typeof r === 'string' ? r : a
      })
    }
  }())
  String.prototype.escape = (function () {
    var entity = {
      quote: '"',
      lt: '<',
      gt: '>'
    }
    return function () {
      return this.replace(/&([^&;]+);/g, function (a, b) {
        var r = entity[b]
        return typeof r === 'string' ? r : a
      })
    }
  }())
  console.log('&lt;&quote;Hello&quote;&gt;'.escape())  // <"Hello">
  ```

## 静态方法

### String.fromCharCode
String.fromCharCode(num1[, ...[, numN]])  只能处理两个字节的UTF-16编码
returns a string created from the specified sequence of UTF-16 code units.
```javascript
String.fromCharCode(65, 66, 67);  // returns "ABC"
String.fromCharCode(0x2014)       // returns "—"
```

### String.fromCodePoint
String.fromCodePoint(num1[, ...[, numN]])  可以处理所有编码
作用类似于String.fromCharCode，由于String.fromCharCode不能识别码点大于`0xFFFF`的字符，并且最高位会被舍弃。所以新增了String.fromCodePoint方法
```javascript
console.log(String.fromCodePoint(0x2F804))
```

### String.raw

`String.raw(callSite, ...substitutions)`

String.raw\`templateString\`

```javascript
String.raw`Hi\n${2+3}!` // 'Hi\n5!'

String.raw({ raw: 'test' }, 0, 1, 2) // 't0e1s2t'
```

## 常用方法

1. `character = str.charAt(index)`: 返回字符串index位置的字符
  实际使用上不好用，字符的位置是相对于UTF-16编码（即每个字符占两个字节）来说的，如果一个字符占用四个字节的话，该方法就会返回乱码。所以严格来说要像下面这种情况处理这种情况
    ```javascript
    // 注意：该字符不是'吉祥'的'吉'
    var a = '𠮷'
    // 扩展运算符可以把字符串转换为数组，而且扩展运算符可以正确识别4个字节的Unicode字符
    function charAt(str, index) {
      return [...str][index]
    }
    console.log(charAt(a, 0))
    ```
2. `str.charCodeAt(index)`: 返回字符编码 只能处理两位字节的编码
3. `str.codePointAt(pos)`: 返回字符编码，可以处理四位字节的编码
    ```javascript
      // 返回字符串某一个字符的编码
      function codePointAt(str, index) {
        return [...str][index].codePointAt(0)
      }
    ```

总之，先用扩展运算符把字符串转换成字符数组，然后再对字符数进行组取长度，获取特定位置的字符或者字符编码才是最安全的。


endsWith  includes indexOf lastIndexOf

`str.startsWith(searchString[, position])`: 判断一个字符串是否以某个特定的字符串开头

`str.endsWith(searchString[, length])`: length参数可选，表示要操作的str的长度，如果省略，默认是str的长度

`str.includes(searchString[, position])`: position表示要执行搜索的位置，默认是0

`str.indexOf(searchValue[, fromIndex])`
```javascript
// 使用indeOf来获取字符串中字母e出现的次数
const str = 'To be, or not to be, that is the question.';
let count = 0;
let position = str.indexOf('e');
while (position !== -1) {
  count++;
  position = str.indexOf('e', position + 1);
}
console.log(count); // displays 4

// 也可以用str.match(reg)
var matches = str.match(/e/g)
console.log(matches.length)
```
`referenceStr.localeCompare(compareString[, locales[, options]])`: 有点复杂，以后再说

`str.padStart(targetLength [, padString])`: 填充特定字符到指定的长度

`str.padEnd(targetLength [, padString])`

`str.repeat(count)`

`str.split([separator[, limit]])`: split的一个问题是，当用''进行分割时，是按照UTF-16编码的code进行分割的，解决这个问题有个2办法
var a = '𠮷'
1. 扩展运算符：[...a]
2. Array.from(a)
3. '𠮷'.split(/(?=[\s\S])/u) //用到了正向零宽断言

`str.slice(beginIndex[, endIndex])`: beginIndex 和 endIndex 都可以是负数，如果beginIndex大于endIndex，则返回空字符串。

`str.trim()`
`str.trimStart()  str.trimLeft()`: 这两个一样
`str.trimEnd(); str.trimRight();`: 这两个也一样