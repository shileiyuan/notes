注意：

 将任意元素的display设置为flex后，可将其转换为Flex容器，且其子元素的float、clear和vertical-align属性将失效



### flex容器属性： 

1, 主轴方向: 

`flex-direction: row (default) | row-reverse | column | column-reverse;`



2, 换行：

`flex-wrap: nowrap (default) | wrap | wrap-reverse;`



flex-flow: flex-direction 和 flex-wrap 的简写形式， 默认为 row nowrap



3, 主轴对齐方式：

`justify-content: flex-start (default) | flex-end | center | space-between | space-around;`



4, 交叉轴对齐方式：

`align-items: stretch (default) | flex-start | flex-end | center | baseline;`



5, 交叉轴有多行时，每一行的对齐方式，只有一行的时候没有效果

`align-content: stretch (default) | flex-start | flext-end | center | space-between | space-around;`



### **flex项目属性：**

1, 顺序： 数值越小越靠前，默认为0

`order: <number>`



2, 有剩余空间时的放大比例： 默认为0不放大，值为1则放大，2是1的双倍大小，以此类推

`flex-grow: <number>`



3, 空间不足时的缩小比例：默认为1，如果为0则为不缩小

`flex-shrink: <number>`



4, 定义项目的默认大小，默认为auto，即项目的本来大小

`flex-basis: auto (default) | <length>`



flex: 是flex-grow, flex-shrink 和 flex-basis 的简写，默认值为 0 1 auto。 后两个属性是可选的

该属性有两个关键字： `auto (1 1 auto) 和 none (0 0 auto)`



5, 对于特定项目的对齐方式，相当于覆盖容器定义的align-items，默认为auto，表示继承父元素的align-items属性

`align-self: auto | stretch |  flex-start | flex-start | flex-end | center | baseline;`







### 实际遇到的场景



**1.**  问题：设置了flex-grow的项目，overflow：auto 不起作用

原因：设置了flex-grow的元素，规范是在overflow：visible的情况下，min-height 会被默认设置成content的高度。其他情况下，min-height 被设置成0。但是浏览器的实现bug，所有情况下都min-height都被设置成了content的高度，导致overflow：auto失效。[flex-grow, min-height, overflow](https://medium.com/@stephenbunch/how-to-make-a-scrollable-container-with-dynamic-height-using-flexbox-5914a26ae336)

解决办法：给设置了flex-grow: 1的元素，手动设置min-height: 0即可



