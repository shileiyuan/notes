### 1. 字体限制

chrome限制字体的最小值是12px，如果想要显示更小的效果，可以使用`transform: scale(0.75)`



### 2. 单行不换行，显示省略号

```css
white-space:nowrap;
text-overflow:ellipsis;
overflow:hidden;
```



### 3. padding和margin的百分比

padding和margin为百分比时，是按照父级元素的**宽度**来计算的，而宽度，实际上正是自适应和现代web设计的灵魂

```html
<div class="constant-width-to-height-ratio"></div>
<style>
  /*宽度和高度的比例固定的元素 */
 .constant-width-to-height-ratio {
   background: #333;
   width: 50%;
 }
 .constant-width-to-height-ratio::before {
   content: '';
   padding-top: 100%;
   float: left;
 }
 .constant-width-to-height-ratio::after {
   content: '';
   display: block;
   clear: both;
 }
</style>
```



### 4. flex和负的margin搭配可以实现类似antd的Tab组件的效果

```html
<div className='app-tab'>
  <div className='app-tab-content'>
    <div className='app-tab-pane pane1'>pan1</div>
    <div className='app-tab-pane pane2'>pan2</div>
    <div className='app-tab-pane pane3'>pan3</div>
  </div>
</div>

<style>
.app-tab {
  border: 1px solid red;
  width: 200px;
  height: 500px;
  margin: auto;
  &-content {
    display: flex;
    flex-flow: row nowrap;
    width: 100%;
    margin-left: -100%;
    transition: margin-left .3s cubic-bezier(.645,.045,.355,1)
  }
  &-pane {
    width: 100%;
    flex-shrink: 0;
  }
}
</style>
```



### 5. 紧贴底部的页脚

问题描述：
有一个具有块级样式的页脚，当页面足够长时，它一切正常，当页面较短时，就会出现问题：页脚不能紧贴在视口的最底部，而是紧跟在内容的下方。

解决方案：
1. `min-height + calc + vh`： 如果页面布局很复杂，需要计算的高度太多了，就完全不适用了
2. flex布局



### 6. 常用颜色值

> \#ccc
>
> tomato