### 简介
1. Buffer将JS的数据处理能力从字符串扩展到了任意二进制数据
2. Buffer的大小是固定的，在被创建时确定，且无法调整。
3. Buffer在Node中是一个全局变量，所以无需引用即可使用。

Buffer在文件IO和网络IO中运用广泛。在应用中，我们通常会操作字符串，但一旦在网络中传输，都需要转换成Buffer，以进行二进制数据传输。通过预先转换静态内容为Buffer对象，可以有效地减少CPU的重复使用，节省服务器资源。在Node构建的web应用中，可以选择将动态内容和静态内容分离，静态内容部分可以通过预先转换为Buffer的方式，使性能得到提升。

### API


1. `Buffer.from(array)` returns a new Buffer that contains a copy of the provided octets.

2. `Buffer.from(arrayBuffer[, byteOffset[, length]])` returns a new Buffer that shares the same allocated memory as the given ArrayBuffer.

3. `Buffer.from(buffer)` returns a new Buffer that contains a copy of the contents of the given Buffer.

4. `Buffer.from(string[, encoding])` returns a new Buffer that contains a copy of the provided string.

5. `Buffer.alloc(size[, fill[, encoding]])`returns a new initialized Buffer of the specified size. This method is slower than Buffer.allocUnsafe(size) but guarantees that newly created Buffer instances never contain old data that is potentially sensitive.

6. `Buffer.allocUnsafe(size)` and `Buffer.allocUnsafeSlow(size)` each return a new uninitialized Buffer of the specified size. Because the Buffer is uninitialized, the allocated segment of memory might contain old data that is potentially sensitive.

### Node目前支持的编码：
1. 'ascii' - 仅支持 7 位 ASCII 数据。如果设置去掉高位的话，这种编码是非常快的。
2. 'utf8' - 多字节编码的 Unicode 字符。许多网页和其他文档格式都使用 UTF-8 。
3. 'utf16le' - 2 或 4 个字节，小字节序编码的 Unicode 字符。支持代理对（U+10000 至 U+10FFFF）。
4. 'ucs2' - 'utf16le' 的别名。
5. 'base64' - Base64 编码。当从字符串创建 Buffer 时，按照 RFC4648 第 5 章的规定，这种编码也将正确地接受“URL 与文件名安全字母表”。
6. 'latin1' - 一种把 Buffer 编码成一字节编码的字符串的方式（由 IANA 定义在 RFC1345 第 63 页，用作 Latin-1 补充块与 C0/C1 控制码）。
7. 'binary' - 'latin1' 的别名。
8. 'hex' - 将每个字节编码为两个十六进制字符。



### 例子

```javascript
// 读取一个图片，将其用base64编码存储在文件中，然后读取该文件，还原该图片
const fs = require('fs');

fs.readFile('a.jpg', (err, data) => {
  // 此处的data是Buffer类型的
  fs.writeFile('a.txt', data.toString('base64'))
})

fs.readFile('a.txt', (err, data) => {
  // fs.writeFile('b.jpg', data.toString(), 'base64');
  const buf = Buffer.from(data.toString(), 'base64');
  fs.writeFile('b.jpg', buf)
})
```
















