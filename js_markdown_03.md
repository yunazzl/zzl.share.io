## js学习笔记03

### 1.作用域

局部变量与全局变量重名时，局部变量覆盖全局变量

js里的局部变量，是在函数作用域里的（不存在块级作用域，代码块内声明的变量作用域也是整个函数区）

```js
function test(o) {
    var s = "scope" //作用域是整个test函数
    if (typeof o == 'object') {
        var j = 'object' //作用域是整个test函数
    }
    for (var k=0; k<10; k++) { //k的作用域是整个test函数
        console.log(k)
    }
    console.log(j) //j 可能是’object'，也可能是undefined
}
```

js声明会提升到作用域开头（**声明提前**），所以以上也可以写作

```js
function test(o) {
    var s
    var j
    var k
    s = 'scope'
    if (typeof o == 'object') {
        j = 'object'
    }
    for (k=0; k<10; k++) {
        console.log(k)
    }
    console.log(j)
}
```

```js
var scope = 'global'
function f() {
    console.log(scope); //由于声明提前，这里是undefined，不是全局变量global
    var scope = 'local' 
    console.log(scope);//输出local
}
```

### 2.作为属性的变量

当声明一个js全局变量时，实际上它就是全局对象的一个属性。

var声明的一个变量，是全局对象的不可配置属性，无法通过delete删除

隐式（不使用var）声明的全局变量，是全局对象的可配置属性，可以删除

```js
var truevar = 1; //声明一个不可删除的全局变量
fakevar = 2; //创建全局对象的一个可删除的属性
this.fakevar2 = 3; //同上
delete truevar //=>false 变量并没有被删除
delete fakevar //=>true 变量被删除
delete this.fakevar2 //=>true 变量被删除
```

### 3.作用域链

当声明一个函数的时候，它存储一个作用域链（内部的作用域链）

当调用一个函数时，就会生成一个新的对象来存储它的局部变量，并将这个对象添加至作用域链上（顶端）。如果是最顶层的调用

对于嵌套函数，每次外部函数被调用时，内部函数又会被重新定义一次。所以每次调用外部函数的时候，作用域链都会不同。

**每次调用外部函数的时候，内部函数代码虽然是相同的，但是关联的这段代码的作用域链是不同的**

