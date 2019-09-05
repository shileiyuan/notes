### 获取iframe元素的window对象
1. iframe.contentWindow
2. window.iframes: 可以得到一个包含所有的iframe元素的window对象的数组
3. 通过iframe的id或name属性

一般的，在HTML中使用的id属性会成为可以被js访问的window下的全局变量。如一个`<button id="okay" />`元素，可以直接通过okay来引用该元素。
如果window对象上已经有一个属性okay了，上面的事情就不会发生了。
但是iframe元素不同于一般元素，通过iframe元素的id或name属性获取到的是iframe的window对象，而不是iframe元素。

### 在iframe元素中获取父元素的window对象
window.parent
window.top

### iframe与父页面之间通信

#### 向目标window发送数据
```javascript
targetWindow.postMessage(data, targetOrigin)
```
targetOrigin: 是一个字符串，指定目标窗口的源。其中包括协议、主机名以及端口号（其他的设置了也不起作用）。如果要传递的消息不包括任何敏感信息的话，可以将参数设置为"*".如果要指定和当前窗口同源的话，可以设置为"/"。

早期的data只能传字符串，所以为了兼容性，最好JSON.stringify(data)，然后在接收方JSON.parse(e.data )


#### 接收数据
```javascript
window.addEventListener('message', function(e) {
  console.log(e.data)
})
```

### 加载顺序问题
先在iframe的window对象上触发onlaod，然后在父页面的iframe元素上触发onload，然后触发父页面的window对象的onload事件

### 作用域问题
每个iframe都有自己的window对象。
如果在窗体间共享函数，要牢记：函数在定义它的作用域中执行，而不是调用它的作用域。