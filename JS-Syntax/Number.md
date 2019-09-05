### 数字的表示法
在JavaScript中，所有的Number都是以64-bit的双精度浮点数存储的，即IEEE-754双精度浮点数。

二进制在存储的时候是以二进制的科学计数法来存储的。

64bit的划分是“1-11-52”的模式：
1：最左边那位，符号位，1表示负，0表示正
11: 表示指数部分
52: 表示尾数部分

**符号位决定正负，指数域决定数量级，尾数域决定精度**

https://segmentfault.com/a/1190000009084877


```javascript
Math.round: 四舍五入
Math.floor: 舍去小数
Math.ceil: 小数向上进一位
```



```javascript
//十进制转其他  
var x = 110;
console.log(x);
console.log(x.toString(8));
console.log(x.toString(32));
console.log(x.toString(16));

//其他转十进制  
var x = '110';
console.log(parseInt(x, 2));
console.log(parseInt(x, 8));
console.log(parseInt(x, 16));

//其他转其他  
//先用parseInt转成十进制再用toString转到目标进制  
console.log(String.fromCharCode(parseInt(141, 8)))
console.log(parseInt('ff', 16).toString(2));   
```

