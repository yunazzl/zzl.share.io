## js学习笔记07

### 数组

```js
var a = new Array(5, 4, 3, 2, 1, 'testing, testing')   //[4, 3, 2, 1, 'testing, testing'] length是5
```

**设置一个较小的length会截断后面的元素**

```js
var a=[1,2,3,4,5];
a.length-=1;
a.length+=1;console.log(a)//[1,2,3,4,empty]
```

数组也是对象，所以对象访问属性的行为它都有，如果数组访问的属性是一个索引，数组会把它当做索引处理，更新length

**所以js数组没有越界错误，只是会返回undefined**

**数组的索引可以使负数或非整数（不能转换为整数，1.0可以转换为整数，还是可以做索引的），它们会被处理成属性的访问，不会更新length**

delete一个数组元素，不会修改length的长度，只会产生稀疏数组

**join()把数组联合成一个字符串，参数是连接的字符。undefined&null会被当做空字符串连接**

**reverse()将数组中的元素颠倒顺序**

```js
var a=[1, 2, 3]
a.reverse().join() //=> '3,2,1' 现在的a是[3,2,1]
```

**sort()按字母表升序排序（非字符转换成字符比较），如果包含undefined，会被排到最后**

**sort()还可以传入一个比较函数作为参数，自定义排序。如果希望第一个元素在前，则返回小于0的数，反之返回一个大于零的数。相等就返回0**

```js
var a = ['banana', null, 'cherry', 'apple', 3, 111];
console.log(a.sort().join()) //=>111,3,apple,banana,cherry, undefined排在了最后，当做空字符
var b = [3, 2222, 11];
b.sort(function(x,y){return y-x}) //降序 [222，11，3]
```

**concat()创建并返回一个新数组。包括该数组本身的元素和参数元素(参数可以使元素，也可以是数组)**

```js
var a=[1,2,3]
a.concat(4, 5)//返回[1,2,3,4,5]
a.concat([4, 5])//返回[1,2,3,4,5]
a.concat([4, 5], [6, 7])//返回[1,2,3,4,5,6,7]
a.concat(4, [5, [6, 7]])//返回[1,2,3,4,5,[6,7]]
```

**slice()返回指定的子数组(参数是开始位置和结束位置，子数组不包括结束位置)**

```js
var a=[1,2,3,4,5]
a.slice(0,3)//返回[1,2,3]
a.slice(3)//返回[4,5]
a.slice(1, -1)//返回[2,3,4]
a.slice(-3, -2)//返回[3]
```

**splice()在数组中插入或删除数据的通用方法，可以保持数组连续性和更新length**

```js
var a=[1,2,3,4,5,6,7,8]
a.splice(4)//返回[5,6,7,8];a是[1,2,3,4]
a.splice(1,2)//返回[2,3];a是[1,4] (结果是根据上一行的a的数据来的，而不是初始化a的数据)
a.splice(1,1)//返回[4];a是[1]
var b=[1,2,3,4,5]
b.splice(2,0,'a','b')//返回[];a是[1,2,'a','b',3,4,5]
b.splice(2,2,[1,2],3)//返回['a','b'];a是[1,2,[1,2],3,3,4,5]
```

**push()返回length，pop()返回元素**

```js
var stack=[]
stack.push(1,2)//[1,2] 返回2
stack.pop()//[1] 返回2
stack.push(3)//[1,3] 返回2
stack.pop()//[1] 返回3
stack.push([4,5])//[1,[4,5]] 返回2
stack.pop()//[1] 返回[4,5]
stack.pop()//[] 返回1
```

**unshift()和shift()方法类似于push和pop，只是它们是从头插入删除**

```js
var a=[]
a.unshift(1)//[1] 返回1
a.unshift(22)//[22,1] 返回2
a.shift()//[1] 返回22
a.unshift(3,[4,5])//[3,[4,5],1] 返回3
a.shift()//[[4,5],1] 返回3
a.shift()//[1] 返回[4,5]
a.shift()//[] 返回1
```

**toString()**

```js
[1,[2,'c']].toString() //'1,2,c'
```

### 以下是ES5新增的方法

它们的参数是一个数组，这个数组有三个参数，分别是：元素、索引、数组本身，如果是稀疏数组，空元素不会遍历。但是map遇到空元素依然产生空元素。

