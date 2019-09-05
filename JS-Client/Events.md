### DOM事件流
事件流包括三个阶段：事件捕获阶段、处于目标阶段和事件冒泡阶段。

在DOM事件流中，实际的目标在捕获阶段不会接收到事件。

先发生事件捕获，到实际目标之前的元素结束，然后在实际目标元素上发生事件（处于目标阶段），但是事件处理被看成冒泡阶段的一部分。

然后，冒泡阶段发生，事件又传播回文档。

### 关闭页面之前触发的事件
```javascript
window.addEventListener('beforeunload', function(e){
  // 注意，这里面不能出现弹框 alert confirm showModalDialog prompt 等，否则会被忽略不执行
  console.log(e) ;
  e.returnValue = '是否真的要关闭？';
  return '是否真的要关闭？??'
})
```
return的字符串可能不会显示在弹窗中，但是浏览器会自己提供一个固定内容的弹窗，询问是否要离开。为了兼容， return 和 returnValue都是需要的。

### 自定义事件
document.createEvent