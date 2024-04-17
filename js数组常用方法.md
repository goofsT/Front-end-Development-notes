# js数组方法

### 改变原数组

> 1.`pop()`:pop(): 删除 数组的最后一个元素，把数组长度减 1，并且返回它删除的元素的值。如果数组已经为空，则 pop() 不 改变数组，并返回 undefined 值。

```js
const arr = [2,3]
const rs = arr.pop()
console.log(rs) //3
console.log(arr) // [2]
```

> 2.`push()`:push() 方法可把它的参数顺序添加到数组 的尾部。它直接修改 数组，而不是创建一个新的数组。

```js
let arr = [2,3]
let rs = arr.shift() 
console.log(rs) //2
console.log(arr) // [3]
```

> 3.`shift()`:从数组的第一个元素从其中删除，并返回第一个元素的值,如果数组是空的，那么 shift() 方法将不进行任何操作.

```js
let arr = [2,3]
let rs = arr.shift() 
console.log(rs) //2
console.log(arr) // [3]
```

> 4.`unshift()`:unshift() 方法可向数组的头部添加一个或更多元素，并返回新数组的长度。

```js
let arr=[]
arr.unshift(1,2,3)
console.log(arr)//[1,2,3]
```

> 5.`reverse()`:翻转数组，用户颠倒数组中元素的顺序

```js
let arr=[1,2]
arr.reverse()
console.log(arr)//[2,1]
```



> 6.`sort()`:在原数组基础上进行排序，如果没有指明 `compareFn` ，那么元素会按照转换为的字符串的诸个字符的 Unicode 位点进行排序。例如 "Banana" 会被排列到 "cherry" 之前。当数字按由小到大排序时，9 出现在 80 之前，但因为（没有指明 `compareFn`），比较的数字会先被转换为字符串，所以在 Unicode 顺序上 "80" 要比 "9" 要靠前。



字母：

```js
let arr=['x','a','c']
arr.sort()
console.log(arr)//['a','c','x']
```

数字：

```js
let arr=[11,2,3,5,9,8]
arr.sort()
console.log(arr)//[11,2,3,5,8,9]
```

可以发现，当排序为数字时，或者字母分大小写时，sort会缺乏稳定性，所以需要指定按某种顺序进行排列的函数:`compareFn`

```js
// 无函数
sort()
// 箭头函数
sort((a, b) => { /* … */ } )
// 比较函数
sort(compareFn)
// 内联比较函数
sort(function compareFn(a, b) { /* … */ })
```

a:第一个用于比较的元素



b:第二个用于比较的元素



​	

<ul>
        <li>如果 compareFn(a, b) 大于 0，b 会被排列到 a 之前。</li>
        <li>如果 compareFn(a, b) 小于 0，那么 a 会被排列到 b 之前；</li>
        <li>如果 compareFn(a, b) 等于 0，a 和 b 的相对位置不变。备注：ECMAScript 标准并不保证这一行为，而且也不是所有浏览器都会遵守（例如 Mozilla 在 2003 年之前的版本）；</li>
        <li>compareFn(a, b) 必须总是对相同的输入返回相同的比较结果，否则排序的结果将是不确定的。</li>
    </ul>



> 比较函数：

```js
function compareFn(a, b) {
  if (在某些排序规则中，a 小于 b) {
    return -1;//小于0,b在a前
  }
  if (在这一排序规则下，a 大于 b) {
    return 1;//大于0,a在b前
  }
  // a 一定等于 b
  return 0;
}
```





> 7.`splice()`:splice 方法通过删除或替换现有元素或者原地添加新的元素来修改数组，并以数组形式返回被修改的内容。此方法会改变原数组.



语法:

```js
splice(start)
splice(start, deleteCount)
splice(start, deleteCount, item1)
splice(start, deleteCount, item1, item2, itemN)
```

(1)start:指定修改的开始位置（从 0 计数）。如果超出了数组的长度，则从数组末尾开始添加内容；如果是负值，则表示从数组末位开始的第几位（从 -1 计数，这意味着 -n 是倒数第 n 个元素并且等价于 `array.length-n`）；如果负数的绝对值大于数组的长度，则表示开始位置为第 0 位。



