React循环生成一个list的时候，如果用index作为key，会导致两个问题：



1. 性能一定会下降

2. 如果子组件的状态不完全是由父组件传入的props决定的，比如子组件有自己的state或者子组件是一个uncontrolled component，当列表的某一项被删除的时候，就会导致数据错乱。





所以最佳实践还是永远不要用index作为key，如果后端没有返回key，前端可以自己生成一个uuid来作为key



用index作为key，则增加或删除列表项的时候，key其实是会变的，

如果列表项的所有值都是通过props传进来的，也就是都是可变的话，那不会出现bug

一旦列表项的某个值不是通过props传进来的，那这个值就会出现错乱

