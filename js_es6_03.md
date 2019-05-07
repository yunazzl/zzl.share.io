## ES6学习笔记03

### 1.Iterator和for…of循环

原生具有Iterator接口的数据类型：数组、类数组、字符串、Set、Map

任意一个对象的Symbol.iterator方法，等于该对象的遍历器生成函数，调用该函数会返回该对象的一个遍历器对象

```js
var myInterable = {}
myInterable[Symbol.iterator] = function* () {
  yield 1;
  yield 2;
  yield 3;
}

[...myInterable] // [1， 2， 3]
```

### 2.Generator函数

Generator函数是ES6提供的一种异步编程解决方案

形式上，Generator函数是一个普通函数，但是有两个特征。一是，function关键字与函数名之间有一个星号;二是，函数体内部使用yield语句，定义不同的内部状态(yield语句在英语里的意思就是“产出”)。 

```js
function * helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}
var hw = helloWrldGemerator();
```

调用Generator函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，也就是上一章介绍的遍历器对象(Iterator Object)。

```js
hw.next() // {value: 'hello', done: false}
hw.next() // {value: 'world', done: false}
hw.next() // {value: 'ending', done: true}
hw.next() // {value: undefined, done: true}
```

遍历器对象的next方法的运行逻辑如下。 

1. 遇到yield语句，就暂停执行后面的操作，并将紧跟在yield后面的那个表达式的值，作为返回的对象的value属性值。 
2. 下一次调用next方法时，再继续往下执行，直到遇到下一个yield语句。 
3. 如果没有再遇到新的yield语句，就一直运行到函数结束，直到return语句为止，并将return语句后面的表达式的值，作为返回的对象 的value属性值。 
4. 如果该函数没有return语句，则返回的对象的value属性值为undefined。 

**这样，Generator可以分步执行，返回一系列结果**

```js
var arr = [1, [[2, 3], 4], [5, 6]]

var flat = function* (a) {
  var length = a.length
  for (var i=0; i<length; i++) {
    var item = a[i]
    if (typeof item !== 'number') {
      yield* flat(item)
    } else {
      yield item
    }
  }
}

for (var f of flat(arr)) {
  console.log(f)
}
// 1, 2, 3, 4, 5, 5
```

遍历器的遍历器就是自己

```js
function* gen() {
	// some code
}
var g = gen()

g[Symbol.iterator]() === g // true
```

**yield语句本身没有返回值，next方法可以带一个参数，该参数就会被当做上一个yield语句的返回值。**

！！！！！！！上一个yield的返回值！！！！！！！

```js
function* f() {
	for (var i=0; true, i++) {
    var reset = yield i;
    if (reset) { i = -1; }
  }
}
var g = f()
g.next() // {value: 0, done: false}
g.next() // {value: 1, done: false}
g.next(true) // {value: 0, done: false}

function* foo(x) {
  var y = 2 * (yield (x+1));
  var z = yield (y/3);
  return (x+y+z);
}
//在运行时注入参数
var b = foo(5)
b.next() //{value:6, done: false}
b.next(12) //{value:8, done: false}
b.next(13) //{value: 42, done: true}
```

**yield*，在一个Generator函数里调用执行另一个Generator函数**

```js
function* foo() {
  yield 'a';
  yield 'b';
}
function *bar() {
  yield 'x';
  yield* foo();
  yield 'y';
}
//等同于
function* bar() {
  yield 'x';
  yield 'a';
  yield 'b';
  yield 'y';
}
function* bar() {
  yield 'x';
  for (let v of foo()) {
    yield v;
  }
  yield 'y';
}

```

### 3.Promise

```js
var promise = new Promise((resolve, reject)=>{
	//... some code
  //新建之后会立即执行
  if (/*异步操作*/) {
    resolve(value)
  } else {
    reject(error)
  }
})

promise.then(value=>{
  //success
}, error=>{
  //failure
})
```

如果调用resolve函数和reject函数时带有参数，那么它们的参数会被传递给回调函数。reject函数的参数通常是Error对象的实例，表示抛出的错误;resolve函数的参数除了正常的值以外，还可能是另一个Promise实例。表示异步操作的结果有可能是一个值，也有可能是另一个异步操作。

```js
var p1 = new Promise((resolve, reject)=>{
})
var p2 = new Promise((resolve, reject)=>{
  resolve(p1)
})
```

这时p1的状态就会传递给p2，也就是说，p1的状态决定了p2的状态。如果p1的状态是Pending，**那么p2的回调函数就会等待p1的状态改变**;如果p1的状态已经是Resolved或者Rejected，那么p2的回调函数将会立刻执行。 

**then()**

then方法返回的是一个**新的**Promise实例，因此可以采用链式调用

```js
getJson('/posts.json').then(json=>{
  return json.post
}).then(post=>{
  //...
})
```

第一个then的返回值，将作为下一个then的参数。

前一个回调函数，有可能返回的还是一个Promise对象(即有异步操作)，这时后一个回调函数，就会等待该Promise对象的状态发生变化，才会被调用。 

```js
getJson('/post/1.json').then(
	post => getJson(post.commentURL)
).then(
	comments => console.log('resolved', comments),
  err => console.log('rejected', err)
)
```

上面是两个网络请求

**catch()**

catch是.then(null, rejection)的另外一种写法，用于指定发生错误时的回调函数

```js
getJson('/posts.json').then(posts=>{
}).catch(error=>{
  console.log('发生错误！', error)
})
```

例子

```js
getJson('/post/1.json').then(post=>{
  return getJson(post.commentURL)
}).then(comments=>{
  // some code
}).catch(error=>{
  //处理前面三个promise产生的错误
})
```

Promise里的异常都会被catch捕获，若果没有catch，也不会传递到外层

```js
var someAsyncThing = function() {
  return new Promise((resolve, reject)=>{
    //下面一行会报错，因为x没有声明
    resolve(x+2)
  })
}
someAsyncThing().then(()=>{
  console.log('everything is great')
})
```

