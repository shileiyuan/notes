### 1. 选取元素
* getElementById
* getElementsByTagName
* getElementsByClassName
* getElementsByName
* querySelector
* querySelectorAll

`getElementsByName` 和 `getElementsByTagName` 都返回 `NodeList` 对象，而类似 `document.images` 和 `document.forms` 的属性为 `HTMLCollection` 的对象。`NodeList` 和 `HTMLCollection` 对象不是历史文档状态的一个静态快照，而是实时的，并且当文档变化时他们所包含的元素列表能随之改变。
所以，如果要在迭代一个 `NodeList` 对象的同时，要在文档中添加或删除元素，通常会需要对NodeList对象生成一个静态副本：`var snapshot = Array.prototype.slice.call(nodelist, 0);`ßßß

***
### 2. 作为节点树的文档(不好用，因为我们通常不关心Text和Comment节点)
Document对象、Element对象和Text对象都是Node对象。Node对象定义了以下重要的属性：
1. parentNode
2. childNodes
3. firstChild、lastChild
4. nextSibling previousSibling
5. nodeType：该节点的类型， 9代表Document节点，1代表Element节点，3代表Text节点， 8代表Comment节点， 11代表DocumentFragment节点
6. nodeValue: Text节点或Comment节点的文本内容
7. nodeName： 若为标签则返回的值为标签名的大写形式“DIV”，否则为#document  #element  #text   #comment   #document-fragment

***
### 3. 作为元素树的文档（常用，忽略Text和Comment节点）
1. children（并非标准属性，但是在所有浏览器上都能工作，包括IE）
2. firstElementChild， lastElementChild
3. nextElementSibling, previousElementSibling
4. childElementCount = children.length

***
### 4. HTML代码中的属性名  VS  JS脚本中的属性名
1. HTML中的属性名不区分大小写，JS脚本中应该用小写的属性名来访问属性
2. 如果属性名包含不止一个单词，JS中采用驼峰命名
3. 如果HTML属性名为JS中的保留字，一般在JS中要加前缀‘html’， <label/>标签的for属性变为htmlFor
4. class ---> className
5. 在JS中表示HTML属性的值通常是字符串，但是当属性值为布尔值或数值时（例如，input元素的defaultChecked和maxLength属性），JS中的属性值也是布尔值或数值而不是字符串
6. 事件处理程序属性值总是为Function对象或null
7. 任何HTML元素的style属性值是CSSStyleDeclaration对象而不是字符串

***
### 5. 获取和设置非标准HTML属性
HTML标准属性可以通过上面介绍的通过元素JS属性名直接获取和修改，但是非标准属性名不可以。
但是可以通过getAttribute setAttribute hasAttribute removeAttribute来获取、设置、检测和删除标准或不标准的属性。
这种方式与前面的基于标准属性的API之间有两个重要区别：
1. 所有属性值都被看作是字符串，getAttribute只返回字符串或null，不返回数值，布尔值和对象。
2. 这些方法使用与HTML属性名相同的属性名，不再需要加html前缀，驼峰或className变形了。

***
### 6. 数据集属性
1. 上面所说的非标准属性付出的代价是文档将不再是合法有效的HTML
2. HTML5提供了一个方案： 任意以‘data-’为前缀的小写的属性名字都是合法的。
3. Element对象的dataset属性可以访问这些自定义属性。此时应去掉前缀data-，且带连字符的属性对应于驼峰命名法，如data-jquery-test对应于： dataset.jqueryTest

***
### 7. 获取元素的内容
#### 获取html字符串： 
* innerHTML: 所有浏览器都支持
* outerHTML: 返回的字符串包含被查询元素的开头和结尾标签

#### 获取元素的文本内容
* textContent: 除了IE都支持，将指定元素的所有后代Text节点简单的串联到一起
* innerText： 除了firefox都支持

#### 获取Text节点的文本内容
nodeValue： Element节点的nodeValue为null

***
### 8. 创建、插入、删除和替换节点
1. 创建： document.createElement()  document.createTextNode()  document.createDocumentFragment
2. 插入： appendChild()  insertBefore(), insertBefore是在待插入节点的父节点上调用的，接受两个参数，第一个是要插入的节点，第二是一个父节点下面的节点，新节点将插入到该节点的前面
3. 删除节点： removeChild
4. 替换节点： replaceChild(newChild, oldChild)

**如果将一个已经存在的节点append到另一个位置，相当于移动了这个节点**

***
### 9. DOM层级
document.body -> document.documentElement(html) -> document -> window

***
### 10. 高级点的
element.insertAdjacentHTML(position, text);
```html
<!-- beforebegin -->
<p>
  <!-- afterbegin -->
  foo
  <!-- beforeend -->
</p>
<!-- afterend -->
```

### DOM对象属性和标签属性
[jQuery 的 attr 与 prop 的区别](https://github.com/JChehe/blog/blob/master/posts/jQuery%20%E7%9A%84%20attr%20%E4%B8%8E%20prop%20%E7%9A%84%E5%8C%BA%E5%88%AB.md)