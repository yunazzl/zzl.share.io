## js学习笔记05

### 1.&&、ll和！的妙用

&&实现简短的if表达式

```js
if (a==b) stop() //只有在a==b的时候才调用stop()
(a==b) && stop() //同上
```

ll从一组备选表达式中选出第一真值

```js
//如果max_width已经定义了，直接使用它；否则在preferences对象中查找max_width
//如果没有定义它，则使用一个写死的常亮
var max = max_width || preferences.max_width || 500
```

!首先将操作数转换为布尔值，然后在对布尔值取反。！总是返回true或false

**可以使用两次逻辑非运算得到一个值的等价布尔值：！！x**

### 2.函数声明

**函数声明不能在if、while等任何语句内。但是函数声明可以。**

```js
var func1 = function() {} //func1是变量指向函数
function func2 () {}	//func2是函数
```

**虽然两种方式都能创建新的函数，但是函数声明的是一个变量，它的声明会提前，但是定义&赋值还在原来的位置，而函数定义的函数，是整个定义被提前**

```js
function main() {
  func1() //报错，此时变量func1是undefined
  func2() //正常执行，func2声明被提前
  var func1 = function() {} //函数声明表达式
	function func2 () {}	//函数定义表达式
}
```

**和变量一样，var声明的函数变量是无法用delete删除的。但是这个变量是可读写的，变量值可以重写。**

### 3.神奇的for in

```js
var o = {x: 1, y:2, z:3}
var a = [], i=0;
for (a[i++] in o) /*empty*/;
console.log(a.toString()); //输出的是 x, y, z
```

**for in 的左值可以当做赋值表达式的左值，可以使任意表达式，每次循环都会计算这个表达式，也就是说每次循环它计算的值有可能不同。**

### 4.try、catch、finally

**当由于return、continue或break语句使得跳出try语句块，在执行新的目标代码之前先执行finally块中的逻辑**

**如果不存在catch从句，则先执行finally中的逻辑，然后向上传递这个异常**

