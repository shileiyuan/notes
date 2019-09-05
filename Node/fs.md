### access
Using fs.access() to check for the accessibility of a file before calling fs.open(), fs.readFile() or fs.writeFile() is not recommended. Doing so introduces a race condition, since other processes may change the file's state between the two calls. Instead, user code should open/read/write the file directly and handle the error raised if the file is not accessible.

### 创建目录 mkdir
```javascript
fs.mkdir('/tmp/a/apple', { recursive: true }, (err) => {
  if (err) throw err;
});
```
如果目录已存在，会报错

### 删除目录
fs.rmdir(path, callback)

### 删除文件
```javascript
fs.unlink('path/file.txt', (err) => {
  if (err) throw err;
  console.log('path/file.txt was deleted');
});
```
### 文件重命名
```javascript
fs.rename('oldFile.txt', 'newFile.txt', (err) => {
  if (err) throw err;
  console.log('Rename complete!');
});
```

### 获取文件状态
```javascript
var fs = require('fs');

var getTimeDesc = function (d) {
  return [d.getFullYear(), d.getMonth() + 1, d.getDate()].join('-') + ' ' + [d.getHours(), d.getMinutes(), d.getSeconds()].join(':');
};

fs.stat('./abc.txt', function (err, stats) {
  if(err) throw err
  console.log('是否文件: ' + stats.isFile());
  console.log('是否目录: ' + stats.isDirectory());
  console.log('文件大小: ' + stats.size);
  console.log('创建时间: ' + getTimeDesc(stats.birthtime));
  console.log('访问时间: ' + getTimeDesc(stats.atime));
  console.log('修改时间: ' + getTimeDesc(stats.mtime));
});
```


### 追加文件内容
```javascript
fs.appendFile('abc.txt', 'data to append', (err) => {
  if (err) throw err;
console.log('The "data to append" was appended to file!');
});
```


### 监控文件的变化
```javascript
var fs = require('fs')
fs.watch('./abc.txt', (eventType, fileName) => {
  console.log(eventType, fileName)
})
```


### 文件拷贝
```javascript
var fs = require('fs');
function copy(src, dst) {
  // fs.writeFileSync(dst, fs.readFileSync(src));  小文件拷贝
  // fs.createReadStream(src).pipe(fs.createWriteStream(dst)) // 大文件拷贝
}
function main(argv) {
  copy(argv[0], argv[1]);
}

main(process.argv.slice(2));
```
process是一个全局变量，可通过process.argv获得命令行参数。由于argv[0]固定等于NodeJS执行程序的绝对路径，argv[1]固定等于主模块的绝对路径，因此第一个命令行参数从argv[2]这个位置开始。


### fs.open

flag: 
r：以读取模式 打开文件，如果文件不存在，就报错
w： 以写入模式打开文件，如果文件不存在就创建一个（默认的写入模式是覆盖原来的内容）
a：相对于w，以追加模式打开文件，如果文件不存在就创建一个
r+/w+/a+：以读、写or追加 模式打开文件，其他表现和r/w/a相同
wx/ax：如果文件不存在就报错，其他表现同w/a相同



### 例子

```javascript
// 转换文件编码
const fs = require('fs')
const path = require('path')
const iconv = require('iconv-lite')
const chardet = require('chardet')
const util = require('util')

const { promisify } = util

// 自己写的promisify
const p = api => (...args) => new Promise((resolve, reject) => api(...args, (err, data) => err ? reject(err) : resolve(data)))

const [readdir, mkdir, stat, readFile, writeFile] = ['readdir', 'mkdir', 'stat', 'readFile', 'writeFile'].map(name => p(fs[name]))

const detectFile = p(chardet.detectFile)

// 将GBK编码转换成utf-8编码
const convert = function (from, to) {
  fs.readdir(from, (err, files) => {
    fs.mkdir(to, err => {
      console.log(`目录${to}创建成功`)
      files.forEach(file => {
        const from2 = path.join(from, file)
        const to2 = path.join(to, file)
        fs.stat(from2, (err, stat) => {
          if (stat.isDirectory()) {
            convert(from2, to2)
          } else {
            fs.readFile(from2, (err, data) => {
              chardet.detectFile(from2, (err, encoding) => {
                if (encoding === 'Big5' || encoding === 'GB18030') {
                  data = iconv.decode(data, 'gbk')
                }
                fs.writeFile(to2, data, err => {
                  console.log(`文件${to2}写入成功`)
                })
              })
            })
          }
        })
      })
    })
  })
}

// async await 版本
const convert_await = async function (from, to) {
  const files = await readdir(from)
  await mkdir(to)
  files.forEach(async file => {
    const from2 = path.join(from, file)
    const to2 = path.join(to, file)
    const status = await stat(from2)
    if (status.isDirectory()) {
      convert_await(from2, to2)
    } else {
      let data = await readFile(from2)
      const encoding = await detectFile(from2)
      if (encoding === 'Big5' || encoding === 'GB18030') {
        data = iconv.decode(data, 'gbk')
      }
      fs.writeFile(to2, data, err => {
        console.log(`文件${to2}写入成功`)
      })
    }
  })
}

convert_await('./abc', './def')


```

