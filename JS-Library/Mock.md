```javascript
mockRequest.onGet(API.query_userList).reply(200, Mock.mock({
  'status': 0,
  'data|20-50': [{
    'id': '@guid',
    'name': '@cname',
    'age|1-100': 1
  }],
  'total': function() {
    return this.data.length
  }
}))
```

