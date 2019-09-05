### 浏览器模式

依赖于HTML文件顶部的doctype声明。
js中可以通过document.compatMode来确定。如果为'CSS1Compat'就是标准模式，如果为'BackCompat'或undefined就是怪异模式



### 脚本化图片

通过new Image() 创建一个新对象， 设置它的src属性，但是不把它添加到文档中。

这样浏览器会加载图片并缓存起来。当其他图片使用相同的URL属性时，就会从缓存中加载而不是重新下载了。



### 拖拽

[w3c-html_5_draganddrop](http://www.w3school.com.cn/html5/html_5_draganddrop.asp)

