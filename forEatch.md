# forEatch

### 基本使用

1.作用：遍历数组

```js
const array1 = ['a', 'b', 'c'];

array1.forEach(element => console.log(element));

// expected output: "a"
// expected output: "b"
// expected output: "c"
```

`forEach()` 被调用时，不会改变原数组，也就是调用它的数组（尽管 `callbackFn` 函数在被调用时可能会改变原数组）。（译注：此处说法可能不够明确，具体可参考 EMCA 语言规范：'`forEach` does not directly mutate the object on which it is called but the object may be mutated by the calls to `callbackfn`.'，即 `forEach` 不会直接改变调用它的对象，但是那个对象可能会被 `callbackFn` 函数改变。）



> 基本数据类型：

```js\
//数组-基本数据类型
let array = [1,2,3,4];

array.forEach(num => {
    if(num === 4){
        num = 5
    }    
 })
 console.log(array)  //[1,2,3,4]

```

> 复杂数据类型：

```js
        //数组-基本数据类型
        let array = [
            {
                name: '小红',
                age: 12
            },
            {
                name: '小明',
                age: 18
            },
        ];

        array.forEach(person => {
            if(person.name === '小红'){
                person.age = 15
                console.log(person === array[0])  //true
            }
        })

        console.log(array)  //[{name: '小红',age: 15},{name: '小明',age: 18},]

```

**![在这里插入图片描述](https://img-blog.csdnimg.cn/20201030145908363.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpa2Vwb3RhdG8=,size_16,color_FFFFFF,t_70#pic_center)**

此处数据发生改变，但是需要注意的是，因为对象为引用数据类型，它里面存的其实是一个指针，这个指针存在栈内存（相当于浅层的存储区，用来存一些基本的简单数据）里面，这个指针指向的是堆内存里面的实际数据，也就是说，你拿到的person不是{name: ‘小红’,age: 15}这串数据本身，而是指向这串数据的地址，forEach里面不允许改变person的值，也就是说这个地址是改变不了的，就比如里面存的是‘湖滨小区1幢204’，这个地址被保护起来了，person也就只能指向这间房间，但是如果不改变房间，把房间里住的人变了可以吗？当然可以，这个forEach就管不着了，所以改变person.age就相当于把房间里的人给换了，相当于偷梁换柱，是可以实现的，这样就改变了array。



**结论**

> **forEach不能改变数组本身，无论是基础数据类型还是引用数据类型都不可以。**



> 最好不要尝试用forEach来改变数组的值，用for循环都比它好使。要在forEach里面改变数组，需要用array[index]的方法来改变数组本身。这样就需要在forEach里面穿第二个参数index。



### 跳出forEach

```js
let arr = [...new Array(10).keys()];
try{
    arr.forEach(item=>{
        console.log(`item:${item}`)
        if(item>5) throw new Error("break");
    })
}catch(err){
    if(err.message === "break")	
        console.log("break success!")
    else 
        console.error(err)
}
```

`备注`需要注意的是：除了抛出异常以外，没有办法中止或跳出 `forEach()` 循环。如果你需要中止或跳出循环，`forEach()` 方法不是应当使用的工具。



如果需要提前终止循环，可以使用：

<ul><li>一个简单的for循环</li>
	<li>for of/for in循环</li>
    <li>使用every,some,find,findIndex判断元素后再进行遍历</li>
</ul>




### 手写forEach

```js
Array.prototype.myForEach = function (fn, thisValue) {
    if (typeof fn !== 'function') {
        throw new Error(`${fn} 不是一个函数`)
    }
    if ([null, undefined].includes(this)) {
        throw new Error(`this 是null 或者 undefined`)
    }
    const arr = Object(this)
    for (let i = 0; i < arr.length; i++) {
        fn.call(thisValue, arr[i], i, arr)
    }
}

```



