```javascript
 // 返回启动node进程时的路径
process.cwd() 

// 返回当前执行的module所在的目录，是绝对路径
__dirname

__filename

// 在一个项目中，dirname比较好使
const DOC_PATH = path.resolve(dirname, '../../doc')

convert_await(path.resolve(DOC_PATH, 'abc'), path.resolve(DOC_PATH, 'def'))

```

