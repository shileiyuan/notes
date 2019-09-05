### HTML名称   VS  JS名称
如果有连字符，用驼峰命名： font-family  ---> e.style.fontFamily = 'sans-serif'
如果在JS中是保留字，要加css前缀： float  ---> e.style.cssFloat

### 使用CSSStyleDeclaration对象时，需要注意的地方
所有的值都是字符串
所有的定位属性都必须加单位  e.style.left = ‘200px’

### 内联样式字符串
设置： `e.setAttribute('style', s)   <=>  e.style.cssText = s`
查询： `e.getAttribute('style')  <=>  e.style.cssText`

内联样式字符串通常只在设置样式时有用，如果需要查询元素的样式，就要使用计算样式

### 计算样式
元素的计算样式是一组属性值，它由浏览器通过把内联样式结合所有链接样式表中所有可应用的样式规则后计算得到的：它就是一组在显示元素时实际使用的属性值。
类似内联样式，计算样式也是用一个`CSSStyleDeclaration`对象来表示的，区别是，计算样式是只读的。

查询计算样式： **window.getComputedStyle(element, '')**
第一个参数就是要获取其计算样式的元素，第二个参数也是必须的，通常是null或字符串，也可以是命名CSS伪对象的字符串，如':before', ':after', ':first-line' 或 ':first-letter'

### 脚本化类
设置元素的class为attention： e.className = 'attention'
移除attention类： e.className = ''

缺陷：HTML元素可以有多个css类名，class属性保存了一个用空格隔开的类名列表。上面这种方法会覆盖其他的类名。
方法： HTML5为每个元素定义了一个classList属性，是一个只读的类数组对象，有四个方法：
add remove toggle contains

### 脚本化样式表
document.styleSheets属性是一个只读的类数组对象，他包含CSSStyleSheet对象，表示与文档关联在一起的样式表。
style、link和CSSStyleSheet对象都定义了一个在js中可以设置和查询的disabled属性。如果disabled为true，样式表就被浏览器关闭并忽略。
```javascript
function toggleStyleSheets(ss, enable) {
  if (typeof ss === 'number') {
    document.styleSheets[ss].disabled = !enable
  } else {
    var sheets = document.querySelector(ss)
    for (let i = 0; i < sheets.length; i++) {
      sheets[i].disabled = !enable;
    }
  }
}
```