### !important
 语法： **Selector** { sRule!important; }

### @media

```css
@import url(example.css) screen and (width:800px);
@media screen and (width:800px){ … };
@media all and (width:1024px){
	body{color:#f00;}
}
@media all and (device-height:800px){ … }
@media all and (orientation:landscape){ … }
@media all and (device-aspect-ratio:16/10){ … }
@media all and (min-color:1){ … }
@media all and (monochrome:0){ … }
@media all and (grid:0){ … }
```

```html
<link media="screen and (width:800px)" rel="stylesheet" href="example.css">
<?xml-stylesheet media="screen and (width:800px)" rel="stylesheet" href="example.css" ?>
```



### @import
语法： **@import <url> <media_query_list>**
```css
@import url("global.css");
@import url(global.css);
@import "global.css";
```
以上3种方式都有效。当使用url(url)的方式时，包住路径的引号可有可无；当直接使用url时，包住路径的引号必须保留。

**指定媒体查询**
```css
@import url(example.css) screen and (min-width:800px);
@import url(example.css) screen and (width:800px),(color);
@import url(example.css) screen and (min-device-width:500px) and (max-device-width:1024px);
```

### @font-face
```css
@font-face {
  font-family: "Open Sans";
  src: url("/fonts/OpenSans-Regular-webfont.woff2") format("woff2"),
       url("/fonts/OpenSans-Regular-webfont.woff") format("woff");
}
```
### IE hack
```html
<!--[if gte IE 6]>
<style>
.test{color:red;}
</style>
<![endif]-->
```



