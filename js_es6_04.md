## ES6学习笔记04

### 异步操作&Async函数

```js
var asyncReadFile = async function(){
  var f1 = await readFile('/etc/fstab')
  var f2 = await readFile('/etc/shells')
  console.log(f1.toString())
  console.log(f2.toString())
}
```

