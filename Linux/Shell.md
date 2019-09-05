[TOC]

[bash-handbook](https://github.com/denysdovhan/bash-handbook/blob/master/translations/zh-CN/README.md)

## 脚本位置

`~/bin`目录存放个人脚本

`/usr/local/bin` 目录存放给系统上所有用户使用的脚本

`/usr/local/sbin ` 目录存放系统管理员使用的脚本

一般情况下，本地支持的软件，无论是脚本或者是编译好的程序，都应该放置在`/usr/local`目录下，而不是`/bin` 或者`/usr/bin`目录下。这些目录都是由Linux文件系统层次结构标准指定的，只能包含由Linux发行商所提供和维护的文件。



## 局部变量

局部变量是仅在某个脚本内部有效的变量。它们不能被其他的程序和脚本访问。

```bash
username="abc"  # 声明变量，等号两边不能有空格
echo $username  # 输出变量的值
unset username  # 删除变量

# 可以用 local 关键字声明属于某个函数的局部变量。这样声明的变量会在函数结束时消失

local local_var="I'm a local value"

```



## 环境变量

环境变量是对当前shell会话内所有的程序或脚本都可见的变量。创建它们跟创建局部变量类似，但使用的是`export`关键字。

```bash
export GLOBAL_VAR="I'm a global value"
```



常见的环境变量：

| Variable  | Description                                       |
| :-------- | :------------------------------------------------ |
| `$HOME`   | 当前用户的用户目录                                |
| `$PATH`   | 用分号分隔的目录列表，shell会到这些目录中查找命令 |
| `$PWD`    | 当前工作目录                                      |
| `$RANDOM` | 0到32767之间的整数                                |
| `$UID`    | 数值类型，当前用户的用户ID                        |
| `$PS1`    | 主要系统输入提示符                                |
| `$PS2`    | 次要系统输入提示符                                |

[这里](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_03_02.html#sect_03_02_04)有一张更全面的Bash环境变量列表。



## 位置参数

**位置参数** 是在调用一个函数并传给它参数时创建的变量。下表列出了在函数中，位置参数变量和一些其它的特殊变量以及它们的意义。

| Parameter      | Description                    |
| :------------- | :----------------------------- |
| `$0`           | 脚本名称                       |
| `$1 … $9`      | 第1个到第9个参数列表           |
| `${10} … ${N}` | 第10个到N个参数列表            |
| `$*` or `$@`   | 除了`$0`外的所有位置参数       |
| `$#`           | 不包括`$0`在内的位置参数的个数 |
| `$FUNCNAME`    | 函数名称（仅在函数内部有值）   |

在下面的例子中，位置参数为：`$0='./script.sh'`，`$1='foo'`，`$2='bar'`：

    ./script.sh foo bar

变量可以有 _默认_ 值。我们可以用如下语法来指定默认值：

```bash
 # 如果变量为空，赋给他们默认值
: ${VAR:='default'}
: ${$1:='first'}
# 或者
FOO=${FOO:-'default'}
```



## Shell 扩展

_扩展_ 发生在一行命令被分成一个个的 _记号（tokens）_ 之后。换言之，扩展是一种执行数学运算的机制，还可以用来保存命令的执行结果，等等。

感兴趣的话可以阅读[关于shell扩展的更多细节](https://www.gnu.org/software/bash/manual/bash.html#Shell-Expansions)。

### 大括号扩展

大括号扩展让生成任意的字符串成为可能。它跟 _文件名扩展_ 很类似，举个例子：

```bash
echo beg{i,a,u}n # begin began begun
```

大括号扩展还可以用来创建一个可被循环迭代的区间。

```bash
echo {0..5} # 0 1 2 3 4 5
echo {00..8..2} # 00 02 04 06 08
```

### 命令置换

命令置换允许我们对一个命令求值，并将其值置换到另一个命令或者变量赋值表达式中。当一个命令被``` `` ```或`$()`包围时，命令置换将会执行。举个例子：

```bash
now=`date +%T`
# or
now=$(date +%T)

echo $now # 19:08:26
```

### 算数扩展

在bash中，执行算数运算是非常方便的。算数表达式必须包在`$(( ))`中。算数扩展的格式为：

```bash
result=$(( ((10 + 5*3) - 7) / 2 ))
echo $result # 9
```

在算数表达式中，使用变量无需带上`$`前缀：

```bash
x=4
y=7
echo $(( x + y ))     # 11
echo $(( ++x + y++ )) # 12
echo $(( x + y ))     # 13
```



### 单引号和双引号

1. 在双引号中，变量引用或者命令置换是会被展开的。在单引号中是不会的。

```bash
echo "Your home: $HOME" # Your home: /Users/<username>
echo 'Your home: $HOME' # Your home: $HOME
```


2. 当局部变量或环境变量包含空格时，它们在引号中的扩展要格外注意：如果输入可能包含空格，务必要用引号把表达式包起来。

```bash
FILE="Favorite Things.txt"
cat $FILE   # 尝试输出两个文件: `Favorite` 和 `Things.txt`
cat "$FILE" # 输出一个文件: `Favorite Things.txt`
```



在双引号中，除了"$ \ `"(美元符号、反斜杠和反引号)之外，所有的特殊字符都会被看成普通字符。

这意味着单词分割、路径名扩展、波浪线扩展和花括号扩展都将失效，但是参数扩展、算数扩展和命令替换仍然生效。



单引号会抑制所有的扩展。



## 数组



### 数组声明

在bash中，可以通过简单地给数组变量的某个下标赋值来创建一个数组：

```bash
fruits[0]=Apple
fruits[1]=Pear
fruits[2]=Plum
```

数组变量也可以通过复合赋值的方式来创建，比如：

```bash
fruits=(Apple Pear Plum)
```



### 数组扩展

单个数组元素的扩展跟普通变量的扩展类似：

```bash
echo ${fruits[1]} # Pear
```

整个数组可以通过把数字下标换成`*`或`@`来扩展：

```bash
echo ${fruits[*]} # Apple Pear Plum
echo ${fruits[@]} # Apple Pear Plum
```

上面两行有很重要（也很微妙）的区别，假设某数组元素中包含空格：

```bash
fruits[0]=Apple
fruits[1]="Desert fig"
fruits[2]=Plum
```

为了将数组中每个元素单独一行输出，我们用内建的`printf`命令：

```bash
printf "+ %s\n" ${fruits[*]}
# + Apple
# + Desert
# + fig
# + Plum
```

为什么`Desert`和`fig`各占了一行？尝试用引号包起来：

```bash
printf "+ %s\n" "${fruits[*]}"
# + Apple Desert fig Plum
```

现在所有的元素都跑去了一行 —— 这不是我们想要的！为了解决这个痛点，`${fruits[@]}`闪亮登场：

```bash
printf "+ %s\n" "${fruits[@]}"
# + Apple
# + Desert fig
# + Plum
```

在引号内，`${fruits[@]}`将数组中的每个元素扩展为一个单独的参数；数组元素中的空格得以保留。



### 数组切片

可以通过切片运算符来取出数组中的某一片元素：

```bash
# ${fruits[@]}扩展为整个数组，:0:2取出了数组中从0开始，长度为2的元素
echo ${fruits[@]:0:2} # Apple Desert fig
```



### 向数组中添加元素

向数组中添加元素也非常简单。复合赋值在这里显得格外有用。

```bash
fruits=(Orange "${fruits[@]}" Banana Cherry)
echo ${fruits[@]} # Orange Apple Desert fig Plum Banana Cherry
```

上面的例子中，`${fruits[@]}`扩展为整个数组，并被置换到复合赋值语句中，接着，对数组`fruits`的赋值覆盖了它原来的值。



### 从数组中删除元素

用`unset`命令来从数组中删除一个元素：

```bash
unset fruits[0]
echo ${fruits[@]} # Apple Desert fig Plum Banana Cherry
```



## 流

Bash接收输入，并以字符序列或 **字符流** 的形式产生输出。这些流能被重定向到文件或另一个流中。

有三个文件描述符：

| 代码 | 描述符 | 描述               |
| :--: | :--------: | :------------------- |
| `0`  | `stdin`    | 标准输入       |
| `1`  | `stdout`   | 标准输出       |
| `2`  | `stderr`   | 标准错误输出   |

重定向让我们可以控制一个命令的输入来自哪里，输出结果到什么地方。这些运算符在控制流的重定向时会被用到：

| Operator | Description                                  |
| :------: | :------------------------------------------- |
| `>`      | 重定向输出                     |
| `&>`     | 重定向输出和错误输出            |
| `&>>`    | 以附加的形式重定向输出和错误输出 |
| `<`      | 重定向输入                     |
| `<<`     | [Here文档](http://tldp.org/LDP/abs/html/here-docs.html) 语法 |
| `<<<`    | [Here字符串](http://www.tldp.org/LDP/abs/html/x17837.html) |

以下是一些使用重定向的例子：

```bash
# ls的结果将会被写到list.txt中
ls -l > list.txt

# 将输出附加到list.txt中
ls -a >> list.txt

# 所有的错误信息会被写到errors.txt中
grep da * 2> errors.txt

# 从errors.txt中读取输入
less < errors.txt
```



## 管道

我们不仅能将流重定向到文件中，还能重定向到其它程序中。**管道** 允许我们把一个程序的输出当做另一个程序的输入。

在下面的例子中，`command1`把它的输出发送给了`command2`，然后输出被传递到`command3`：

```bash
command1 | command2 | command3

ls -l | grep .md$ | less # 打印出扩展名是.md的文件
```



## 命令序列

命令序列是由`;`，`&`，`&&`或者`||`运算符分隔的一个或多个管道序列。

如果一个命令以`&`结尾，shell将会在一个子shell中异步执行这个命令。换句话说，这个命令将会在后台执行。

以`;`分隔的命令将会依次执行：一个接着一个。shell会等待直到每个命令执行完。

```bash
# command2 会在 command1 之后执行
command1 ; command2

# 等同于这种写法
command1
command2
```

以`&&`和`||`分隔的命令分别叫做 _与_ 和 _或_ 序列。

_与序列_ 看起来是这样的：

```bash
# 当且仅当command1执行成功（返回0值）时，command2才会执行
command1 && command2
```

_或序列_ 是下面这种形式：

```bash
# 当且仅当command1执行失败（返回错误码）时，command2才会执行
command1 || command2
```

_与_ 或 _或_ 序列的返回值是序列中最后一个执行的命令的返回值。



## 条件语句

### test命令

test命令会执行各种检查和比较，返回true或false，通常和if一起使用。该命令有两种等价模式：

```bash
test expression

[ expression ]
```



### 文件表达式

| 基元       | 含义                                                      |
| :-----------: | :----------------------------------------------------------- |
| `[ -e FILE ]` | 如果`FILE`存在 (**e**xists)，为真                             |
| `[ -f FILE ]` | 如果`FILE`存在且为一个普通文件（**f**ile），为真                |
| `[ -d FILE ]` | 如果`FILE`存在且为一个目录（**d**irectory），为真               |
| `[ -s FILE ]` | 如果`FILE`存在且非空（**s**ize 大于0），为真                    |
| `[ -r FILE ]` | 如果`FILE`存在且有读权限（**r**eadable），为真                  |
| `[ -w FILE ]` | 如果`FILE`存在且有写权限（**w**ritable），为真                  |
| `[ -x FILE ]` | 如果`FILE`存在且有可执行权限（e**x**ecutable），为真            |
| `[ -L FILE ]` | 如果`FILE`存在且为一个符号链接（**l**ink），为真                |
| `[ FILE1 -nt FILE2 ]` | `FILE1`比`FILE2`新（**n**ewer **t**han）                  |
| `[ FILE1 -ot FILE2 ]` | `FILE1`比`FILE2`旧（**o**lder **t**han）                  |

### 字符串表达式

| 基元        | 含义                                                     |
| :------------: | :---------------------------------------------------------- |
| `[ -z STR ]`   | `STR`为空（长度为0，**z**ero）                               |
| `[ -n STR ]`   | `STR`非空（长度非0，**n**on-zero）                           |
| `[ STR1 == STR2 ]` | `STR1`和`STR2`相等                                      |
| `[ STR1 != STR2 ]` | `STR1`和`STR2`不等                                      |
| `[ STR1 > STR2 ]` | 在排序时，STR1在STR2之后 |

### 整数表达式

| 基元             | 含义                                                |
| :-----------------: | :----------------------------------------------------- |
| `[ ARG1 -eq ARG2 ]` | `ARG1`和`ARG2`相等（**eq**ual）                         |
| `[ ARG1 -ne ARG2 ]` | `ARG1`和`ARG2`不等（**n**ot **e**qual）                 |
| `[ ARG1 -lt ARG2 ]` | `ARG1`小于`ARG2`（**l**ess **t**han）                   |
| `[ ARG1 -le ARG2 ]` | `ARG1`小于等于`ARG2`（**l**ess than or **e**qual）      |
| `[ ARG1 -gt ARG2 ]` | `ARG1`大于`ARG2`（**g**reater **t**han）                |
| `[ ARG1 -ge ARG2 ]` | `ARG1`大于等于`ARG2`（**g**reater than or **e**qual）   |



### 更现代的test命令版本

```bash
# 相当于增强的test命令
[[ expression ]]
```

**新增的字符串表达式：** `string1=~regex` 

如果string1与扩展的正则表达式regex匹配，则返回true

```bash
#!/bin/bash

INT=-5

if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
  if [ $INT -eq 0 ]; then
    echo "INT is zero"
  else
    if [ $INT -lt 0 ]; then
      echo "INT is negative"
    else
      echo "INT is positive"
    fi

    if [ $((INT % 2)) -eq 0 ]; then
      echo "INT is even"
    else
      echo "INT is odd"
    fi
  fi
else
  echo "INT is not an integer." >&2
  exit 1
fi
```

**[[]]增加的另外一个特性是==操作符支持模式匹配**

```bash
#!/bin/bash
file=foo.bar
if [[ $file == foo.* ]]; then
	echo true
else echo false
fi
```

**除了[[]]之外，bash同样提供了(())命令，它可用于操作整数**

(())用于执行算数真值测试，当算数计算的结果是非零值时，返回true。

注意这里使用了大于号、小于号和等号用来测试相等性。在处理整数时，这些语法看起来更加自然。另外，由于(())符合命令是shell语法的一部分，而不是普通的命令，并且只能处理整数，所以它能够通过名字来识别变量，而且不需要执行扩展操作。

```bash
#!/bin/bash
x=5
if (( x < 30 )); then
  echo equals
else
  echo not
fi
```



### 组合表达式

| Operation | test | [[]] 和 (()) |
| --------- | ---- | ------------ |
| AND       | -a   | &&           |
| OR        | -o   | \|\|         |
| NOT       | !    | !            |



| Operation      | Effect                                                      |
| :------------: | :---------------------------------------------------------- |
| `[ ! EXPR ]`   | 如果`EXPR`为假，为真                                         |
| `[ (EXPR) ]`   | 返回`EXPR`的值                                               |
| `[ EXPR1 -a EXPR2 ]` | 逻辑 _与_， 如果`EXPR1`和（**a**nd）`EXPR2`都为真，为真  |
| `[ EXPR1 -o EXPR2 ]` | 逻辑 _或_， 如果`EXPR1`或（**o**r）`EXPR2`为真，为真     |



### 另一种方式的分支

```bash
#!/bin/bash

# 先检测temp目录是否存在，只有当检测失败时，才会创建这个目录
[ -d temp ] || mkdir temp
```





### 使用case

每种情况都是匹配了某个模式的表达式。`|`用来分割多个模式，`)`用来结束一个模式序列。第一个匹配上的模式对应的命令将会被执行。`*`代表任何不匹配以上给定模式的模式。命令块儿之间要用`;;`分隔。

```bash
case "$extension" in
  "jpg"|"jpeg")
    echo "It's image with jpeg extension."
  ;;
  "png")
    echo "It's image with png extension."
  ;;
  "gif")
    echo "Oh, it's a giphy!"
  ;;
  *)
    echo "Woops! It's not image!"
  ;;
esac
```



## 循环

Bash中有四种循环：`for`，`while`，`until`和`select`。

## for 循环

`for`与它在C语言中的姊妹非常像。看起来是这样：

```bash
for arg in elem1 elem2 ... elemN
do
  # 语句
done
```

在每次循环的过程中，`arg`依次被赋值为从`elem1`到`elemN`。这些值还可以是通配符或者[大括号扩展](#大括号扩展)。

当然，我们还可以把`for`循环写在一行，但这要求`do`之前要有一个分号，就像下面这样：

```bash
for i in {1..5}; do echo $i; done
```

还有，如果你觉得`for..in..do`对你来说有点奇怪，那么你也可以像C语言那样使用`for`，比如：

```bash
for (( i = 0; i < 10; i++ )); do
  echo $i
done
```

当我们想对一个目录下的所有文件做同样的操作时，`for`就很方便了。举个例子，如果我们想把所有的`.bash`文件移动到`script`文件夹中，并给它们可执行权限，我们的脚本可以这样写：

```bash
#!/bin/bash

for FILE in $HOME/*.bash; do
  mv "$FILE" "${HOME}/scripts"
  chmod +x "${HOME}/scripts/${FILE}"
done
```



### while 循环

`while`循环检测一个条件，只要这个条件为 _真_，就执行一段命令。被检测的条件跟`if..then`中使用的[基元](#基元和组合表达式)并无二异。因此一个`while`循环看起来会是这样：

```bash
while [[ condition ]]
do
  # 语句
done
```

跟`for`循环一样，如果我们把`do`和被检测的条件写到一行，那么必须要在`do`之前加一个分号。

比如下面这个例子：

```bash
#!/bin/bash
# 0到9之间每个数的平方
x=0
while [[ $x -lt 10 ]]; do # x小于10
  echo $(( x * x ))
  x=$(( x + 1 )) # x加1
done
```

### until 循环

`until`循环跟`while`循环正好相反。它跟`while`一样也需要检测一个测试条件，但不同的是，只要该条件为 _假_ 就一直执行循环：

```bash
until [[ condition ]]; do
  # 语句
done
```



### select 循环

`select`循环帮助我们组织一个用户菜单。它的语法几乎跟`for`循环一致：

```bash
select answer in elem1 elem2 ... elemN
do
  # 语句
done
```

`select`会打印`elem1..elemN`以及它们的序列号到屏幕上，之后会提示用户输入。通常看到的是`$?`（`PS3`变量）。用户的选择结果会被保存到`answer`中。如果`answer`是一个在`1..N`之间的数字，那么`语句`会被执行，紧接着会进行下一次迭代 —— 如果不想这样的话我们可以使用`break`语句。

一个可能的实例可能会是这样：

```bash
#!/bin/bash

PS3="Choose the package manager: "
select ITEM in bower npm gem pip
do
  echo -n "Enter the package name: " && read PACKAGE
  case $ITEM in
    bower) bower install $PACKAGE ;;
    npm)   npm   install $PACKAGE ;;
    gem)   gem   install $PACKAGE ;;
    pip)   pip   install $PACKAGE ;;
  esac
  break # 避免无限循环
done
```

这个例子，先询问用户他想使用什么包管理器。接着，又询问了想安装什么包，最后执行安装操作。

运行这个脚本，会得到如下输出：

```
$ ./my_script
1) bower
2) npm
3) gem
4) pip
Choose the package manager: 2
Enter the package name: bash-handbook
<installing bash-handbook>
```



### 循环控制

可以使用 `break` 和 `continue` 



## 函数

1. 函数声明必须在函数调用之前
2. 写下函数名字，函数就会被调用
3. 函数参数使用位置参数

```bash
greeting () {
	if [[ -n $1 ]]; then
    echo "Hello, $1!"
  else
    echo "Hello, unkown!"
  fi
  return 0
}

greeting Denys  # Hello, Denys!
greeting        # Hello, unknown!

```




































