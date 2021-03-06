## js学习笔记02

### 1.布尔值

下面这些值会被转换成false

```js
undefined
null
0
-0
NaN
""
```

false和上面可以转换成false的值有时被称作假值，其它值称为真值

### 2.null和undefined

对null执行typeof运算，结果返回字符串“object”，也就是说，可以将null认为是一个特殊的对象值，含义是“非对象”。但实际上，通常认为null是它自有类型的唯一一个成员，它可以表示数字、字符串和对象是“无值”的。

undefined是预定义的全局变量（它和null不一样，它不是关键字。同样的还有NaN和Infinity，是全局常量），它的值就是“未定义”。ES3中它是可读/可写的变量，可以给它赋值。ES5之后undefined变成了只读的。使用typeof运算符得到undefined的类型，则返回“undefined”，表明这个值是这个类型的唯一成员。

null和undefined是不同的，但是判断相等运算符“==”认为两者是相等的（要使用严格相等运算符“===”来区分它们）

你或许认为undefined是表示系统级的、出乎意料的或类似错误的值的空缺，而null是表示程序级的、正常的或在意料之中的值的空缺。如果你想将它们赋值给变量或者属性，或将它们作为参数传入函数，最佳选择是使用null。

### 3.全局对象

在代码的最顶级（不在任何函数内的js代码）可以使用js关键字this来引用全局对象。在客户端js中，在其表示浏览器窗口的所有js代码中，Window对象可以充当全局对象。这个Window对象有一个属性window引用其自身，它可以代替this引用全局对象。

**如果代码声明了一个全局变量，这个全局变量就是全局对象的一个属性**

### 4.包装对象

字符串、数字和布尔值，都是基础数据，非对象，为什么他们可以通过“.”符合调用属性/方法？？？？

**只有引用了这些值的属性，js就会将这些值通过调用new String(), new Number(), new Boolean()的方式转换成对象，这个对象用来处理属性的引用。一旦属性引用结束，这个新对象就会被销毁。这个对象称为包装对象。**

（其实在实现上并不一定创建或销毁这个临时对象，然而整个过程看起来是这样）

```js
var s="test"
s.len = 4
var t= s.len
```

第二行代码创建了一个临时字符串对象，并给其len属性赋值4，随即销毁这个对象。

第三行又创建了一个新的临时字符串对象，并尝试读取他的len属性，这个属性自然不存在

所以t为undefined

（在某些js运行环境中并不支持包装对象）

需要注意的是，可通过String()、Number()、Boolean()构造函数显式创建包装对象

```js
var s='test', n=1, b=true;
var S=new String(s)
var N=new Number(n)
var B=new Boolean(b)
```

以上s和S，n和N，b和B，“==”等于运算符将原始值和包装对象视为相等，但“===”全等运算符将它们视为不等。（使用typeof，包装对象的类型是object）

### 5.不可变的原始值和可变的对象引用

js中的原始值（undefined、NaN、Infinity、null、布尔值、数字和字符串）与对象（包括数组和函数）有着根本区别。原始值是不可更改的：任何方法都无法更改（或“突变”）一个原始值。

字符串中所有方法看起来是返回了修改后的字符串，实际上是返回了一个新的字符串。

### 6.类型转换

```js
var n=123456.789;
n.toFixed(0);//"123457" 四舍五入
n.toFixed(2);//"123456.79"
n.toFixed(5)://"123456.78900"
n.toExponential(1);//"1.2e+5"
n.toExponential(3);//"1.235e+5"
n.toPrecision(4);//"1.235e+5"
n.toPrecision(7);//"123456.8"
n.toPrecision(10);//"123456.7890"
```

