### 应用实例
```javascript
import React from 'react';
import style from './App.css';

export default () => {
  return (
    <h1 className={style.title}>
      Hello World
    </h1>
  );
};

.title {
  color: red;
}
```

构建工具会将style.title和css文件中的title编译成一个哈希字符串，就形成了一个唯一的class名称，从而达到了局部作用域的效果。

### webpack开启CSS Module功能
`{test: /\.css$/, loader: "style-loader!css-loader?modules"}`

### global

CSS Modules 允许使用`:global(.className)`的语法，声明一个全局规则。凡是这样声明的class，都不会被编译成哈希字符串。

App.css里加入一个全局class
```css
.title {
  color: red;
}

:global(.title) {
  color: green;
}
```

App.js使用普通的class的写法，就会引用全局class。
```javascript
import React from 'react';
import styles from './App.css';

export default () => {
  return (
  <h1 className="title">
    Hello World
  </h1>
  );
};
```

会看到h1显示为绿色


css-loader默认的哈希算法是[hash:base64]，这会将.title编译成._3zyde4l1yATCOkgn-DBWEL这样的字符串。

webpack.config.js里面可以定制哈希字符串的格式。


```javascript
module: {
  loaders: [
    // ...
    {
      test: /\.css$/,
      loader: "style-loader!css-loader?modules&localIdentName=[path][name]---[local]---[hash:base64:5]"
    },
  ]
}
```

运行这个示例。


$ npm run demo03

你会发现.title被编译成了demo03-components-App---title---GpMto。