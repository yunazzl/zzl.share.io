## js学习笔记08

## 函数

函数每次调用都会拥有一个本次调用的上下文：this

**当函数是一个对象的方法时，这个对象调用这个方法时的上下文(this)就是这个对象**

### 函数调用的方式

1. 作为函数
2. 作为方法
3. 作为构造函数
4. 通过call()和apply()方法简介调用

**没有参数的构造函数可以省略括号**

### 实参对象

```js
function f(x,y,z) {
  //首先验证传入的实参的个数是否正确
  if (arguments.length!=3) {
    throw new Error('function f called with '+arguments.length+' arguments, but it expects 3 arguments.')
  }
  // ......
  
}
```

**实参对象可以修改形参的值**

```js
function f(x) {
  arguments[0] = null;//修改实参数组的元素同样会修改x的值
  console.log(x);//输出null
}
```

如果实参对象是一个真正的数组，就做不到这一点了

这个例子里arguments[0]和x指代同一个值

**arguments有两个属性，callee(表示这个函数本身)，caller(表示函数的调用者)**

### 自定义函数的属性

js函数的本质也是一个对象，所以它也可以添加自定义对象**可以当做函数的静态变量**

```js
uniqueInteger.counter = 0
function uniqueInteger() {
  return uniqueInteger.counter ++
}
```

### 函数当做命名空间

将自己的代码放入一个函数体内，这样声明的变量就变成了局部变量，避免了自己声明的变量和其它地方声明的变量之间存在冲突

```js
(function(){
  //模块代码
}())//定义完函数要立即调用，要不然代码就没有意义了
//最外层的括号不能省略，避免它被解析成函数声明
```

### 闭包

从技术角度讲，所有js函数都是闭包

```js
var scope = 'globle scope'
function checkscope() {
  var scope = 'local scpe'
  function f() {return scope}
  return f
}
console.log(checkscope()())//=>‘local scpe'
```

**嵌套函数，在外部函数每次调用的时候都会重新定义一次**

**闭包（函数）的作用域链式在声明的时候确定的**

上例的最后一行：checkoutscope()调用，内部函数f被定义，此时作用域链确定，这个作用域链里'local scpe'优先被访问。checkoutscope()()执行的是已经确定作用域链的函数。

上面的uniqueInteger函数的属性暴露出来了，容易被恶意修改，可以用闭包的这个特性重新修改一下

```js
var uniqueInteger = (function(){
  var counter = 0
  return function() {
    return counter ++
  }
}())//外部无法修改局域变量counter
```

**复习**：命名空间函数外面的括号不能少，否则赋值给uniqueInteger的函数就不是我们期望的那个了

**一次调用创建函数，如果可以返回多个闭包，这个counter还可以在多个闭包之间共享。多次调用返回的闭包不可以共享**

```js
var uniqueInteger = (function(){
  var counter = 0
  return {
    count:function() { return counter ++ },
    reset:function() { counter = 0}
  }
}())//count负责计数，reset负责重置
```

### 函数的属性、方法和构造器

**length**

函数的length属性表示形参的个数，是一个只读属性。与arguments的length形成鲜明对比(arguments.length表示实参的个数)

**prototype**

每个函数都有不同的原型。当用作构造器时，初始化的对象都继承这个函数的原型

**call()和apply()方法**

它们的第一个参数都是实际调用该方法的对象。call后面还可以跟多个参数，作为函数的参数。apply后面可以跟一个数组(类数组也行)，作为函数的参数。

```js
function m(x, y) {
  return x+y
}
var o = {}
console.log(m.call(o, 1, 2))//=>3
console.log(m.apply(o, [1, 2]))//=>3
o.m = m
console.log(o.m(1, 2))//=>3
```

**bind()方法**

bind类似于call，但是bind返回的是一个新函数，而且这个函数不是立即执行。

```js
function f(y) {return this.x+y}
var o={x:1}
var g=f.bind(o)
g(2)//=>3
```

bind的实现原理

```js
function bind(f, o) {
  if (f.bind) return f.bind(o)
  return function() {
    return f.apply(o, arguments)
  }
}
```

**bind传入的第一个参数绑定到函数的this，后续的实参逐一绑定到函数的形参。新函数传入的实参会绑定到后剩余的形参。新函数的length是绑定函数的形参个数减去绑定实参的个数**

```js
var sum=function(x,y){return x+y};//返回两个实参的和值
//创建一个类似sum的新函数，但this的值绑定到null
//并且第一个参数绑定到1，这个新的函数期望只传入一个实参
var succ=sum.bind(null,1);
succ(2)//=＞3:x绑定到1，并传入2作为实参y
function f(y,z){return this.x+y+z};//另外一个做累加计算的函数
var g=f.bind({x:1},2);//绑定this和y
g(3)//=＞6:this.x绑定到1，y绑定到2，z绑定到3
```

这种绑定参数，减少新函数的参数的技术叫做**柯里化**

### 不完全函数

```js
//实现一个工具函数将类数组对象（或对象）转换为真正的数组
//在后面的示例代码中用到了这个方法将arguments对象转换为真正的数组
function array(a,n){return Array.prototype.slice.call(a,n||0);}
//这个函数的实参传递至左侧，类似于bind
function partialLeft(f/*,...*/){
	var args=arguments;//保存外部的实参数组
	return function(){//并返回这个函数
		var a=array(args,1);//开始处理外部的第1个args
		a=a.concat(array(arguments));//然后增加所有的内部实参
		return f.apply(this,a);//然后基于这个实参列表调用f()
	};
}
//这个函数的实参传递至右侧
function partialRight(f/*,...*/){
  var args=arguments;//保存外部实参数组
  return function(){//返回这个函数
    var a=array(arguments);//从内部参数开始
    a=a.concat(array(args,1));//然后从外部第1个args开始添加
    return f.apply(this,a);//最后基于这个实参列表调用f()
  };
}
//这个函数的实参被用做模板
//实参列表中的undefined值都被填充
function partial(f/*,...*/){
  var args=arguments;//保存外部实参数组
  return function(){
    var a=array(args,1);//从外部args开始
    var i=0,j=0;//遍历args，从内部实参填充undefined值
    for(;i＜a.length;i++)
    if(a[i]===undefined)a[i]=arguments[j++];//现在将剩下的内部实参都追加进去
    a=a.concat(array(arguments,j))
    return f.apply(this,a);
  };
}
//这个函数带有三个实参
var f=function(x,y,z){return x*(y-z);};//注意这三个不完全调用之间的区别
partialLeft(f,2)(3,4)//=＞-2:绑定第一个实参:2*(3-4)
partialRight(f,2)(3,4)//=＞6:绑定最后一个实参:3*(4-2)
partial(f,undefined,2)(3,4)//=＞-6:绑定中间的实参:3*(2-4)
```

### 函数记忆(缓存结果)

使用闭包的特性，记忆每次运行的结果