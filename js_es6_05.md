## ES6学习笔记05

### Class

```js
class Point {
  constructor(x, y) {
    this.x = x
    this.y = y
  }
  
  toString () {//等于Point.prototype.toString = ....
    return `(${this.x}, ${this.y}`
  }
}
typeof Point //'function'
Point = Point.prototype.cunstructor //true

var p = new Point(1, 2)
p.toString() //'(1, 2)'
```

上面代码表明，类的数据类型就是函数，类本身就指向构造函数。

声明的toString方法，相当于在prototype原型是添加方法。

**类内部所有定义的方法，都是不可枚举的**

```js
//类的构造器必须跟着new
var point = Point(2, 3) //报错
//正确
var point = new Point(2, 3)
```

实例的属性，除非显示地定义在其本身(即this上)，否则都是定义在原型上

```js
class Point {
  constructor (x, y) {
    this.x = x
    this.y = y
  }
  toString () {
    return `(${this.x}, ${this.y}`
  }
}
var point = new Point(2, 3)
point.toString() // (2, 3)

point.hasOwnProperty('x') //true
point.hasOwnProperty('y') //true
point.hasOwnProperty('toString') //false
proint.__proto__.hasOwnProperty('toString')//true
```

**class表达式**

```js
const MyClass = class Me {
	getClassName() {
    return Me.name;
  }
}
```

**继承**

```js
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y) // 调用父类的constructor
    this.color = color
  }
  toString() {
    return this.color + ' ' + super.toString() //调用父类的toString()
  }
}
```

constructor方法中必须调用父类的super方法，否则新建实例时会报错。

### Module

```js
export var firstName = 'Michale'
export var lastName = 'Jackson'
export var year = 1958
//等同于
var firstName = 'Michale'
var lastName = 'Jackson'
var year = 1958

export {firstName, lastName, year}
```

**可以用as关键字修改export的变量名**

```js
export {
	fristName as f,
  lastName as l,
  year as y,
  year as r //同一个变量，可以有多个变量名
}
```

```js
//错误写法
export 1
var m = 1
export m //等同于 export 1
//正确写法
export { m }
export { m as n }
```

**import指令**

```js
import {firstName, lastName, year} from './profile'
//or
import { lastName as surname } from './profile'
```

**模块的整体加载**

```js
import * as all from './profile'

all.firstName
all.lastName
all.year
```

**export default**

```js
export default function () {
}
//import可以为其制定任意名
import customName from './export-default'
```

```js
export default function foo() {
	console.log('foo')
}
//or
function foo() {
  console.log('foo')
}
export default foo
```

export default 是输出一个叫做default的变量，所以它后面不能跟变量声明语句。但是可以直接export值

```js
exprt var a = 1 //correct
var a = 1
export default a//correct
export default var a = 1//incorrect

export default 42
```

可以同时引入默认变量和其它变量

```js
import customName, { otherMethod } form './profile'

//导出类
export default class {...}

import MyClass from './MyClass'
let o = new MyClass()
```

**模块的继承**

假设一个circleplus模块，继承了circle模块

```js
//circleplus.js
export * from './circle'
export var e = 2.71828182856
export default function(x) {
  return Math.exp(x)
}
```

**export *命令会忽略circle模块的default变量**

也可以给circle的变量改名

```js
//circleplus.js
export { area as circleArea } from './circle'

//main.js
import * as math from './circleplus'
import exp from 'circleplus'
console.log(exp(math.e)) // 2.71828182856
```

ES6模块加载的机制，与CommonJS模块完全不同。CommonJS模块输出的是一个值的拷贝，而ES6模块输出的是值的**只读**引用。

```js
//CommonJS
//lib.js
var counter = 3;
function incCounter() {
  counter ++
}
module.exports = {
  counter: counter,
  incCounter: incCounter
}
//main.js
var mod = require('./lib')

console.log(mod.counter); //3
mod.incCounter();
console.log(mod.counter); //3
```

```js
//对它进行稍微修改
//lib.js
var counter = 3;
function incCounter() {
  counter ++
}
module.exports = {
  get counter() { return counter },
  incCounter: incCounter
}
```

```js
//ES6 Module
//lib.js
export let counter = 3;
export function incCounter() {
  counter ++;
}

//main.js
import { counter, incCounter } from './lib'
console.log(counter);//3
incCounter();
console.log(counter);//4
```

**循环加载问题**

CommonJS的一个模块，就是一个脚本文件。require命令第一次加载该脚本，就会执行整个脚本，然后在内存生成一个对象。即使再次执行require命令，也不会再次执行该模块，而是到缓存之中取值。所以可以避免循环加载。

```js
//CommonJs Module对象
{
  id: '模块名',
	exports: {},
  loaded: true
}
```

CommonJS模块的重要特性是加载时执行，即脚本代码在require的时候，就会全部执行。一旦出现某个模块被"循环加载"，就只输出已经执行的部分，还未执行的部分不会输出。 

```js
//a.js
exports.done = false;
var b = require('./b.js');
console.log('in a.js, b.done = %j', b.done);
exports.done = true;
console.log('a.js complete');
```

在