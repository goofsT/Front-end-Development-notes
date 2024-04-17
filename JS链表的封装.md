# JS单向链表的封装

### 链表介绍

> 链表相对于数组的优点在于：

- 内存空间不是必须连续的，可以充分利用计算机的内存，实现灵活的内存动态管理。
- 链表不需要再创建的时候就确定大小，并且它的大小可以无限的延伸下去。
- 链表在**插入和删除**数据时，时间复杂度(即执行算法所需要的计算工作量)可以达到`O(1)`，相对数组效率高许多。

> 链表相对于数组的缺点在于：

- 链表访问任何一个位置的元素时，都需要从头开始访问
- 无法像数组一样通过下标值访问元素，需要从头开始访问，直到找到对应的元素



> 单向链表可以理解为由头指针(head)和节点构成(node),当head为null时，表示链表长度为0

> 一个节点包含数据(item)+指向下一个节点的指针(next),当next为null时,表示到达尾部



### 封装链表

1.链表常见操作：



 <ul>
        <li>append(element)：向列表尾部添加一个新的元素</li>
        <li>insert(position, element)：向列表的某个位置插入一个新的元素</li>
        <li>get(position)：获取对应位置的元素</li>
        <li>indexOf(element)：返回元素在列表中的索引。如果没有该元素则返回-1</li>
        <li>update(position, data)：修改某个位置的元素的data值</li>
        <li>removeAt(position)：从列表的某个位置移除一个元素</li>
        <li>remove(data)：从列表中移除一个元素</li>
        <li>isEmpty()：如果链表中没有元素，返回true。否则返回false</li>
        <li>size()：返回链表中包含的元素个数，和数组的length属性类似</li>
        <li>toString()：链表中元素是Node类，需要重写toString方法，方便输出打印元素的值。</li>
    </ul>

```js
class Node {
    constructor(item) {
        this.item = item
        // 指向下一个节点
        this.next = null
    }
}
class LinkedList {
    constructor() {
        this.head = null
        this.length = 0
    }

    // 尾部追加元素
    append(item) {
        // 创建节点对象
        const node = new Node(item)
        if (!this.head) {//head没有指向，说明没有元素
            this.head = node
        } else {
            //不为空，已经存在其他节点
            // 循环判断是否有next
            let current = this.head
            while (current.next) {
                current = current.next
            }
            //让最后一个node的next指向当前添加的node
            current.next = node
        }
        this.length++
    }

    // 插入元素到指定位置
    insert(position, item) {
        // 越界
        if (position < 0 || position > this.length) return false
        const node = new Node(item)
        if (position === 0) {
            // 插入到最前面，让新node指向head所指向的旧node（旧node后移）
            node.next = this.head
            // head指向当前新node
            this.head = node
        } else {
            let index = 0
            let current = this.head //当前指向
            let previous = null //当前指向的前一个
            // 一直往下找，直到current指向为position位置元素
            while (index++ < position) {
                previous = current
                current = current.next
            }
            // 前一个ndoe的next指向新node
            previous.next = node
            // 新node的next执行current(position位置元素)
            node.next = current
        }
        this.length++
        return true
    }

    // 获取对应位置元素
    get(position) {
        // 越界
        if (position < 0 || position > this.length - 1) return null
        let index = 0
        let current = this.head
        while (index++ !== position) {
            current = current.next
        }
        return current
    }

    // 获取元素位置,没有返回-1
    indexOf(item) {
        if (this.head === null) return -1
        let current = this.head
        let position = -1
        for (let i = 0; i < this.length; i++) {
            if (current.item === item) {
                position = i
            }
            current = current.next
        }
        return position
    }

    // 更新元素(返回更新前元素)
    update(position, item) {
        if (position < 0 || position > this.length - 1) return null
        // 删除指定位置node
        const result = this.removeAt(position)
        // 插入新node
        this.insert(position, item)
        return result
    }

    // 指定位置移除元素
    removeAt(position) {
        if (position < 0 || position > this.length - 1) return null
        let current = this.head
        let previous = null
        // 删除第一个
        if (position === 0) {
            // 让头指针指向删除项的下一个node
            //此时第一个node未被引用，内存自动回收
            this.head = current.next
        } else {
            for (let i = 0; i <= position - 1; i++) {
                previous = current
                current = current.next
            }
            previous.next = current.next
        }
        this.length--
        return current.item
    }
    // 移除指定元素
    remove(item) {
        const position = this.indexOf(data)
        if (position === -1) {
            return null
        } else {
            return this.removeAt(position)
        }
    }
    // 是否为空
    isEmpty() {
        return this.head === null ? true : false
    }
    // 返回链表长度
    size() {
        return this.length
    }
    toString() {
        let current = this.head
        let listString = ''
        while (current) {
            listString += current.item + ''
            current = current.next
        }
        return listString
    }
}
```

测试代码：

```js

const test=new LinkedList()
// 添加
test.append("aaa")
test.append("ccc")
// 插入
test.insert(0,"bbb") 
console.log(test);//LinkedList{bbb=>aaa=>ccc}
// 获取指定位置元素
console.log(test.get(1)); //Node{item:"aaa",next:Node}
// 获取元素位置
console.log(test.indexOf("aaa"));//1
// 移除指定位置元素
console.log(test.removeAt(1));//aaa
console.log(test);//LinkedList{bbb=>ccc}
// 更新元素
test.update(0,"aaa")
test.update(1,"bbb")
console.log(test);//LinkedList{aaa=>bbb}
//toString
console.log(test.toString());//aaabbb

```

