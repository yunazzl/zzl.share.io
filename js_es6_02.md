## ES6学习笔记02

### 1.字符串扩展

```js
//字符的Unicode表示法
'\u0061' //'a'
'\uD842\uDFB7' //'吉'
//以上单个值不能超过0xffff，但是ES6进行了扩展
'\u{20bb7}' //'吉'
```

**模板字符串**

```js
$('#result').append(`
there are <b>${basket.cout}</b> items
in your basket, <em>${basket.onSale}</em>
are on sale!
`) 
```

**这里使用的是反引号(`)，是增强版的字符串，支持内部换行，或者在内部嵌入变量**

### 2.标签模板

```js
alert`123`
//等同于
alert(123)
```

标签模板就是函数调用的一种特殊形式，如果模板带有变量，情况就复杂一点

```js
function tag(stringArr, ...values) {
  
}
var a=5, b=10
tag`Hello ${a+b} world ${a*b}`;
//等同于
tag(['Hello ', ' world ', ''], 15, 50)
```

tag函数的第一个参数是一个数组，该数组成员就是模板字符串没有被变量替换的部分(被变量分割后的，一段段字符串，如果变量后没有字符串，就是空字符串'')，tag的其它参数就是各个变量的值。

将各个参数按照原来的位置拼合回去

```js
function tag(stringArr, ...values) {
  var result = ''
  var i = 0
  while (i < stringArr.length) {
    result += stringArr[i]
    if (i<values.length) {
      result += values[i]
    }
    i++
  }
  return result; 
}
tag`Hello ${a+b} world ${a*b}`;//‘Hello 15 world 50'
```

**标签模板的应用**

```js
//防止用户输入恶意内容
//有点像filter
var message = SaferHTML`<p>${sender} has sent you a message.</p>`;
function SaferHTML(templateData, ...values) {
  var s = templateData[0];
  for (let i=0; i<values.length; i++) {
    let arg = String(values[i])
    //过滤掉输入中的&、<和>
    s+=arg.replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;');
    s+=templateData[i+1];
  }
  return s
}
```

```js
//多语言切换(国际化)
let str = i18n`Welcom to ${siteName}, you are visitor number ${visitorNumber}!`
//'欢迎访问xxx, 您是第xxx位访问者！'
```

### 正则的扩展

[迁移到js学习，正则段](./js_markdown_10)

### 数组的扩展

[迁移到js学习，数组段](./js_markdown_07)

### 函数的扩展

[迁移到js学习，函数段](./js_markdown_08)

### 对象的扩展

[迁移到js学习，对象段](./js_mardown_06)

### Symbol类型

symbol值通过Symbol函数生成，是一个独一无二的值。这是一个原始类型，不是对象。

```js
let s1 = Symbol('foo')
let s2 = Symbol('foo')

s1 === s2 //false

//symbol值不能与其他类型的值进行运算
let sym = Symbol('my symbol')
'your symbol is ' + sym //typeError: can`t covert symbol to string
`your symbol is ${sym}` //typeError: can`t covert symbol to string

//symbol可以显示地转为字符串
String(sym) //'my symbol'
sym.toString()//'my symbol'
//可以转为boolean，但是不能转为number
Boolean(sym)//true
!sym //false
Number(sym) //TypeError
```

**symbol作为属性名**

```js
let mySymbol = Symbol()

//first style
var a = {}
a[mySymbol] = 'hello'

//second style
var a = {
  [mySymbol]: 'hello'
}

//third style
var a = {}
Object.defineProperty(a, mySymbol, { value: 'hello' })
```

symbol可以作为常量key

```js
const size = Symbol('size')

class Collection {
  constructor() {
    this[size] = 0
  }
  add(item) {
    this[this[size]] = item
    this[size] ++
  }
  static sizeOf(instance) {
    return instance[size]
  }
}

var x = new Collection(); Collection.sizeOf(x) // 0
x.add('foo'); Collection.sizeOf(x) // 1
Object.keys(x) // ['0'] 
Object.getOwnPropertyNames(x) // ['0'] 
Object.getOwnPropertySymbols(x) // [Symbol(size)]
```

**Symbol.for(), Symbol.keyFor()**

```js
Symbol.for('bar') == Symbol.for('bar') //true
Symbol('bar') == Symbol('bar') //false

let s1 = Symbol.for('foo')
Symbol.keyFor(s1) //'foo'

let s2 = Symbol('foo')
Symbol.keyFor(s2) //undefined
```

需要注意的是，Symbol.for为Symbol值登记的名字，是全局环境的，可以在不同的iframe或service worker中取到同一个值。

### Proxy和Reflect（代理和反射）

```js
var obj = new Proxy({}, {
  get: function (target, key, receiver) {
    console.log(`getting ${key}!`)
    return Reflect.get(target, key, receiver)
  },
  set: function (target, key, value, receiver) {
  	console.log(`setting ${key}!`)
    return Reflect.set(target, key, value, receiver)
	}
})
```

**Proxy也可以作为其他对象的原型**

```js
var proxy = new Proxy({}, {
  get(target, property) {
    return 35
  }
})
let obj = Object.create(proxy)
obj.time //35
```

**Proxy代理函数**

```js
var handler = {
  get (target, name) {
    if (name === 'prototype') {
      return Object.prototype
    }
    return 'Hello, ' + name
  },
  apply (target, obj, args) {
    return args[0]
  },
  construct (target, args) {
    return {value, args[1]}
  }
}
var fproxy = new Proxy(function(x, y) {
  return x+y
}, handler)

fproxy(1, 2) //apply: 1
new fproxy(1, 2)//construct: {value:2}
fproxy.prototype === Object.prototype //get: true
fproxy.foo //get: 'Hello, foo'
```

**Proxy的所有拦截器**

```js
get(target, propkey, receiver) //
set(target, propkey, value, receiver) //
has(target, propkey) // 拦截in操作符，propkey in proxyB
deleteProperty(target, propkey) //拦截delete操作符，delete proxyB[propkey]
ownKeys(target) //Object.getOwnPropertyNames(),Object.getOwnPropertySymbols(),Object.keys()
getOwnPropertyDescriptor(target, propkey) //
defineProperty(target, propkey, propdesc) //
preventExtensions(target) //Object.preventExtensions()
getPrototypeOf(target) //拦截Object.getPrototypeOf()，返回一个对象
isExtensible(target) //拦截Object.isExtensible(),返回布尔值
setPrototypeOf(target, proto) //拦截Object.setPrototypeOf()，返回布尔值,如果是函数，除了这个，set也可以拦截
apply(target, object, args) //拦截函数调用，proxyF(...args)/proxyF.call(obj, ...args)/proxyf.apply(obj, args)
construct(target, args) //拦着构造器, new proxyF(...args)
```

**Proxy的实例方法**

```js
//实现可以读取负数索引的数组
function createArray(...elements) {
  let handler = {
    get(target, propkey, receiver) {
      let index = Number(propkey)
      if (index < 0) {
        propkey = String(target.length+index)
      }
      return Reflect.get(target, propkey, receiver)
    }
  }
  
  let target = []
  target.push(...elements)
  return new Proxy(target, handler)
}
let arr = createArray('a', 'b', 'c')
arr[-1] //c

//实现链式开发
let pipe = (function(){
 return function (value) {
   var funcStack = []
   var proxy = new Proxy(pipe,{
     get (target, propkey) {
       if (propkey === 'get') {
         return funcStack.reduce((val, fn)=>{
           return fn(val)
         }, value)
       }
       if (propkey in target) {
         funcStack.push(target[propkey])
       }
       return proxy
     }
   })
    return proxy
 }
}())

pipe.double = v => v*2
pipe.pow = v => v*v
pipe.reverseInt = v => v.toString().split('').reverse().join('') | 0

console.log(pipe(3).double.pow.reverseInt.get)

//dom节点生成器
const dom = new Proxy({}, {
  get(target, property) {
    return function(attrs = {}, ...children) {
      const el = documents.createElement(property)
      for (let prop of Object.keys(attrs)) {
        el.setAttribute(prop, attrs[prop])
      }
      for (let child of children) {
        if (typeof child === 'string') {
          child = document.createTextNode(child)
        }
        el.appendChild(child)
      }
      return el;
    }
  }
})
const el = dom.div({},
                  'Hello, my name is ',
                  dom.a({href: '//example.com'}, 'mark'),
                  '. I like',
                  dom.ul({},
                        dom.li({}, 'The web'),
                        dom.li({}, 'Food'),
                        dom.li({}, '...actually that\`s it`')
                        )
                  )
document.body.appendChild(el)
```

**Proxy.revocable()生成一个可取消的Proxy实例**

```js
let {proxy, revoke} = Proxy.revocable({}, {})

proxy.foo = 123
proxy.foo // 123

revoke()
proxy.foo //TypeError: Revoked
```

### Set

set类似于数组，但是成员的值都是唯一的

```js
var s = new Set();
[2, 3, 5, 4, 5, 2, 2].forEach(v = > s.add(v))
for (let i of s) {
	console.log(i)
}
//2 3 4 5
//去除重复部分
[...new Set(array)]
```

set的方法

```js
add(v) //添加值，返回set自身。s.add(1).add(2).add(2)
delete(v) //删除v，返回一个布尔值
has(v) //v是否为set成员
clear() //清空set
```

```js
keys()//返回键名遍历器，set没有健名，所以返回的是键值等同于values()
values()//返回键值遍历器
entries()//返回键值对遍历器
forEach()//函数遍历每个成员

let set = new Set(['red', 'green', 'blue'])
for (let item of set.keys()) {
  console.log(item)
}
//red green blue
for (let value of set.values()) {
  console.log(value)
}
//red green blue
for (let item of set.entries()) {
  console.log(item)
}
//['red', 'red'] ['green', 'green'] ['blue', 'blue']
for (let x of set) {
  console.log(x)
}
//red green blue
```

**应用**

```js
let set = new Set([3, 5, 2, 2, 5, 5])
let unique = [...set] //[3, 5, 2]
```

**WeakSet类似Set，但是成员只能是对象，而且是弱引用。成员对象有可能被回收了，所以不可遍历。**

```js
let ws = new WeakSet()
let obj = {}
ws.add(obj)
ws.delete(obj)
ws.has(obj)

ws.size //undefined
ws.forEach //undefined

//适合做全局弱引用栈
const foos = new WeakSet()
class Foo {
  constructor() {
    foos.add(this)
  }
  method () {
    if (!foos.has(this)) {
      throw new TypeError('foo.prototype.method 只能在Foo的实例上调用')
    }
  }
}
```

### Map

Map和对象一样，都是键值对。但是对象(Object)的健只能是字符串，而Map的键可以使任意类型

```js
//对象方式
var data = {}
var element = document.getElementById('myDiv')

data[element] = [metadata] //data['[object HTMLDivElement]'] = 'metadata'
//element被自动转成字符串
```

Object提供了'字符串-值'的对应，Map结构提供了'值-值'的对应

```js
var m = new Map()
var o = {p; 'Hello world'}

m.set(o, 'content')
m.get(o) //'content'
m.has(o) //true
m.delete(o)//true
m.has(o)//false

var map = new Map({
  ['name', 'zhangsna'],
  ['title', 'author']
})
map.size // 2
map.has('name')//true
map.get('name')//'zhangsan'
map.has('title')//true
map.get('title')//'author'
```

**键值比较的时候，比较的是内存地址**

```js
var map = new Map()
var k1 = ['a']
var k2 = ['a']

map.set(k1, 111).set(k2, 222)

map.get(k1) // 111
map.get(k2) // 222
```

