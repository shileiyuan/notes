### 基本类型

1. 布尔值： let isDone: boolean = false;
2. 数字： let decLiteral: number = 6;
3. 字符串： let name: string = "bob";
4. 数组： let list: number[] = [1, 2, 3];      let list: Array\<number\> = [1, 2, 3];
5. 元组： let x: [string, number] = ['hello', 10]
6. 枚举

```javascript
enum Color {Red, Green, Blue}
let c: Color = Color.Green;

// 默认情况下，从0开始为元素编号，也可以手动指定
enum Color {Red = 1, Green, Blue}

// 或者全部采用手动赋值
enum Color {Red = 1, Green = 2, Blue = 4}

// 可以根据枚举的值得到它的名字
enum Color {Red = 1, Green, Blue}
let colorName: string = Color[2];

alert(colorName);  // 显示'Green'因为上面代码里它的值是2
```
7. any

```javascript
let notSure: any = 4;
notSure.ifItExists(); // okay, ifItExists might exist at runtime
notSure.toFixed(); // okay, toFixed exists (but the compiler doesn't check)

let list: any[] = [1, true, "free"];
list[1] = 100;
```
8. void: void类型像是与any类型相反，它表示没有任何类型。当一个函数没有返回值时，你通常会见到其返回值类型是 void

```javascript
function warnUser(): void {
  alert("This is my warning message");
}

```
声明一个void类型的变量没有什么大用，因为你只能为它赋予undefined和null

9. null和undefined： 和 void相似，它们的本身的类型用处不是很大

```javascript
// Not much else we can assign to these variables!
let u: undefined = undefined;
let n: null = null;
```
never

### let
当let出现在循环体中的时候，会针对每次循环创建一个新的作用域。

```javascript
for (let i = 0; i < 10 ; i++) {
  setTimeout(function() {console.log(i); }, 100 * i);
}

for(var i = 0; i < 10; i++) {
  (function(i){
    setTimeout(function() {console.log(i); }, 100 * i);
  }(i))
}
```

### 解构
```javascript
// 创建剩余变量
const obj = {
  name: 'Danny',
  age: 10,
  gender: true
}
const {name, ...rest} = obj

// 给变量重命名并赋默认值
const { x: y = 3 } = { x: 5 };
console.log(y)

// 给属性重命名
let { a: newName1, b: newName2 } = o;

// 这里的冒号不是指示类型的。 如果你想指定它的类型， 仍然需要在其后写上完整的模式。
let {a, b}: {a: string, b: number} = o;
```

### 接口

#### 可选属性
可以对可能存在的属性进行预定义
允许interface中的属性多，传入的参数的属性少
```javascript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  let newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

let mySquare = createSquare({color: "black"});
```

#### 额外的属性检查

当将对象字面量赋值给变量或作为参数传递的时候，会被特殊对待而且会经过额外的参数检查。

对于对象字面量来说，一般是不允许传入的参数比interface中多的
但是可以有三种方法绕过这个检查
```javascript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
  // ...
}

// name没有在接口中定义，会报错
let mySquare = createSquare({ width: 100, name: 'abc' });

//使用类型断言绕过检查
let mySquare = createSquare({ width: 100, name: 'abc' } as SquareConfig);

// 最佳的方式是能够添加一个字符串索引签名，前提是你能够确定这个对象可能具有某些做为特殊用途使用的额外属性
interface SquareConfig {
  color?: string;
  width?: number;
  [propName: string]: any;
}

// 将这个对象字面量赋值给另一个变量，再传给函数，也可以绕过检查
let squareOptions = { width: 100, name: 'abc' };
let mySquare = createSquare(squareOptions);
```

### 只读属性
一些对象属性只能在对象刚刚创建的时候修改其值。 你可以在属性名前用 readonly来指定只读属性
```javascript
interface Point {
  readonly x: number;
  readonly y: number;
}
```

### 函数类型
```javascript
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch: SearchFunc = function (source, subString) {
  let result = source.search(subString);
  return result > -1;
}
```

### 可索引的类型
```javascript
interface ReadonlyStringArray {
  readonly [index: number]: string; // 指明了以number类型当索引得到的值必须是string类型
}
let myArray: ReadonlyStringArray = ["Alice", "Bob"];
myArray[2] = "Mallory"; // error!
```

### 类类型

```typescript
interface ClockConstructor {
  new (hour: number, minute: number);
}

interface ClockInterface {
  tick();
}

const Clock: ClockConstructor = class Clock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {
      console.log("beep beep");
  }
}
```





## 函数



```typescript
// 可选参数
function buildName(firstName: string, lastName?: string) {
    // ...
}

// 默认参数
function buildName(firstName: string, lastName = "Smith") {
    // ...
}

// 剩余参数
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}
```



## 泛型

```typescript
// 定义泛型函数
function identity<T>(arg: T): T {
    return arg;
}

// 使用泛型函数
let output = identity<string>("myString");  // type of output will be 'string'

// 编译器会根据参数自动进行类型推断
let output = identity("myString");  // type of output will be 'string'
```



```typescript
function loggingIdentity<T>(arg: T[]): T[] {
    console.log(arg.length);  // Array has a .length, so no more error
    return arg;
}
```















































