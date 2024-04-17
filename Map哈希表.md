# Map哈希表封装

### 哈希表介绍

哈希表是一种**数据结构**，通常基于数组实现。 我们知道，数组这种数据结构，如果已知某一项的下标值，那么查询起来的速度是非常快的，这是数组的优点。但是数组也有缺点：

- 不知道需要查询的数据的下标值时，比如存储的员工信息，只知道员工姓名，那么就不得不一个个遍历，效率不高；
- 另外，数组的插入和删除元素，也是比较消耗性能的，因为每插入或删除一个元素，那么其后面的每一项都需要往后或往前挪一项，以保证连续性。

哈希表则解决了数组中存在的上述 2 个问题。



### 哈希表优缺点

> 优点

- 哈希表可以提供非常快速的**插入-删除-查找操作**；
- 无论多少数据，插入和删除值都只需要非常短的时间，即O(1)的时间级。实际上，只需要**几个机器指令**即可完成；
- 哈希表的速度比**树还要快**，基本可以瞬间查找到想要的元素。但是相对于树来说编码要简单得多。

> 缺点

- 哈希表中的数据是**没有顺序**的，所以不能以一种固定的方式（比如从小到大 ）来遍历其中的元素。
- 通常情况下，哈希表中的key是**不允许重复**的，不能放置相同的key，用于保存不同的元素。

- 哈希化过后的下标依然可能**重复**，如何解决这个问题呢？这种情况称为**冲突**，冲突是**不可避免**的，我们只能**解决冲突**。





### 哈希函数

```js
//设计哈希函数
//1.将字符串转成比较大的数字：hashCede
//2.将大的数字hasCode压缩到数组范围(大小)之内
function hashFunc(str,size){
  let hashCode=0
  //霍纳算法
  for(let i =0;i<str.length;i++){
    // str.charCodeAt(i)//获取某个字符对应的unicode编码
    hashCode=31*hashCode+str.charCodeAt(i)
  }
  let index=hashCode%size
  return index
}
//测试
console.log(hashFunc('123',7));//5
console.log(hashFunc('ABC',7));//3
console.log(hashFunc('NBA',7));//6

```



### **哈希表的常见操作：**

- put（key，value）：插入或修改操作；
- get（key）：获取哈希表中特定位置的元素；
- remove（key）：删除哈希表中特定位置的元素；
- isEmpty（）：如果哈希表中不包含任何元素，返回trun，如果哈希表长度大于0则返回false；
- size（）：返回哈希表包含的元素个数；
- resize（value）：对哈希表进行扩容操作；

### 哈希表封装

```js
// 哈希表
const MAX_LOAD_FACTOR=0.75
const MIN_LOAD_FACTOR=0.25
class HashTable{
  constructor(){
    this.storage=[]//数组存贮元素
    this.count=0//记录元素个数
    this.limit=7//最大个数限制
  }

  //设计哈希函数
  //1.将字符串转成比较大的数字：hashCede
  //2.将大的数字hasCode压缩到数组范围(大小)之内
  hashFunc(str,size){
    let hashCode=0
    //霍纳算法
    for(let i =0;i<str.length;i++){
      // str.charCodeAt(i)//获取某个字符对应的unicode编码
      hashCode=31*hashCode+str.charCodeAt(i)
    }
    let index=hashCode%size
    return index
  }

  // 判断是否为质数
  isPrime(num){
    let temp=Math.ceil(Math.sqrt(num))
    for(let i=2;i<temp;i++){
      if(num%i===0){
        return false
      }
    }
    return true
  }

  getPrime(num){
    while(!this.isPrime(num)){
      num++
    }
    return num
  }

  //添加(更新)元素
  put(key,value){
    // 根据key映射到index
    const index=this.hashFunc(key,this.limit)
    //使用数组当容器
    let bucket=this.storage[index]
    if(bucket===undefined){
      bucket=[]
      this.storage[index]=bucket
    }
    //哈希表中键重复时会覆盖原数据
    // 判断是插入还是覆盖
    let overide=false//是否进行过覆盖
    for(let i=0;i<bucket.length;i++){
      //每个元素以数组形式存储//["name","张三"]
      let tuple=bucket[i] 
      // 判断key值是否存在
      if(tuple[0]===key){
        tuple[1]=value//更新元素
        overide=true
      }
    }
    // 插入操作
    if(!overide){
      bucket.push([key,value])
      this.count++
    
      if(this.count>this.limit*MAX_LOAD_FACTOR){
        let newLimit=this.limit*2
        this.resize(this.getPrime(newLimit))
      }
    }
  } 


  // 根据key获取value
  get(key){
    // 1.根据key获取index下标值
    const index=this.hashFunc(key,this.limit)
    // 2.根据下标找到bucket容器
    const bucket=this.storage[index]
    //不存在bucket容器
    if(bucket==undefined){
      return null
    }else{
      // 遍历bucket
      for(let i =0;i<bucket.length;i++){
        let tuple=bucket[i]
        if(tuple[0]===key){
          //找到元素返回
          return tuple[1]
        }
      }
      // 遍历结束没有找到
      return null
    }
  }

  // 根据keu移除元素
  remove(key){
    // 1.根据key获取index下标值
    const index=this.hashFunc(key,this.limit)
    // 2.根据下标找到bucket容器
    const bucket=this.storage[index]
    //不存在bucket容器
    if(bucket==undefined){
      return null
    }else{
      // 遍历bucket
      for(let i =0;i<bucket.length;i++){
        let tuple=bucket[i]
        if(tuple[0]===key){
          // 删除元素
          bucket.splice(i,1)
          this.count--
          //个数减少到一定程度时，缩小容量
          if(this.limit>8&&this.count<this.limit*MIN_LOAD_FACTOR){
            let newLimit=Math.floor(this.limit/2)
            newLimit=this.getPrime(newLimit)
            this.resize(newLimit)
          }
          //返回删除的元素
          return tuple[1]
        }
      }
      // 遍历结束没有找到
      return null
    }

  }

  isEmpty(){
    return this.count===0
  }
  size(){
    return this.count
  }

  //重置容量
  resize(newLimit){
    // 保存旧的内容
    let OldStorage=this.storage
    // 重置属性
    this.limit=newLimit
    this.storage=[]
    this.count=0
    //取出旧数据，重新放入storage
    OldStorage.forEach(item=>{
      if(item==null){
        return 
      }
      for(let i=0;i<item.length;i++){
        let tuple=item[i]
        this.put(tuple[0],tuple[1])//存放数据
      }
    })
  }

}

```

