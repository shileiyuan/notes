### 对象

**增：**
assoc/assocPath: 浅复制对象，然后设置或覆盖对象的指定属性
objOf：创建一个包含单个键值对的对象
zipObj: 将两个列表对应位置的元素作为键值对组合，生成一个新的键值对的列表。生成的列表的长度取决于较短的输入列表的长度。

**删：**
dissoc/dissocPath: 浅复制对象，删除返回对象中指定路径上的属性
omit：删除对象中给定的keys对应的属性
reject: 删除满足predicate的属性，是filter的补操作

**改：**
lens
evolve:

**查：**
prop/path: 取出对象中指定属性的值。如果不存在，则返回 undefined。
propOr/pathOr: 如果不存在可以指定默认值的 `R.pathOr([], ['USER_INFO', 'products'], window)`
props: 取出对象中，keys对应的值的数组
pick：返回对象的部分拷贝，其中仅包含指定键对应的属性。如果某个键不存在，则忽略该属性。
pickAll: 与pick类似，但pickAll会将不存在的属性以key:undefined形式返回
pickBy: 返回部分对象的拷贝，其中仅包含（val, key）满足predicate的属性
filter: pickBy的回调函数的参数是(value,key)，filter的回调函数的参数是value
kyes/keysIn: list all the keys
values/valuesIn: list all the values

**判断：**
has/hasIn
propEq/pathEq
propSatisfies/pathSatisfies
where
whereEq
eqProps: 判断连个对象指定的属性值是否相等

**其他：**
toPairs/toPairsIn: 将对象的属性转换成键、值二元组类型的数组
clone: 深复制
merge：合并两个对象的属性（不包括prototype属性）。如果某个key在两个对象中都存在，使用后一个对象相应的属性值。
mergeAll：将对象类型列表合并为一个对象。
mergeDeepLeft/mergeDeepRight

### 数组
**增：**
prepend： 在头部加一个元素
append：在尾部加一个元素
insert：将一个元素插入到指定索引处
insertAll: 将一个list插入到指定索引处

**删：**
init：删掉列表的最后一个元素，返回其他的元素
without: 返回第二个集合减去第一个集合的元素的差集
uniq：删掉重复的元素
uniqBy: 传一个函数来操作数据，根据函数的返回值进行相等行判断
uniqWith: 传一个函数来进行判断，该函数返回true或false
remove： 删除列表中从start开始的count个元素。类似splice
drop：删除前n个元素
dropWhile：对 list 从前向后删除满足 predicate 的头部元素，直到遇到第一个 falsy 值。

**改：**
update: 替换数组中指定索引处的值
adjust: 将数组中指定索引处的值替换为经函数变换的值。

**查：**
pluck: 从列表内的每个对象元素中取出特定的名称，组成一个新的列表。
filter：筛选出满足条件的元素列表
reject：filter的补操作
slice：有两个number类型的参数
take：返回列表的前n个元素
takeWhile：跟filter的区别就是遇到了不满足条件的元素，就会终止执行
head
last
nth
find：查找并返回 list 中首个满足 predicate 的元素；如果未找到满足条件的元素，则返回 undefined
indexOf
lastIndexOf
findIndex
findLastIndex
length
intersection: 取出两个list中相同的元素则称的set（集合：没有重复元素）intersection: 取出两个list中相同的元素则称的set（集合：没有重复元素）
intersectionWith
difference: 求差集。求第一个列表中，未包含在第二个列表中的元素的集合。对象和数组比较数值相等，而非引用相等。
union: 集合并运算
unionWith
zip: 将两个列表对应位置的元素组合成一个新的列表。生成的列表长度取决于较短的输入列表的长度。
zipWith: 将两个列表对应位置的元素通过一个函数处理，生成一个新的元素的列表

**循环：**
map
reduce: 左折叠操作，里面的函数的第一个参数是累积值，第二个参数是从数组中取出的数据

**判断：**
contains
all
any
none

**数学操作：**
sum： 数组元素求和
product： 数组元素求乘积
mean：求平均值
median: 把数组元素排序，然后取中间那个值

**其他：**
addIndex：给循环加上index
groupBy：将列表根据一定的规则分组，并将其存储在一个对象中
range： R.range(1, 5); //=> [1, 2, 3, 4]
splitWhen：查找列表中首个满足 predicate 的元素，在该处将列表拆分为两部分。
reverse
sort
concat
indexBy: 通过生成键的函数，将元素为对象的 list 转换为以生成的键为索引的新对象。

### 其他
R.unapply
R.invoker