(2)整数，表示要移除的数组元素的个数。如果 `deleteCount` 大于 `start` 之后的元素的总数，则从 `start` 后面的元素都将被删除（含第 `start` 位）。如果 `deleteCount` 被省略了，或者它的值大于等于`array.length - start`(也就是说，如果它大于或者等于`start`之后的所有元素的数量)，那么`start`之后数组的所有元素都会被删除。如果 `deleteCount` 是 0 或者负数，则不移除元素。这种情况下，至少应添加一个新元素。



(3)`item1, item2, ...` 可选

要添加进数组的元素，从`start` 位置开始。如果不指定，则 `splice()` 将只删除数组元素。

(4)返回值，由被删除的元素组成的一个数组。如果只删除了一个元素，则返回只包含一个元素的数组。如果没有删除元素，则返回空数组。

```js
var myFish = ["angel", "clown", "mandarin", "sturgeon"];
var removed = myFish.splice(2, 0, "drum");

// 运算后的 myFish: ["angel", "clown", "drum", "mandarin", "sturgeon"]
// 被删除的元素：[], 没有元素被删除

```







### 不改变原数组

> 1.`concat()`：用于连接两个或多个数组，仅会返回连接后的结果数组.

```js
let arr=[1,2]
let newArr=arr.concat([3,4])
console.log(newArr)//[1,2,3,4]
```

