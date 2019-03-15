## js学习笔记04

### 表达式和运算符

**数组直接量中的列表逗号直接的元素可以省略，省略的空位用undefined填充**

```js
var sparseArr = [1,,,,5,]; //最后一个逗号分隔并不会填充undefined，所以长度还是5
```

数组直接量的元素列表结尾处可以留下单个逗号，这时并不会填充一个undefined值

**如果创建对象表达式不需要传入任何参数给构造函数，那么圆括号可以省略**

```js
suinew Object
new Date
```

### 创建对象表达式

创建对象表达式由两部分组成：创建一个对象，调用初始化函数初始化对象的属性。

**js首先创建一个空对象，然后构造函数里通过this访问这个新对象，初始化它的属性。**

```js
function createO(x) {
   this.x = x
}
let xo = new createO(3)
console.log(xo.x) //=>3 
```

如果构造函数返回一个对象，这个对象将作为创建对象表达式的返回值，新创建的对象（new出来的对象）就废弃了

```js
function createO(x) {
   this.x = x
   return {'x':x+2}
}
let xo = new createO(3)
console.log(xo.x) //=>5 这里使用的是返回的对象，new的对象被废弃了
```

### 运算符优先级

属性访问和函数调用表达式的优先级高于任何运算符

```js
typeof my.functions[x](y)
```

虽然typeof是优先级最高的运算符，它还是在两次属性访问和函数调用之后执行

**+运算符列子**

```js
1+2//=>3
'1'+'2'//=>'12'
'1'+2//=>'12'
1+{}//=>'1[object Object]' 对象转换为字符串后进行字符串链接
true+true//=>2 true转为1后做加法
2+null//=>2 null转为0后做加法
2+undefined//=>NaN undefined转为NaN后做加法
1+2+'blind mice'//=>'3blind mice'
1+(2+'blind mice')//=>'12blind mice'
```

**比较预算付例子**

```js
'11'<'3'//字符串比较，结果为true
'11'<3//数字比较，'11'转换为11，结果为false
'one'<3//数字比较，’one'转换为NaN，结果为false
```

**比较运算符里有一个奇葩，只要比较运算表达式里有NaN（或者值转换后是NaN），所有比较运算均返回false**

**任何位运算符都会将NaN、Infinity和-Infinity转换为0（实际验证返回值是-1？？？？）**

### in&instanceof运算符

in运算符的左操作数是一个字符串（或者可以转换成字符串），右操作数是一个对象。如果右侧对象用有一个名为左操作数值的属性名，则返回true

```js
var point = {x:1, y:2}
'x' in point //=>true;对象有一个名为'x'的属性
'z' in point //=>false;对象中不存在名为'z'的属性
'toString' in point //=>true; 对象继承了toString()方法
var data = [7, 8, 9]
'0' in data //=>true;数组包含元素'0'
1 in data //=>true;数字转换为字符串
3 in data //=>false;没有索引为3的元素
```

instanceof预算符左操作数是一个对象，右操作数是一个对象的类。如果左侧是右侧类的实例（或者子类的实例），则表达式返回true，否则返回false。

```js
var d=new Date()
d instanceof Date //=>true，的是Date的实例
d instanceof Object //=>true，所有对象都是Object的实例
```

