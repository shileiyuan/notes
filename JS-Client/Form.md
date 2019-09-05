### FormData
该对象不是一个普通的对象，不能直接console.log()打印，
要遍历这个对象需要调用这个对象本身的方法（keys values entries 等 ）获取一个iterator对象。

```javascript
var submit = document.getElementById('btn');
var myform = document.getElementById('myform');
submit.addEventListener('click', function (e) {
  var formData = new FormData(myform);
  formData.append('age', '10');
  for (item of formData.entries()) {
    console.log(item)
  }
})
```

### form标签
#### 常用属性：
1. elements: 表单中所有控件的集合
2. name： 表单的名称

#### 获取表单
1. document.getElementById
2. document.forms[0]

### change事件
输入框和文本域的change事件，仅当用户改变了元素的值然后将焦点移到其他元素上时才会触发。也就是说，调用该事件处理程序就意味着一个完整的改变。

### select标签
select标签如果没有multiple属性，则type属性为 select-one, 如果有multiple属性，则type属性为select-multiple

#### select的属性：
1. size: 当size大于1时，将显示列表中的选项（可能会有滚动）
2. 当用户选取或取消选取一个选项时，select元素会触发onchange事件
3. 当type为select-one时，可读写属性selectedIndex指定了哪个选项当前被选中
4. 当type为select-multiple时，就必须遍历options，并检测每个对象的selected属性值
5. options是一个包含了多个Option元素的类数组对象

#### option的属性
1. text：可读写，指定了option显示的字符串
2. value：可读写，指定了提交表单时，提交给服务器的字符串
3. selected： 可读写
4. index：当前选项在options集合中的索引