## CommonJS规范

### 模块

在js没有内置模块系统的时候，CommonJS创造了自己的

math.js

```js
exports.add = function() {
  var sum = 0, i = 0, args = arguments, l = args.length;
  while (i<l) {
    sum += args[i++];
  }
  return sum;
}
```

increments.js

```js
var add = require('matn').add;
exports.increments = function(val) {
  return add(val, 1);
}
```

program.js

```js
var inc = require('increment').increment;
var a = 1;
inc(a);//2
```

仔细阅读上面的代码，会发现require是同步的。也就是说，系统需要在require方法返回之前，就判断给定模块是否可用(并初始化它，**也就是说模块的代码在被require的时候执行**)。

在浏览器中，js代码通常都是在document中插入\<script\>标签。但这是异步加载的，所以require在浏览器环境中无法正常加载。