> 2.`join()`:法将一个数组（或一个[类数组对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Indexed_collections#使用类数组对象_array-like_objects)）的所有元素连接成一个字符串并返回这个字符串，用逗号或指定的分隔符字符串分隔。如果数组只有一个元素，那么将返回该元素而不使用分隔符。

```js
const arr=["h","e","l","l","o"]
console.log(arr.join(' '))//hello
```

（1）参数：`arr.join(separator)`,指定一个字符串来分隔数组的每个元素。如果需要，将分隔符转换为字符串。如果省略，数组元素用逗号（`,`）分隔。如果 `separator` 是空字符串（`""`），则所有元素之间都没有任何字符。



（2）返回值：一个所有数组元素连接的字符串。如果 `arr.length` 为 0，则返回空字符串。



> 3.`filter()`:新数组中的元素是通过指定数组中符合的条件筛选出来的。方法创建给定数组一部分的[浅拷贝](https://developer.mozilla.org/zh-CN/docs/Glossary/Shallow_copy)，其包含通过所提供函数实现的测试的所有元素。不会检测空数组。

```js
const arr=[1,2,3,4,5,6]
const result=arr.filter(item=>item>3)
console.log(result)//[4,5,6]
```

语法：

```js
// 箭头函数
filter((element) => { /* … */ } )
filter((element, index) => { /* … */ } )
filter((element, index, array) => { /* … */ } )

// 回调函数
filter(callbackFn)
filter(callbackFn, thisArg)

// 内联回调函数
filter(function(element) { /* … */ })
filter(function(element, index) { /* … */ })
filter(function(element, index, array){ /* … */ })
filter(function(element, index, array) { /* … */ }, thisArg)

```

(1).callbackFn:用来测试数组中每个元素的函数。返回 `true` 表示该元素通过测试，保留该元素，`false` 则不保留。它接受以下三个参数：



>element:数组正在处理的元素

>index:正在处理的元素的索引

> arr:调用了filter()方法的数组本身



(2).thisArg:执行callbackFn时,用于指定this的值



> 4.`slice()`: 截取数组，方法返回一个新的数组对象，这一对象是一个由 `begin` 和 `end` 决定的原数组的**浅拷贝**（包括 `begin`，不包括`end`）。原始数组不会被改变。

```js
const animals = ['ant', 'bison', 'camel', 'duck', 'elephant'];

console.log(animals.slice(2));
// expected output: Array ["camel", "duck", "elephant"]

console.log(animals.slice(2, 4));
// expected output: Array ["camel", "duck"]

console.log(animals.slice(1, 5));
// expected output: Array ["bison", "camel", "duck", "elephant"]

console.log(animals.slice(-2));
// expected output: Array ["duck", "elephant"]

console.log(animals.slice(2, -1));
// expected output: Array ["camel", "duck"]

```

**参数**



(1)begin:提取起始处的索引（从 `0` 开始），从该索引开始提取原数组元素。如果该参数为负数，则表示从原数组中的倒数第几个元素开始提取



(2)end(可选):提取终止处的索引（从 `0` 开始），在该索引处结束提取原数组元素。`slice` 会提取原数组中索引从 `begin` 到 `end` 的所有元素（**包含 `begin`，但不包含 `end`**）



> 5.`find()`:返回数组中满足条件的第一个元素的值，否则返回undefined.

```js
const arr=[1,2,3,4,5]
const result=arr.find(item=>item<3)
console.log(result)//1
```

> 6.`findIndex()`,返回数组中满足条件的第一个元素的索引，否则返回-1

```js
const arr=[1,2,3]
const result=arr.findIndex(item=>item===2)
console.log(result)//1
```

> 7.`indexOf()`:方法返回在数组中可以找到给定元素的第一个索引，如果不存在，则返回 -1。

```js
const arr=[1,2,3]
const result=arr.indexOf(2,1)
const result2=arr.indexOf(2,2)
console.log(result)//1
console.log(regult2)//-1
```

参数：

(1)searchElement:要查找的元素。



(2)fromIndex(可选)：开始查找的位置。如果该索引值大于或等于数组长度，意味着不会在数组里查找，返回 -1。如果参数中提供的索引值是一个负值，则将其作为数组末尾的一个抵消，即 -1 表示从最后一个元素开始查找，-2 表示从倒数第二个元素开始查找，以此类推。注意：如果参数中提供的索引值是一个负值，并不改变其查找顺序，查找顺序仍然是从前向后查询数组。如果抵消后的索引值仍小于 0，则整个数组都将会被查询。其默认值为 0。



indexOf与findIndex的区别：

- `indexOf` ：查找值作为第一个参数，采用 `===` 比较，更多的是用于查找基本类型，如果是对象类型，则是判断是否是同一个对象的引用

- `findIndex` ：比较函数作为第一个参数，多用于非基本类型(例如对象)的数组索引查找，或查找条件很复杂

  

> 8.`includes()`:判断一个数组是否包含一个指定的值，根据情况，如果包含则返回 `true`，否则返回 `false`。

```js
const arr=["ax","bx","cs"]
const result=arr.includes("ax",1)
console.log(result)//false
```



> 9.`some()`:判断数组中是不是至少有 1 个元素通过了被提供的函数测试。它返回的是一个 Boolean 类型的值。

```js
function isBiggerThan10(element, index, array) {
  return element > 10;
}

[2, 5, 8, 1, 4].some(isBiggerThan10);  // false 
[12, 5, 8, 1, 4].some(isBiggerThan10); // true

[2, 5, 8, 1, 4].some(x => x > 10);  // false
[12, 5, 8, 1, 4].some(x => x > 10); // true
```

> reduce实现根据属性进行分组

```js
arr=[
    {name:'tt',age:20,id:1},
    {name:'ss',age:20,id:2},
    {name:'xx',age:30,id:3},
    {name:'aa',age:30,id:4},
    {name:'bb',age:20,id:5},
]
const result=arr.reduce((pre,cur)=>{
    const index=pre.findIndex(v=>v.age===cur.age)
	if(index>=0){
		pre[index].list.push(cur)
	}else{
		pre.push({
			age:cur.age,
			list:[cur]
		})
	}
return pre
},[])


结果:
[
    {age:20,list:[
         {name:'tt',age:20,id:1},
    	 {name:'ss',age:20,id:2},
         {name:'bb',age:20,id:5},
    ]},
     {age:30,list:[
        {name:'xx',age:30,id:3},
    	{name:'aa',age:30,id:4},
    ]},
    
]
```

>reduce实现数组外壳转对象

```js
const result=arr.reduce((pre,cur)=>{
    pre[cur.id]=cur
    return pre
},{})

结果
{
    "1": {name:'tt',age:20,id:1},
    "2": {name:'ss',age:20,id:2},
}
```

```
1、map速度比forEach快

2、map会返回一个新数组，不对原数组产生影响,foreach不会产生新数组，forEach返回undefined

3、map因为返回数组所以可以链式操作，forEach不能

4, map里可以用return（return的是什么，相当于把数组中的这一项变为什么（并不影响原来的数组，只是相当于把原数组克隆一份，把克隆的这一份的数组中的对应项改变了） ,而forEach里用return不起作用，forEach不能用break，会直接报错

```
