## ES6学习笔记01

### 1.let&const

**let（const也是）声明的变量的作用域只在它所在的代码块内**

```js
{
  let a = 10;
  var b = 1;
}
console.log(a)//undefined
console.log(b)//1
```

```js
var a = [];
for (let i=0; i<10; i++) {
  a[i] = function() {
    console.log(i);
  };
}
a[6]();//6
```

上面代码中，变量i是let声明的，当前的i只在本轮循环中有效，所以每次循环的i其实都是一个新的变量

**let（const也是）不存在变量提升问题**

**let（const也可以）可以暂时性死区（temporal dead zone）**

```js
var tmp = 123;
if (true) {
  tmp = 'abc'; //ReferenceError 这里是暂时性死区
  let tmp;
}
```

因为if块里有let 声明的tmp，所以tmp就绑定了这个代码块。所以let声明前的tmp不是全局变量，而是一个未定义的let变量，所以会报错。

**暂时性死区的本质就是，只要已进入当前的代码块，所要使用的变量就已经存在了，但是不可获取，只有等变量声明之后才可以获取使用**

**let（const也是）不允许重复声明同一个变量**

**函数形参，也算是let声明的**

```js
function func(arg) {
  let arg;//报错
}
function func(arg) {
  {
    let arg;//不报错
  }
}
```

**var不支持块作用域&声明提升的弊端**

```js
var tmp = new Date();
function f() {
  console.log(tmp);
  if (false) {
    var tmp = 'hello world';
  }
}
f();//undefined
```

原本我想打印的是全局变量的tmp，因为没有块作用域，以及声明提升，我打印了局部变量tmp

```js
var s = 'hello';
for (var i=0; i<s.length; i++) {
  console.log(s[i]);
}
console.log(i);//6
```

**const声明常量，必须和赋值一起**

var和function在最顶层声明的变量/函数，是全部变量。它们都是全局对象的属性。

**ES6中let、const和class声明的全局变量，不是全局对象的属性**

### 2.变量的解构赋值

```js
//ES6
var [a, b, c] = [1, 2, 3];
//等价于ES5的
var a=1, b=2, c=3;
```

这种赋值方式也叫**模式匹配**，只要两边格式一样，就可以一一赋值。

```js
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo //1
bar //2
baz //3

let [ , , third] = ['foo', 'bar', 'baz'];
third //'baz;

let [x, ,y] = [1, 2, 3];
x //1
y //3

let [head, ...tail] = [1, 2, 3, 4];
head //1
tail //[2, 3, 4]

let [x, y, ...z] = ['a'];
x //'a'
y //undefined
z //[]

let [x, y] = [1, 2, 3];
x //1
y //2
```

如果解构不成功，变量的值就等于undefined。如果右边数组多余左边的变量，叫做**不完全解构**

**…可以收敛，也可以解开数组**

```js
let [...tail] = [1, 2, 3, 4]
//tail是数组[1, 2, 3, 4], ...收敛参数为数组
let [x, y, z, k] = [1, ...[2, 3, 4]]
//x=1, y=2, z=3, k=4
//...解开数组[2, 3, 4]，和前面的1组成[1， 2， 3， 4]
```

这个特性在函数的参数结构中很有用，形参的…聚合参数成数组，实参的…解开数组作为参数

**解构可以指定默认值**

```js
var [foo = true] = [];
foo // true
var [x=1] = [null];
x //null
```

右边必须匹配的空或者是undefined才会启用默认值

**默认值也可以是变量**

```js
let [x=1, y=x] = []; //x=1, y=1
let [x=y, y=1] = []; //ReferenceError
```

### 3.对象的解构赋值

解构赋值不仅可以用数组，也可以用对象。

```js
var {foo, bar} = { foo: 'aaa', bar: 'bbb' };
foo //'aaa'
bar //'bbb'
```

**数组解构是按次序的，对象解构是按key的**

**没法对应属性名的解构**

```js
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz //'aaa'

let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'
//嵌套
var node = {
  loc: {
    start: {
      line: 1,
      column: 5
    }
  }
}
var { loc: { start: { line }}} = node;
line //1
loc // loc is undefined
start //start is undefined
//声明和赋值分开，要用圆括号括起来
let obj = {}
let arr = []
({foo:obj.prop, bar:arr[0]} = {foo:123, bar:tre})
obj // {prop:123}
arr // [true]
```

上面只有line是变量，loc和start都是模式

**嵌套解构的时候，而且子对象所在的父属性不存在，将会报错**

```js
let {foo:{baz}} = {bar:'bar'} //报错，父属性foo对应不了数据
```

**字符串的解构赋值**

```js
const [a, b,c,d,e] ='hello';
a //'h'
b //'e'
c //'l'
d //'l'
e //'o'
//字符串除了可以当做数组，也是对象，所以也可以用对象解构
let {length: len} = 'hello';
len //5
//数值&布尔值这些有包装对象的，也可以解构赋值
let {toString: s} = 123;
s === Number.prototype.toString // true
```

解构赋值的右值必须是一个对象(或者可以有包装对象)，但是null和undefined不是对象，所以右值不可以是null或者undefined

### 4.函数参数的解构赋值

```js
function add([x,y]){
  return x+y;
}
add([1,2]) // 3
[[1,2],[3,4]].map(([a,b])=>a+b);
//[3,7]
//默认值
function move({x=0, y=0} = {}) {
  return [x,y];
}
move({x:3, y:8}) //[3, 8]
move({x:3}) //[3,0]
move()//[0,0]
```

**结构模式中不能带有（）或者多余的{}**

### 5.应用

```js
[x,y]=[y,x] //交换
//函数返回多个值，类似元组
function example() {
  return [1,2,3]
}
var [a,b,c] = example()
function example1() {
  return {
    foo: 1,
    bar: 2
  }
}
var {foo, bar} = example1()
//函数传参
function f({x, y, z}) {
}
f({z: 3, y: 2, x: 1});
//json数据提取
var jsonData = {
  id: 42,
  status: 'ok',
  data: [867, 5309]
}
let { id, status, data:number} = jsonData;
//模块解构
const {SourceMapConsumer, SourceNode} = require('source-map');
```

