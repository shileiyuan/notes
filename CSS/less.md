### 1. cli
安装： `npm install -g less`

编译less文件，并打印到控制台：
`lessc styles.les`

编译less文件到一个单独的文件中：
`lessc styles.less styles.css`

在代码中调用：
```javascript
var less = require('less'); 
less.render('.class { width: (1 + 1) }', function (e, output) {    
    console.log(output.css);
    // 输出： .class { width: 2; }
});
```

### 2. 变量
用@符号定义
```less
@nice-blue: #5B83AD;
@light-blue: @nice-blue + #111;
#header { color: @light-blue; }
```
```css
#header { color: #6c94be; }
```

### 3. mixin
可以将class名称或id名称的规则当作一个封装，在其他地方引用
例如有一个class
```less
.bordered {
  border-top: dotted 1px black;
  border-bottom: solid 2px black;
}
```
可以在其他的规则里面引用这个class
```less
#menu a {
  color: #111;
  .bordered;
}
.post a {
  color: red;
  .bordered;
}
```
如果不想让mixin也输出到css里面取，可以在mixin后面加一对括号,
而且mix中也是可以包含选择器的
```less
.my-hover-mixin() {
  &:hover {
    border: 1px solid red;
  }
}
button {
  .my-hover-mixin();
}
```
Outputs:
```css
button:hover {
  border: 1px solid red;
}
```
#### 3.1 引用mixin下面的一部分
```less
#outer() {
  .inner {
    color: red;
  }
}
// 只引用#outer下面的inner
.c {
  // all do the same thing
  #outer > .inner();
  //#outer .inner();
  //#outer.inner();
}
```
Outputs:
```css
.c {
  color: red;
}
```
#### 3.2  !important
Use the !important keyword after mixin call to mark all properties inherited by it as !important
```less
.foo (@bg: #f5f5f5, @color: #900) {
  background: @bg;
  color: @color;
}
.unimportant {
  .foo();
}
.important {
  .foo() !important;
}
```
Outputs:
```css
.unimportant {
  background: #f5f5f5;
  color: #900;
}
.important {
  background: #f5f5f5 !important;
  color: #900 !important;
}
```

#### 3.3 mixin 多个参数
mixin可以有参数，多个参数之间用分号分隔（也可以用逗号，但容易引起冲突）；且参数可以有默认值；且mixin的名字可以重复，并且所有满足参数要求的mixin都会被解析。
```less
.mixin(@color) {
  color-1: @color;
}
.mixin(@color; @padding: 2) {
  color-2: @color;
  padding-2: @padding;
}
.mixin(@color; @padding; @margin: 2) {
  color-3: @color;
  padding-3: @padding;
  margin: @margin @margin @margin @margin;
}
.some .selector div {
  .mixin(#008000);
}
```
Outputs:
```css
/* 第一个和第二个mixin都被解析了，第三个mixin的padding没有实参，也没有默认值，所以没有被输出 */
.some .selector div {
  color-1: #008000;
  color-2: #008000;
  padding-2: 2;
}
```

#### 3.4 mixin的实参和虚参的对应不仅仅是按照位置的，第一优先级是按照参数的名字的
这个是真的鸡肋
```less
.mixin(@color: black; @margin: 10px; @padding: 20px) {
  color: @color;
  margin: @margin;
  padding: @padding;
}
.class1 {
  .mixin(@margin: 20px; @color: #33acfe);
}
.class2 {
  // 100px按照位置对应为@color,@padding按照参数的名字对应
  .mixin(100px; @padding: 40px);
}
```
Outputs: 
```css
.class1 {
  color: #33acfe;
  margin: 20px;
  padding: 20px;
}
.class2 {
  color: 100px;
  margin: 10px;
  padding: 40px;
}
```

### 3.5 mixin的匹配
```less
.mixin(dark; @color) {
  color: darken(@color, 10%);
}
.mixin(light; @color) {
  color: lighten(@color, 10%);
}
.mixin(@a; @color) {
  display: block;
}

@switch: light;

.class {
  .mixin(@switch; #888);
}
```
Outputs:
```css
.class {
  color: #a2a2a2;
  display: block;
}
```
分析：第一个mixin不匹配，因为第一个参数需要是dark；第二个参数匹配；第三个也匹配，因为，第三个mixin需要的第一个参数是任意的都行。

**根据参数个数匹配：**
```less
.mixin(@a) {
  color: @a;
}
.mixin(@a; @b) {
  color: fade(@a; @b);
}
```
如果调用mixin的时候传入一个参数，则第一个匹配，如果传入两个参数，则第二个匹配

#### 3.6 在mixin里面定义的mixin，当外部mixin被引用的时候，内部的mixin也相当于被导入了进来
```less
.unlock(@value) { // outer mixin
  .doSomething() { // nested mixin
    declaration: @value;
  }
}

#namespace {
  .unlock(5); // unlock doSomething mixin
  .doSomething(); //nested mixin was copied here and is usable
}
```

Outputs:
```css
#namespace {
  declaration: 5;
}
```

