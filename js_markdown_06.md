## js学习笔记06

### 1.对象继承性--原型

**js中的继承是一个对象继承领一个对象（区别于其他语言的类继承）**

没有原型的对象为数不多：Object.prototype

1. 直接量({})创建的对象有默认的原型：Object.prototype；
2. new Create() 创建的对象的原型是构造器的原型(直接量创建的对象都是用的Object构造器，所以它们的原型是Object.prototype)；
3. Object.create()创建对象，传入的第一个参数就是对象的原型，如果不传就没有原型

**只有构造器可以直接用.prototype，set&get原型（内置构造器的prototype是只读的），对象使用Object.getPrototypeOf()，Object.setPrototypeOf()**

**每个对象都可以从原型对象继承属性（方法）**

如果对象和原型有相同名字的属性，访问这个属性的时候，访问的是对象的属性。(对原型没有影响)

**js中，只有在查询属性时才会用到继承性**

**特例：如果原型的属性时一个具有setter方法的accessor属性，可以直接设置原型的属性(其实是访问了setter方法，本质也是查询)。该setter方法值继承自原型的，但是调用者还是该对象，所以，如果setter方法里有对属性的修改，也是修改该对象的属性，对原型没有影响**

从原型继承的属性可以直接访问，如果给原型新增属性，对象也可以访问新增的属性。反之(删除)亦然。

**但是对象不能修改原型的值，尝试修改原型的对象，只是给对象自己新增了一个属性而已，对原型没有影响**

```js
var absoluteObj = {} //原型是Object.prototype
var dataObj = new Date() //原型是Date.prototype
var prototypObj = {x:1, y:2}
var createObj = Object.create(prototypeObj) //原型是prototypeObj
console.log(createObj.x) //=>1 继承了prototypeOb的x
prototypeObj.z = 3
console.log(createObj.z) //=>3 继承了prototypeObj新增的属性z
createObj.x = 5	//createObj新增了x属性，对prototypeObj的x属性没有影响
console.log(createObj.x) //=>5
console.log(prototypeObj.x) //=>1
console.log(Object.getPrototypeOf(createObj) == prototypeObj ? 'yes' : 'no') //=>yes
// Object.getPrototypeOf()可以读取对象的原型 
var prototypeObj1 = {
      z: 18,
      set p (v) {
       this.z = v
      }
    }
Object.setPrototypeOf(createObj, prototypeObj1) //Object.setPrototypeOf()可以修改对象的原型
console.log(createObj.z) //=>18，修改后的原型的z值是18
createObj.p = 20 //访问的原型属性p的setter方法
//方法调用者还是createObj，所以修改的还是它的z属性(这里是新增z属性)
console.log(prototypeObj1.z) //=>18 原型的z值没有修改
console.log(createObj.z) //=>20 修改的是createObj的z值
```

isPrototypeOf()方法检测一个对象是否是另一个对象的原型(或处于原型链中)。与instanceof很相似

```js
p.isPrototypeOf(x)	
```

### 2.属性的访问

1. 使用Object.defineProperty()方法可以对可配置的只读属性赋值
2. 如果o的原型中的属性p是只读的，o.p=3，并不能新增一个属性，而且会报错
3. 如果o是不可扩展的对象，且没有属性p，它的原型也没有p的setter方法可以访问，o.p=3会报错

### 3.属性的删除

**delete只能删除自有属性，不能删除继承属性**

delete删除成功，或者没有副作用(比如删除不存在的属性，或不是一个属性表达式)，都会返回true

**delete不能删除不可配置的属性**

不可扩展对象的属性可以删除

```js
o={x:1};//o有一个属性x，并继承属性toString
delete o.x;//删除x，返回true
delete o.x;//什么都没做（x已经不存在了），返回true
delete o.toString;//什么也没做（toString是继承来的），返回true
delete 1;//无意义，返回true

delete Object.prototype;//不能删除，属性是不可配置的
var x=1;//声明一个全局变量
delete this.x;//不能删除这个属性
function f(){}//声明一个全局函数
delete this.f;//也不能删除全局函数

this.b=1;//创建一个可配置的全局属性（没有用var）
delete b;//将它删除 严格模式下语法错误
delete this.b//正常工作
```

### 4.检测属性

in、hasOwnProperty()、propertyIsEnumerable()

hasOwnProperty()方法用来检测给定的名字是否是对象的自有属性(排除掉继承的)

propertyIsEnumerable()只有检测到是自有属性且这个属性的枚举性为true，才返回true

```js
var o={x:1}
"x"in o;//true："x"是o的属性
"toString"in o;//true：o继承toString属性
o.hasOwnProperty('x')//true
o.hasOwnProperty('toString')//false
o.propertyIsEnumerable("x");//true:o有一个可枚举的自有属性x
o.propertyIsEnumerable("y");//false:y是继承来的
Object.prototype.propertyIsEnumerable("toString");//false:不可枚举
```

