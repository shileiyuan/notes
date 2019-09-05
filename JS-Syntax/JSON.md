Date对象序列化的时候会调用自己的`toJSON()`方法，返回的字符串格式为：**2017-07-20T11:30:16.658Z**, parse的时候为了还原成Date对象，要这样写：
```javascript
JSON.parse(JSON.stringify(obj), function(key, value){
  return typeof value === 'string' && /^\d{4}\-\d{2}\-\d{2}T\d{2}\:\d{2}\:\d{2}\.\d{3}Z$/.test(value) ? new Date(value) : value;
})
```



JSON格式化输出data：

```javascript
JSON.stringify(data, null, 2)
```

