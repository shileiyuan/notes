### 变量声明
$highlight-color: #F90;

1. 变量具有块级作用域
2. 声明变量时，可以引用其他变量   $highlight-border: 1px solid $highlight-color;
3. 父选择器： & ， 这个符号会替换为article a

    ```css
    article a {
      color: blue;
      &:hover {color: red}
    }
    ```
4. \+ \~ \> 等选择器可以放在外层选择器后边或者里层选择器前边

    ```css
    article {
      ~ article { border-top: 1px dashed #ccc }
      > section { background: #eee }
      dl > {
        dt { color: #333 }
        dd { color: #555 }
      }
      nav + & { margin-top: 0 }
    }
    ```
5. 属性嵌套：把属性名从中划线-的地方断开，在根属性后边添加一个冒号:，紧跟一个{ }块，把子属性部分写在这个{ }块中。

    ```css
    // 跟规则嵌套的区别就是属性嵌套后面有一个冒号
    nav {
      border: {
        style: solid;
        width: 1px;
        color: #ccc;
      }
    }
    
    // 对于属性的缩写形式，你甚至可以像下边这样来嵌套，指明例外规则：
    nav {
      border: 1px solid #ccc {
        left: 0px;
        right: 0px;
      }
    }
    ```
### @imoprt
css的@import规则允许在一个css文件中导入其他css文件，但是只有在执行到@import时，浏览器才会去下载其他css文件，导致页面加载起来特别慢。

sass的@import规则在生成css文件时就把相关文件导入进来。这意味着所有相关样式被归纳到了同一个css文件中，而无需发起额外的下载请求。
导入时可以省略.sass或.scss文件后缀。

在下列三种情况下会生成原生的css@import
1. 被导入文件的名字以.css结尾
2. 被导入文件的名字是一个URL地址（比如http://www.sass.hk/css/css.css），由此可用谷歌字体API提供的相应服务；
3. 被导入文件的名字是CSS的url()值。

### @mixin
给一段样式赋予一个名字，这样就可以通过引用这个名字重用这段样式。
```css
// 定义mixin
@mixin rounded-corners {
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}

// 通过@include来使用这个混合器
notice {
  background-color: green;
  border: 2px solid #00aa00;
  @include rounded-corners;
}
```

css的类名是语义性的，描述这个类所在的元素的用途。
而mixin是展示性的，描述了这个规则的视觉样式。

#### 给mixin传参

```css
//定义mixin及其需要的参数

//这里给$normal指定了默认值，可以是任何css属性或者其他变量
@mixin link-colors($normal: red, $hover, $visited) {  
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}

// 使用mixin
a {
  @include link-colors(blue, red, green);
}

// 上面这种方式要注意参数的顺序，下面这种方式通过$name:value的形式指定了每个参数的值，所以不用在乎参数顺序
a {
    @include link-colors(
      $normal: blue,
      $visited: green,
      $hover: red
    );
}
```

### 继承
我的理解就是一个类可以继承另一个类来取得另一个类的所有样式。
```css
//通过选择器继承继承样式
h.error {
  border: 1px solid red;
  background-color: #fdd;
}
.seriousError {
  @extend .error;
  border-width: 3px;
}

//编译之后的样式：在所有出现.error的地方，用.error, .seriousError替换
h.error, h.seriousError {
  border: 1px solid red;
  background-color: #fdd;
}

.seriousError {
  border-width: 3px;
}
```

### 插值语句#{}
```css
$name: foo;
$attr: border;
p.#{$name} {
  #{$attr}-color: blue;
}

编译为：
p.foo {
  border-color: blue;
}
```

### @for    
through包含开始和结束，to包含开始，不包含结束
```css
@for $i from 1 through 3 {
  .item-#{$i} { width: 2em * $i; }
}

编译为
.item-1 { width: 2em; }
.item-2 { width: 4em; }
.item-3 { width: 6em; }
```

### @each
```css
@each $animal in puma, sea-slug, egret, salamander {
  .#{$animal}-icon {
    background-image: url('/images/#{$animal}.png');
  }
}

编译为
.puma-icon {
  background-image: url('/images/puma.png'); }
.sea-slug-icon {
  background-image: url('/images/sea-slug.png'); }
.egret-icon {
  background-image: url('/images/egret.png'); }
.salamander-icon {
  background-image: url('/images/salamander.png'); }



@each $animal, $color, $cursor in (puma, black, default),
                                  (sea-slug, blue, pointer),
                                  (egret, white, move) {
  .#{$animal}-icon {
    background-image: url('/images/#{$animal}.png');
    border: 2px solid $color;
    cursor: $cursor;
  }
}
编译为
.puma-icon {
  background-image: url('/images/puma.png');
  border: 2px solid black;
  cursor: default; }
.sea-slug-icon {
  background-image: url('/images/sea-slug.png');
  border: 2px solid blue;
  cursor: pointer; }
.egret-icon {
  background-image: url('/images/egret.png');
  border: 2px solid white;
  cursor: move; }



@each $header, $size in (h1: 2em, h2: 1.5em, h3: 1.2em) {
  #{$header} {
    font-size: $size;
  }
}

编译为
h1 {
  font-size: 2em; }
h2 {
  font-size: 1.5em; }
h3 {
  font-size: 1.2em; }
```

### 为不同的主题生成不同的样式
```css
$blue: (color1:rgba(0, 0, 0, .2),color2:#fff);
$white: (color1:#fff,color2:#6c7480);
@mixin genCSS($theme){
  @each $name, $map in $theme {
      :global(.#{$name}){
        @include getTheme($map)
      }
  }
}

// 将自己写的样式用getTheme包起来
@mixin getTheme($map){
  .depoly-container {
    background-color: map-get($map, color1);
    color: map-get($map, color2);
  }
}

@include genCSS(('blue': $blue, 'white': $white));



// 编译成
:global(.blue) .depoly-container {
  background-color: rgba(0, 0, 0, 0.2);
  color: #fff;
}

:global(.white) .depoly-container {
  background-color: #fff;
  color: #6c7480;
}
```