### 5.属性的特性

Object.getOwnPropertyDescriptor()可以获得某个对象特定属性的属性描述符

```js
{value:1,writable:true,enumerable:true,configurable:true}
```

Object.defineProperty()可以设置属性的特性，或者新建属性

```js
var o = {}
Object.defineProperty(o, 'x', {value:1,writable:true,enumerable:false,configurable:true})
o.x //=>1
Object.defineProperty(o, 'x', {writable:false}) //设置成只读
o.x = 2 //=>1只读属性，修改不了值
Object.defineProperty(o, 'x', {value:2}) //可以修改只读属性的值，如果是不可配置的，就不行了
o.x //=>2
Object.defineProperty(o, 'x', {get:function(){return 0}})
o.x //=>0 x变成一个只有get方法的
```

对于新创建的属性来说，默认的特性值是false或undefined。对于修改的已有属性来说，默认的特性值没有做任何修改

如果要同时修改或创建多个属性，则需要使用Object.defineProperties()

**不可扩展对象，用这两个方法也不可以新增属性，修改已有属性是可以的**

**可配置特性控制着对其他特性（包括属性是否可删除，还包括自己）的修改。但是不可配置的属性，可以将读写特性改成只读的。**

### 6.可扩展性

```js
Object.isExtensible() //对象是否可扩展
Object.preventExtensions() //将对象转换为不可扩展的
Object.seal() //封锁对象。对象变为不可扩展且所有属性不可配置
Object.isSealed()
Object.freeze() //冻结，对象变为不可扩展，所有属性不可配置，所有数据属性变为只读
Object.isFrozen()
```

### ES6扩展

**对象属性的简洁表示法**

```js
var foo = 'bar'
var baz = {foo}
baz // {foo: 'bar'}

//等同于
var baz = {foo: foo}

var o = {
  method() {
    return 'hello'
  }
}
//等同于
var o = {
  method: function() {
    return 'hello'
  }
}
```

**属性的赋值（setter）&取值器（getter）**

```js
var cart = {
	_wheels: 4,
	get wheels () {
		return this._wheels;
	},
	set wheels(value) {
		if (value < this._wheels) {
			throw new Error('value too small')
		}
		this._wheels = value
	}
}
```

**表达式作为属性名**

```js
let propkey = 'foo'
let lastWord = 'last word'
let obj = {
	[propkey]: true,
	['a' + 'bc']: 123,
	[lastWord]: 'world'
}

//等同于
let obj = {
  foo: true,
  abc: 123,
  'last word': 'world'
}
obj['ab'+'c'] //123
obj[lastWord] //'world'
obj['last world'] //'world'
```

**对象合并Object.assign()**

```js
var target = { a: 1}
var source1 = {b: 2}
var source2 = {c: 3}

Object.assign(target, source1, source2)
target // { a:1, b:2, c:3 }

//后面的值会覆盖前面的值
var target = { a: 1, b: 1 }
var source1 = {b: 2, c:2}
var source2 = {c: 3}

Object.assign(target, source1, source2)
target // { a:1, b:2, c:3 }

//如果参数不是对象，则会先转成对象，参数不能是undefined或者null

//常见用途
//批量赋值
class Point {
  constructor(x, y) {
    Object.assign(this, {x, y})
  }
}
Object.assign(SomeClass.prototype, {
  someMethod(arg1, arg2) {
  },
  anotherMethod() {
  }
})
//克隆对象
function clone(origin) {
  return Object.assign({}, origin)//只能克隆非继承的值
}
function clone(origin) {
  let originProto = Object.getPrototypeOf(orgin)//提取原型
  return Object.assign(Object.create(originProto), origin)//创建兄弟对象，并赋值。可以保持继承的值
}
const merge = (...sources) => Object.assign({}, ...sources)//合并多个对象
//指定默认值
const DEFAULTS = {
  logLevel: 0,
  outputFormat: 'html'
}
function processContent(options) {
  let options = Object.assign({}, DEFAULTS, options)
}
```

**对象的扩展运算符**

Rest结构赋值不会拷贝继承自原型的对象

```js
let {x, y, ...z} = {x:1, y:2, a:3, b:4 }
z // {a: 3, b:4}

var o = Object.creat({x: 1, y: 2});
o.z = 3;
let {x, ...{y, z}} = o;
x// 1 这是普通的解构
y// undefined rest不能解构继承的属性
z// 3 rest解构自身属性z

//运用
let aClone = {...a}
//等同于
let aClone = Object.assign({}, a)

//rest对象里有get方法，这个函数会被执行
let runtimeError = {
  ...a,
  ...{
    get x() {
      throws new Error('throw new')
    }
  }
}
//x被执行，抛出一个异常
```