**forEach遍历数组**

```js
var data = [1,2,3,4,5]
var sum = 0
data.forEach(function(value, index, array){array[index]=value+1})
data//=>[2，3，4，5，6]
```

**map()一对一产生新数组**

```js
var a=[1,2,3]
b=a.map(function(x){return x*x})//b:[1,4,9]
```

**filter()通过过滤产生子集数组，新数组总是稠密的**

```js
var a=[1,2,3,4,5]
b=a.filter(function(x){return x<3})//b:[1,2]
```

**every()是所有元素满足条件，some()是至少有一个满足条件。它们都返回布尔值**

空元素不参与判断

空数组，every()返回true，some()返回false

```js
console.log([1,2,3,,,,5].every((x)=>{return x>0})) //true
console.log([1,2,3,,,,5].some((x)=>{return x<2})) //true
```

**reduce()和reduceRight()使数组元素组合成一个值**

两个参数function(x,y)和初始值

```js
var a=[1,2,3,4,5];
console.log(a.reduce((x,y)=>{return x+y})) //15
console.log(a.reduce((x,y)=>{return x+y}, 1))//16
console.log(a.reduce((x,y)=>{return x+y}, ''))//'12345'
console.log(a.reduce((x,y)=>{return x>y?x:y}, ''))//5 求最大值
console.log(a.reduceRight((x,y)=>{return x+y}, ''))//'54321'
```

**indexOf和lastIndexOf()**

```
var a=[0,1,2,1,0]
a.indexOf(1)//=>1
a.lastIndexOf(1)//=>3
a.indexOf(3)//=>-1
```

它们有两个参数，第二个参数是搜索的其实index位置

**Array.isArray()判断是否是数组类型**

```
Array.isArray([])//=>true
Array.isArray({})//=>false
```

### ES6扩展

**Array.from()：将类数组川味真正的数组**

```js
let arrayLike = {
	'0': 'a',
  '1': 'b',
  '2': 'c',
  length: 3
}
//ES5的写法
let arr1 = [].slice.call(arrayLike);//['a', 'b', 'c']
//ES6的写法
let arr2 = Array.from(arrayLike);//['a', 'b', 'c']
//ES6数据结构方法--扩展
let [...arr3] = [...arrayLike];//['a', 'b', 'c']
let arr4 = [...arrayLike];//['a', 'b', 'c']
```

Array.from()可以接收第二个参数，实现类似map的方法

```js
let spans = document.querySelectorAll('span.name')

//map()
let names1 = Array.prototype.map.call(spans, s=>s.textContect);

//Array.from()
let names2 = Array.from(spans, s=>s.textContent);
```

Array.from()还可以接收第三个参数，用于map函数的this

**Array.of():将一组值，组装成数组**

```js
Array.of(3, 11, 8) //[3, 11, 8]
Array.of(3) //[3]
Array.of() //[]
```

替代方法

```js
function ArrayOf() {
	return [].slice.call(arguments)
}
```

**copyWithin()数组内部复制**

copyWithin(target, start=0, end=this.length)

target：被覆盖的起始位置

start：复制的起点

end：复制的终点

```js
[1, 2, 3, 4, 5, 6].copyWithin(0, 3, 5)
//[4, 5, 3, 4, 5, 6] ,取3-5元素[4, 5]，从0开始覆盖
```

**find()和findIndex()**

find找到符合条件的第一个元素，参数就是判断条件(函数)，没有找到则返回undefined

```js
[1, 4, -5, 10].find( n=>n<0 ) //-5
```

findIndex找到符合条件的第一个元素的位置，参数就是判断条件(函数)，没有找到则返回-1

```js
[1, 4, -5, 10].findIndex( n=>n<0 ) //2
```

**fill()填充数组**

fill(value, start=0, end=this.length)

```js
['a', 'b', 'c'].fill(7) //[7, 7, 7]
['a', 'b', 'c'].fill(7, 1, 3) //['a', 7, 'c']
```

**includes()**

```js
[1, 2, 3].includes(2) //true
[1, 2, 3].includes(4) //false
[1, 2, NaN].includes(NaN) //true
```

可以传入第二个参数，作为搜索起始位置

```js
[1, 2, 3].includes(3, 3) //false
[1, 2, 3].includes(3, -1) //true
```

