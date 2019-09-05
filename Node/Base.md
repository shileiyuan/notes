### 简介

[官网地址](https://nodejs.org/en/)

Node.js is a JavaScript runtime built on Chrome's V8 JavaScript engine. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient. Node.js' package ecosystem, npm, is the largest ecosystem of open source libraries in the world.



### 打开浏览器

```javascript
var c = require('child_process');
c.exec('start http://www.baidu.com') //打开默认浏览器
c.exec("C:\\Users\\jslouvre\\AppData\\Local\\Google\\Chrome\\Application\\chrome.exe http://www.cnblogs.com/rubylouvre/");  //打开任意浏览器
```



### Tips

1. node中， 最外层的this === module.exports




### process

process.argv的第一个元素是系统中node的路径，第二个元素是正在执行的js文件的路径，剩下的参数是命令行中传进来的参数
```javascript
process.argv.forEach((val, index) => {
  console.log(`${index}: ${val}`);
});

//node process.js one two=three four
```

```javascript
// 设置编码，否则读进来的是buffer
process.stdin.setEncoding('utf8');  
process.stdin.on('readable', () => {
  const chunk = process.stdin.read();
  if (chunk !== null) {
    process.stdout.write(`data: ${chunk}`);
  }
});
process.stdin.on('end', () => {
  process.stdout.write('end');
});
```




### StringDecoder

```javascript
// 不会显示不完整的字符
const StringDecoder = require('string_decoder').StringDecoder;
const decoder = new StringDecoder('utf8');

console.log(decoder.write(Buffer.from([0xE2])))
console.log(decoder.write(Buffer.from([0x82])))
console.log(decoder.end(Buffer.from([0xAC])));
```



### readline

```javascript
const readline = require('readline');

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

rl.question('What do you think of Node.js? ', (answer) => {
  // TODO: Log the answer in a database
  console.log(`Thank you for your valuable feedback: ${answer}`);

  rl.close();
});
```

```javascript
// Tiny CLI
const readline = require('readline');
const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
  prompt: 'OHAI> '  // prompt是命令行前面的那个提示符
});

rl.prompt();

rl.on('line', (line) => {
  switch (line.trim()) {
    case 'hello':
      console.log('world!');
      break;
    default:
      console.log(`Say what? I might have heard '${line.trim()}'`);
      break;
  }
  rl.prompt();
}).on('close', () => {
  console.log('Have a great day!');
  process.exit(0);
});

//按Ctrl+C可以退出
```

```javascript
// read file-stream line-by-line
const readline = require('readline');
const fs = require('fs');

const rl = readline.createInterface({
  input: fs.createReadStream('sample.txt')
});

rl.on('line', (line) => {
  console.log(`Line from file: ${line}`);
});
```
