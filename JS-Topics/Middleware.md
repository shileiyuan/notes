```javascript
class Middleware {
  constructor() {
    this.middlewares = []
    this.cache = []
  }
  use(fn) {
    if (typeof fn !== 'function') {
      throw new Error('middleware must be a function');
    }
    this.middlewares.push(fn);
    return this;
  }
  next() {
    if(this.cache.length > 0) {
      const ware = this.cache.shift()
      ware.call(this, this.next.bind(this))
    }
  }

  handleRequest() {
    this.cache = this.middlewares.map(fn => fn)
    this.next()
  }
}


const middleware = new Middleware();
middleware.use(function(next){
  console.log(1);
  next();
  console.log('1 end');
})
middleware.use(function(next){
  console.log(2);
  next();
  console.log('2 end');
})
middleware.use(function(next){
  console.log(3);
  console.log('3 end');
})
middleware.use(function(next){
  console.log(4);
  next();
  console.log('4 end');
})
middleware.handleRequest()

```

