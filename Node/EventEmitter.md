```javascript
const EventEmitter = require('events');
class MyEmitter extends EventEmitter { }

const myEmitter = new MyEmitter();
myEmitter.on('event', () => {
  console.log('触发了一个事件！');
});
myEmitter.emit('event');
```
注册新事件时会触发'newListener'事件， 删除事件时会触发'removeListener'事件。

