## js学习笔记05

### &&、||和！的妙用

&&实现简短的if表达式

```js
if (a==b) stop() //只有在a==b的时候才调用stop()
(a==b) && stop() //同上
```

||从一组备选表达式中选出第一真值

```js
//如果max_width已经定义了，直接使用它；否则在preferences对象中查找max_width
//如果没有定义它，则使用一个写死的常亮
var max = max_width || preferences.max_width || 500
```

!首先将操作数转换为布尔值，然后在对布尔值取反。！总是返回true或false

**可以使用两次逻辑非运算得到一个值的等价布尔值：！！x**