#### 3.7 recursive mixins
在less中，mixin可以调用自己，利用这个特性可以构建循环结构
```less
.loop(@counter) when (@counter > 0) {
  .loop((@counter - 1));    // next iteration
  width: (10px * @counter); // code for each iteration
}

div {
  .loop(5); // launch the loop
}
```
Outputs:
```css
div {
  width: 10px;
  width: 20px;
  width: 30px;
  width: 40px;
  width: 50px;
}
```

```less
.generate-columns(4);

.generate-columns(@n, @i: 1) when (@i =< @n) {
  .column-@{i} {
    width: (@i * 100% / @n);
  }
  .generate-columns(@n, (@i + 1));
}
```
Outputs:
```css
.column-1 {
  width: 25%;
}
.column-2 {
  width: 50%;
}
.column-3 {
  width: 75%;
}
.column-4 {
  width: 100%;
}
```

#### 3.8 mixin guards
```less
.mixin(@a) when (lightness(@a) >= 50%) {
  background-color: black;
}
.mixin(@a) when (lightness(@a) < 50%) {
  background-color: white;
}
.mixin(@a) {
  color: @a;
}
.class1 { .mixin(#ddd) }
.class2 { .mixin(#555) }

// Outputs:
.class1 {
  background-color: black;
  color: #ddd;
}
.class2 {
  background-color: white;
  color: #555;
}
```

```less
// 参数之间也可以互相比较
@media: mobile;

.mixin(@a) when (@media = mobile) { ... }
.mixin(@a) when (@media = desktop) { ... }

.max(@a; @b) when (@a > @b) { width: @a }
.max(@a; @b) when (@a < @b) { width: @b }
```
逻辑操作符
```less
//and
.mixin(@a) when (isnumber(@a)) and (@a > 0) { ... }

//or: you can emulate the or operator by separating guards with a comma ,.
.mixin(@a) when (@a > 10), (@a < -10) { ... }

//not
.mixin(@b) when not (@b > 0) { ... }
```

类型检查函数：
1. iscolor
2. isnumber
3. isstring
4. iskeyword
5. isurl

带单位的数字也会被认为是数字，如果你想更精确的确定数字的单位，可以：
1. ispixel
2. ispercentage
3. isem
4. isunit

### 4. Nested Rules: 嵌套规则
```less
// 嵌套
#header { 
  color: black; 
  .navigation { font-size: 12px; } 
  .logo { width: 300px; }
}

// 经典的清除浮动 &代表当前选择器的上一级元素, 即 .clearfix
.clearfix {
  display: block; 
  zoom: 1; 
  &:after { 
    content: " "; 
    display: block; 
    font-size: 0; 
    height: 0; 
    clear: both; 
    visibility: hidden; 
  } 
}
```

### 4 detached rulesets
定义： 
```less
// declare detached ruleset
@detached-ruleset: { background: red; }; // semi-colon is optional in 3.5.0+

// use detached ruleset
.top {
    @detached-ruleset(); 
}

// Outputs:
.top {
  background: red;
}
```
```less
.desktop-and-old-ie(@rules) {
  @media screen and (min-width: 1200px) { @rules(); }
  html.lt-ie9 &                         { @rules(); }
}
header {
  background-color: blue;
  .desktop-and-old-ie({
    background-color: red;
  });
}

// Outputs:
header {
  background-color: blue;
}
@media screen and (min-width: 1200px) {
  header {
    background-color: red;
  }
}
html.lt-ie9 header {
  background-color: red;
}
```

### Nested Directives and Bubbling　
这个有点复杂

### Operations
这个有点不稳定

### Escaping
没看懂有什么用

### Functions
这个可以有时间再看看到底有什么用

### Namespaces and Accessors
可以将一些规则封装起来以供其他地方使用
```less
//在#bundle下封装一些规则
#bundle {
  .button {
    display: block;
    border: 1px solid black;
    background-color: grey;
    &:hover {
      background-color: white
    }
  }
  .tab { ... }
  .citation { ... }
}


// Now if we want to mixin the .button class in our #header a, we can do:
//在这里使用这些规则：

#header a {
  color: orange;
  #bundle > .button;
}
```

### scope
less的变量也是有作用域的，一个大括号就是一个作用域，先找自己作用域的，找不到再去上层找。而且在同一个作用域中，可以先使用再定义。
```less
@var: red;

#page {
  #header {
    color: @var; // white
  }
  @var: white;
}
```
### comments
注释  // 和 /* */ 都可以


### import
importing works pretty much as expected. You can import a .less file, and all the variables in it will be available. The extension is optionally specified for .less files.
```less
@import "library"; // library.less
@import "typo.css";
```


### use plugin in Node

```javascript
import less from 'less'
import LessPluginThemes from '../src'
import fs from 'fs'
import path from 'path'

const lessString = fs.readFileSync(path.join(__dirname, 'index.less')).toString()

less.render(lessString, {
  plugins: [new LessPluginThemes()]
}).then((result) => {
  console.log(result.css)
  fs.writeFileSync(path.join(__dirname, 'index.css'), result.css)
})

```