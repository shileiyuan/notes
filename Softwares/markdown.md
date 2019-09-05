在其他的markdown规范中，如果要换行，需要在第一行的结尾敲**两个空格**，再敲回车，但是在这里不需要

# 标题
## H2
### H3 ###

分割线 
***
> 这是引用
> 继续引用

###无序列表
* a
* b
* ca

###有序列表
1. a
2. b
3. c

```javascript
console.log('abc')
```

### 强调
**haha**

###斜体
*haha*

`一小段代码`
``There is a literal backtick (`) here.``

### 任务列表
- [ ] a task list item
- [ ] list syntax required
- [ ] normal **formatting**, @mentions, #1234 refs
- [ ] incomplete
- [x] completed

### 表格
| id   | name |  age |
| ---- | :--: | ---: |
| co1  | col2 | col3 |

### 链接
[百度](https://www.baidu.com 'title')

**id在下面定义**
This is [an example][id] reference-style link.

**id和链接文字一样的情况可以省略**
[Google][]

[id]: http://example.com/  "Optional Title Here"
[Google]: (https://www.google.com)

### 图片
![没有图片](quiver-image-url/1BC53D7D9951ECAFC6CE6CDD5631C40D.jpg =100x1440)