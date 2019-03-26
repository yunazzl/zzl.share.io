## 客户端js01

### Window

window定义了location对象指定当前显示窗口的URL。

还定义了alert、setTimeout方法

```js
setTimeout(function(){
  alert(‘helloworld’)；
},2000)
```

这里没有显示的使用window，因为window是全局属性。他处于作用域链的最顶部

window还定义了document指定当前窗口的文档

