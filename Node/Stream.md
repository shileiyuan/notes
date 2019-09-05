### pipe()

readableSrc.pipe(writableDest)

`pipe`方法是使用流最简便的方法。
通常我们通过管道或者事件的方法，使用流，但是要尽量避免两者混用。
通常当你使用`pipe`方法时，就不需要使用事件了。
但是当你需要更多定制操作的话，使用事件方式会更好。

除了从可读流读取数据传输到可写流，`pipe`方法还自动处理一些其他事情。比如处理错误，处理文件结束操作，流之间速度快慢问题。


### Stream流程
In any scenario where the data buffer has exceeded the highWaterMark or the write queue is currently busy, .write() will return false.

When a false value is returned, the backpressure system kicks in. It will pause the incoming Readable stream from sending any data and wait until the consumer is ready again. Once the data buffer is emptied, a .drain() event will be emitted and resume the incoming data flow.

Once the queue is finished, backpressure will allow data to be sent again. The space in memory that was being used will free itself up and prepare for the next batch of data.

**默认的highWaterMark是16kb**

### 用Stream来复制文件

```javascript
var fs = require('fs')

//Unlike the 16 kb default highWaterMark for a readable stream, the stream returned by this method has a default highWaterMark of 64 kb.
var rs = fs.createReadStream('/Users/ysl/Downloads/mysql-8.0.12-macos10.13-x86_64.dmg', { highWaterMark: 16 * 1024 }) 

// default highWaterMark 16 kb
var ws = fs.createWriteStream('mysql.jar')  

var start = new Date().getTime()
rs.on('data', function (chunk) {
  // console.log(chunk.length)
  var flag = ws.write(chunk)
  if (flag === false) {
    // console.log('pause')
    rs.pause()
  }
})
ws.on('drain', function () {
  // console.log('drain')
  rs.resume()
})

rs.on('end', function () {
  ws.end()
  var end = new Date().getTime()
  console.log('用时', end - start, 'ms')
})


// 使用pipe
// rs.pipe(ws)
// rs.on('end', function () {
//   var end = new Date().getTime()
//   console.log('用时', end - start, 'ms')
// })

```

默认情况下，当Readable stream的end事件触发的时候，Writable stream就会自动关闭，rs.pipe(ws, {end: false})会阻止这个默认动作。

**如果在流上设置了编码，那么会得到编码后的字符串而不是原始的Buffer作为事件参数。**