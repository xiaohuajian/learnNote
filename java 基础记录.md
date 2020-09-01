[TOC]

java 对象引用是指针传递的，比如：

```
    public static void main(String[] args) {
        Person person0 = new Person("sjh", "w");
        Person person1 = person0;
        Person person2 = person0;

        person1.setName("shijianhua");
        System.out.println(person2.getName());
    }
    打印输出
    shijianhua
    
    说明p1的改变导致p0 的改变，p0的改变也影响了p2，即p0 p1 p2 是同一个对象。
```

### 移位运算符

二进制的位，可以单独用移位运算符来处理int型整数。而且位运算更加高效。

| 运算符 | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| <<     | 左移运算符，将运算符左边的对象向左移动运算符右边指定的位数（在低位补0） |
| >>     | "有符号"右移运算 符，将运算符左边的对象向右移动运算符右边指定的位数。使用符号扩展机制，也就是说，如果值为正，则在高位补0，如果值为负，则在高位补1. |
| >>>    | "无符号"右移运算 符，将运算符左边的对象向右移动运算符右边指定的位数。采用0扩展机制，也就是说，无论值的正负，都在高位补0 |

**左移运算符** 

向左移n 位，就相当于乘上2 的n 次方

2 << 1  = 4; 

分析： 2 用二进制表示为 0000 0010；向左移动 1位，变为 0000 0100 = 4； 

**带符合 右移运算符  >>  ==(二进制最高位为符号位，正数为 0，负数用1 表示)==**

-2 >> 1 = -1; 2 >> 2 = 0 ; 2 >> 3 = 0;

分析：这里暂不分析负数，在计算机组成原理讲解了 负数通过补码进行计算的，整个过程比较复杂，这里我们就看正数，`2 >> 2`

0000 0010 右移两位 ，高位补0 ，变为 0000 0000 ，所以为0 ，同时右移三位也是为 0；

**无符号 右移运算符 >>>** 

同理，这个无符号右移运算 也是为了负数考虑，但涉及到负数比较复杂，这个好好好的把计算机内部 原码、补码、反码逻辑搞清楚，才能弄清楚这些东西。

### 位运算

- 与（ & ）每一位进行比较，两位都为1，结果为1，否则为0（4 & 1 = 0  ，4的二进制表示0100 ， 1的二进制 0001，运算之后位 0000 所以为0 ）==eg：判断n为偶数与否，可以 n & 1 == 0，true 就是偶数，false 为奇数==
- 或（ | ）每一位进行比较，两位有一位是1，结果就是1（4 | 1 = 3 ，0100 和 0001 结果为 0101= 3）
- 非（ ~ ） 每一位进行比较，按位取反（符号位也要取反）（~ 4 = -3 ，4的十六位 二机制 0000 0000 0000 0100， 按位取反 1111 1111 1111 1011  由于这个二进制最高位为1 表示负数，然后求直值（由于java用补码表示，所以求直值，求反加一，符号位除外）1000 0000 0000 0100 ，再加1，1000 0000 0000 0101 = -5 ）
- 异或（ ^ ）每一位进行比较，相同为0，不同为1（2^3 =  ; 0010 ^ 0101 = 0101 = 3 ）

位运算属于底层运算，效率比较高。同时，在jdk 和sdk 中，源码用的很多，另外，与 & 、或 ｜ 用的比较多。



### Java 除法

在java 中，整数相除，如果除不尽，没有特别申明，结果直接舍弃小数部分，直接拿到结果。比如说

5/3 = 1； 3/2 =1 ； 4/3 = 1 这个东西要明白，因为在二分法的时候经常会用到。





容器类问题大致集中Hashmap相关的，有这么几个问题：



1. 谈谈你理解的 HashMap，讲讲其中的 get put 过程。
2. 为啥重写equals方法就必须重写hashCode方法？重写hashcode 却不必重写equals 方法？
3. 1.8 做了什么优化？
4. 是线程安全的嘛？
5. 不安全会导致哪些问题？
6. 如何解决？有没有线程安全的并发容器？
7. ConcurrentHashMap 是如何实现的？ 1.7、1.8 实现有何不同？为什么这么做？

### hash 与HashMap

哈希 hash

定义：**把任意长度的输入，通过散列算法，变换成固定长度的输出，该输出就是散列值**

特点：相同的输入得到相同的值，不同的输入可能得到相同的输出——这就是碰撞。

算法：

> 数字分析法：提取关键字中取值比较均匀的数字作为哈希地址。
>
> 除留余数法：用关键字k除以某个不大于哈希表长度m的数p，将所得余数作为哈希表地址。
>
> 分段叠加法：按照哈希表地址位数将关键字分成位数相等的几部分，其中最后一部分可以比较短。然后将这几部分相加，舍弃最高进位后的结果就是该关键字的哈希地址。

衡量一个哈希函数的好坏的重要指标就是发生碰撞的概率以及发生碰撞的解决方案。常见的解决碰撞的方法有以下几种：

- 开放定址法:一旦发生了冲突，就去寻找下一个空的散列地址，只要散列表足够大，空的散列地址总能找到，并将记录存入
- 再哈希法 ：当哈希地址发生冲突用其他的函数计算另一个哈希函数地址，直到冲突不再产生为止
- 链地址法：将哈希表的每个单元作为链表的头结点，所有哈希地址为i的元素构成一个同义词链表。即发生冲突时就把该关键字链在以该单元为头结点的链表的尾部；**这种方式就是hashMap采用的链表法**

在Java中，保存数据有两种比较简单的数据结构：数组和链表。**数组的特点是：寻址容易，插入和删除困难；而链表的特点是：寻址困难，插入和删除容易**，于是就把这两种数据结构的优点结合起来。

![pic](http://www.hollischuang.com/wp-content/uploads/2018/03/640.png)

hash方法的功能是根据Key来定位这个K-V在链表数组中的位置的。也就是hash方法的输入应该是个Object类型的Key，输出应该是个int类型的数组下标 , 为什么呢？ **因为hashMap存储元素的位置是在数组中记录的，所以我们需要把object类型的key 转换为 数组的下标。** 那应该怎么去转换呢？那转换时，肯定要考虑以下方面，在把键key转换为数组下标时，得出的结果分布均匀减少冲突并且得到的int 不能超过数组长度。



由此，我们把上述转换过程拆分为2步，

- 把键转换为整数
- 调整数的范围为数组的长度内 0 ～ M （M 的取值很有讲究，因为M的取值会影响到碰撞概率）

**如何把键转为整数？**

对于不同类型的key，这个方法肯定是不一样的，比如说浮点数、字符、字符串等等，具体选择什么算法进行转换，就不在深究，感兴趣的可以看源码 [link](https://mp.weixin.qq.com/s/R1Fs8qCLbdT2r3qrph0Knw) 。简单数一下，浮点数可以乘以一个数，取整数部分；字符用ACSII码，字符串采用的是霍纳法则（多项式求值）；所有这些都是为了让key转换为下标后，尽量不碰撞，同时尽可能的在数组内分布均匀。(有几个标准：让输入的每一项尽可能的参与转换运算，一般的会选用质数，比如absractList)

**解决散列碰撞**

上面说了假如你的数组长度小于你要存放的内容，必要会碰撞。举例：用散列表来存放姓名及其对应的电话号码，现在“张三”和“李四”转变出来的数组下标都是0，你怎么知道我给你的到底是张三还是李四？还是说都是同一个人。所以说**要真正使用散列表，不但要提供一个散列函数，还要提供一个“当两个‘键’的散列值相同的时候，如何判断两个‘键’到底是否相等”的方法。**在Java里面，系统定义了equals()方法来判断两个散列值相同的“键”是不是真的相等。以String类型为例子，以下是String类的equals()方法：

```
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        // 两个长度相同，且每个字符均相同的字符串，认为是相等的
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

把两个字符串的每个字符进行比较，均相同才视为相等。在实际业务中，我们可以根据需求来复写equals方法，并定义相等的要求，比如说 有一个person 类，包含姓名、年龄、id ；我们可以只去判断id 是否一样来说两个person是否相等；也可以把姓名、年龄、id 都进行比较。

从逻辑的流程来说，当向散列表存放键值对的时候，首先通过hashCode()获取“键”对应的整数，然后通过取余计算得到数组的下标，如果这个下标里面已经有元素了，就通过equals()方法判断两个元素是否相等，才能最终确定是否散列碰撞。那该如何解决？

上面也提到了，就是拉链法、开发地址法、在hash等

<img src="https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt51LAibZWTibeL6pNtAo813I9fSA6kpmtKib3CTMDRUA5SOib7zM7jYLHUH2iaIOMEHNE8qtibksZiclwoXQ/640?wx_fmt=png&amp;wxfrom=5&amp;wx_lazy=1&amp;wx_co=1" alt="img" style="zoom:67%;" />

**拉链法指的是散列表的数组下标不直接装填“键”，每个下标都指向一个集合，将散列值相同的“键”放在一个集合里面。**在jdk8 以后，当链表长度超过一定数量 8个后，会采用红黑树的数据结构替代链表；

**开放地址法**

直接用数组存放元素，当存入某个元素的时候，如果计算出来的数组下标已经装了元素，而且这两个元素确实不相等，那就找别的下标来存放新的元素。例如某个元素计算出来的下标是a，这个位置已经有元素了，那就看看位置b有没有元素，如果还有了，那看看位置c，一直到有空位为止。

开放地址法作为一个思路，不同的具体方案区别就在于“下个位置怎么找”，也就是b、c都是怎么算出来的。根据具体计算方式不同，分为：线性探测法、二次探测法、双散列法，这里的“探测”指的就是查找下个位置。

**讲解散列表的时候主要围绕几个主题：**

1. 将“键”转变为整数；==通过重写hashcode 实现==
2. 调整整数的取值范围；
3. 处理散列碰撞。==重写equals 方法判断两个key是否相等==

对于第一个问题，前面有些介绍了，转变的过程因object 的类型不同而不同，比如arrayList的采用的方法

```
public int hashCode() {
        int hashCode = 1;
        for (E e : this)
            hashCode = 31*hashCode + (e==null ? 0 : e.hashCode());
        return hashCode;
    }
```

第二个问题：调整整数的取值范围

前文说过要使用散列表有两步：将“键”转变为数组；调整取值范围。其中第一步必须要自己实现，原因前面也说了，你自己定义的类应该如何转变为整数，只有你自己才知道。至于第二步，实际上涉及到如何维护一个数组，当装填的元素过多时可以扩容、当删除了很多元素时可能还可以缩小，数组的长度要如何根据实际装填的元素数量进行设计，如何将“键”提供的整数调整到数组长度范围内，甚至包括后续的如何处理散列碰撞。**这一系列的问题，其实和具体的“键”的类型没有关系，所以这些操作都是可以复用的，没有必要让每个使用散列表的程序员自己实现一套，Java里面通过HashMap来实现这一切。** （真正的理解这一句话，你就能知道为啥去重写hashcode 方法）

所以在Java里面，做业务的程序员只负责提供一个将“键”转变为整数的方法，这通过hashCode()来实现，至于调整取值范围以及维护整个散列表数组则由Java负责。然而我们前面还说了，在处理散列碰撞的时候，**还需要知道散列碰撞的两个“键”是否真的相等，这个也需要做业务的程序员来提供判断依据，这通过equals()来实现。**（真正的理解这一句话，你就能知道为啥去重写equals方法）

实际上当你学了散列表并且理解了这两个方法到底在干什么的时候，你会发现没有必要把这两个方法放在一起思考。hashCode()只是为了提供一个将“键”转变为整数的方法，至于该怎么写，就像本文说的，根据你的实际情况，让“键”的各个部分参与运算，并让结果尽可能均匀分布。对于equals()，根据你实际的业务逻辑，判断两个“键”是否相等即可

**如何处理碰撞？**

就是上面所说的拉链法。

**散列表的缺陷**

虽然查找很快，但散列函数的计算实际上丢失了和顺序相关的信息，所以在散列表里执行和顺序相关的操作性能很差。诸如查找最大、最小值，排名、选择等操作，最少都要全表遍历一次。（这里找的都是最值，和查找很快不矛盾）

**为啥重写equals方法就必须重写hashCode方法？重写hashcode 却不必重写equals 方法？**

明白

1. hashcode 是把输入对象转换为整数的过程；equals 方法是发生碰撞后，在业务上判断两个对象是否相等；
2. **不被重写（原生）的hashCode值是根据内存地址换算出来的一个值。不被重写（原生）的equlas方法是严格判断一个对象是否相等的方法（object1==object2）**

Object的hashcode方法约定：

如果两个对象根据equals(Object)方法是相等的返回true，那么调用这两个对象中任一个对象的hashCode方法必须产生同样的整数散列码结果；

极大可能两个对象在业务上相等，即equals方法相等，但他们的hashcode不等所以违反了这个约定。

解释第二个问题，只要知道两个方法的含义就行了，重写了Hashcode 方法，可以用系统默认的方式比较两个对象是否相等来去判定。

### HashMap的扩容机制

当put 新的元素时，需要去检查所存的元素个数和阈值 = 数组长度 * 加载因子 比较，大于这个阈值就去resize（），就是定义一个新的数组，其大小是把原来的容量 左移 一位，相当于成以 2. 然后重新计算每个元素在新数组中的位置，这是一个很耗性能的操作。根据之前的元素是单元素、树、还是链表做出不同的操作。

### put 、get 方法

#### put 方法

```
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    else {
        Node<K,V> e; K k;
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

1. 判断当前桶是否为空，空的就需要初始化（resize 中会判断是否进行初始化）。

   ```
   if ((p = tab[i = (n - 1) & hash]) == null)
       tab[i] = newNode(hash, key, value, null);
   ```

2. 根据当前 key 的 hashcode 定位到具体的桶中并判断是否为空，为空表明没有 Hash 冲突就直接在当前位置创建一个新桶即可。

3. 如果当前桶有值（ Hash 冲突），那么就要比较当前桶中的 `key、key 的 hashcode` 与写入的 key 是否相等，相等就赋值给 `e`,在第 8 步的时候会统一进行赋值及返回。

   > 整个2 3步就是判断当前key 对应的节点node （桶）解决hash冲突的一个过程，为空表示没有冲突，不为空表示发生了hash碰撞，怎么办？这里通过比较key和key 的hash值在去判断是否真正的相等，即equals 方法。 

4. 如果当前桶为红黑树，那就要按照红黑树的方式写入数据。

5. 如果是个链表，就需要将当前的 key、value 封装成一个新节点写入到当前桶的后面（形成链表）。

6. 接着判断当前链表的大小是否大于预设的阈值，大于时就要转换为红黑树。

7. 如果在遍历过程中找到 key 相同时直接退出遍历。

8. 如果 `e != null` 就相当于存在相同的 key,那就需要将值覆盖。

9. 最后判断是否需要进行扩容。



#### get方法

```
public V get(Object key) {
        Node<K,V> e;
        return (e = getNode(hash(key), key)) == null ? null : e.value;
    }
    
final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        if ((e = first.next) != null) {
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            do {
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
```

- 首先将 key hash 之后取得所定位的桶。
- 如果桶为空则直接返回 null 。
- 否则判断桶的第一个位置(有可能是链表、红黑树)的 key 是否为查询的 key，是就直接返回 value。
- 如果第一个不匹配，则判断它的下一个是红黑树还是链表。
- 红黑树就按照树的查找方式返回值。
- 不然就按照链表的方式遍历匹配返回值。

### **HashMap、HashTable、ConuurentHashMap 的区别？**

​	1、HashMap是非线程安全的，HashTable是线程安全的。因为他的方法加了synchnorzed

```
  /**
     * Returns the number of keys in this hashtable.
     *
     * @return  the number of keys in this hashtable.
     */
    public synchronized int size() {
        return count;
    }

    /**
     * Tests if this hashtable maps no keys to values.
     *
     * @return  <code>true</code> if this hashtable maps no keys to values;
     *          <code>false</code> otherwise.
     */
    public synchronized boolean isEmpty() {
        return count == 0;
    }
```



​	2、HashMap的键和值都允许有null值存在，而HashTable则不行。

​	3、因为线程安全的问题，HashMap效率比HashTable的要高。

​	4、ConuurentHashMap 也是线程安全，但内部使用的锁不是synchroznized ，而是Lock（）显示锁，这种锁是轻量级锁，并非锁住整个对象，效率比hashTable高。

### Hashmap 底层为什么是线程不安全的？

并发场景下使用时容易出现死循环， 在 HashMap 扩容的时候会调用 resize() 方法，就是这里的并发操作容易在一个桶上形成环形链表。或者简单理解，在插入元素时，两个线程获取到的头节点是一个，两个进行插入，最终肯定只会有一个成功，另一个被覆盖，这样就是不安全的。

### ArrayList

**arrayList 扩容原理**

在add 方法 ensureCapacityInternal(size + 1) ; 把这个方法参数叫为最小数组长度。这个size 是之前的数组长度，这个方法先判断数组是不是空数组，若是第一次加入元素，会初始化为默认长度10；否则就取值size + 1；进入到ensureExplicitCapacity 方法，用这个长度和之前的数组初始化长度相比较，若大于则进行扩容，扩容方法为grow （minIncapacity）方法就用之前的长度进行位运算，右移一位（相当于0.5倍）并加上之前的长度，这样新的长度等于之前的1.5倍；最后用native方法Array。copyOf 进行数组复制。同样的，如果是进行添加一个子容器，道理也是一样的。

### **ConcurrentHashMap 、HashTable 对比hashMap ？**

**hashtable** 

1. Key 和value 都不能为空

   ```
   //key 不能为空 是因为在计算hash值时，key 要调用object 的hashcode方法，若为空会抛出空指针异常
   //value 不能为空，为空也会抛出指针异常
    
    public synchronized V put(K key, V value) {
           // Make sure the value is not null
           if (value == null) {
               throw new NullPointerException();
           }
   
           // Makes sure the key is not already in the hashtable.
           Entry<?,?> tab[] = table;
           int hash = key.hashCode();
           int index = (hash & 0x7FFFFFFF) % tab.length;
        .....
       }
   
   ```

2. 计算hash 值，hashtable 用key 调用了object 的hashcode 方法，即对象的hashcode 方法；hashmap 的hash计算方法是把对象的hashcode 和 自己高16位或运算。

3. 最重要的区别就是 对于 get、put、remove 方法添加了synrchnoized 锁，确保线程安全；

**Concurrenthashmap**

ConcurrentHashMap 是如何实现的？ 1.7、1.8 实现有何不同？为什么这么做？

也是线程安全的；也是区分1.7 和1.8 版本的区别。

1.7 版本：segment 类（内部包含了加载因子、阈值，存放数据类HashEntry并且volatile 修饰），segment 继承了Reetrantlock 类，来实现线程安全；然后concurrenthashmap 的put、get操作都是交给内部类sgment 实现的

1.8版本的Concurrenthashmap 和 1.7相比，没有了segment 类，也把存放书籍的HashEntry 改为 node 内部类。然后node 内部类

next 和 val 都有volient 关键字修饰，保证可见性。

```
final V putVal(K key, V value, boolean onlyIfAbsent) {
        if (key == null || value == null) throw new NullPointerException();
        //1 计算hash
        int hash = spread(key.hashCode());
        int binCount = 0;
        for (Node<K,V>[] tab = table;;) {
            Node<K,V> f; int n, i, fh;
            // 2 判断是否需要进行初始化
            if (tab == null || (n = tab.length) == 0)
                tab = initTable();
            else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
                if (casTabAt(tab, i, null,
                             new Node<K,V>(hash, key, value, null)))
                    break;                   // no lock when adding to empty bin
            }
            //3 当前位置的 hashcode == MOVED == -1,则需要进行扩容。
            else if ((fh = f.hash) == MOVED)
            
                tab = helpTransfer(tab, f);
            else {
                V oldVal = null;
                // 锁  4如果都不满足，则利用 synchronized 锁写入数据。
                synchronized (f) {
                    if (tabAt(tab, i) == f) {
                    
                    ...
                     //  5 如果数量大于 TREEIFY_THRESHOLD 则要转换为红黑树
                    if (binCount >= TREEIFY_THRESHOLD)
                        treeifyBin(tab, i);
                    if (oldVal != null)
                        return oldVal;
                    break;
                }
                    
```

这是put 方法，也是不允许key 、value 为空；另外 采用了synchronized + voliate实现线程安全 ，没有使用了Reentrantlock 类了。同时采用了unsafe 类的CAS思想。同时，也优化了数据结构，引入了红黑树。这一点和hashmap 很相似。 

### SparseArray、 ArrayMap 和HashMap比较

#### SparseArray  稀疏数组

1. 采用的是 两个数组来存放数据，并且key 只能是int 类型的，并且由于int 类型的，避免装箱（类型转换为Interger）

   ```
   private int[] mKeys;
   private Object[] mValues;
   ```

2. 由于不像hashmap 那样，需要额外的内部类Node来取存储数据。

3. 理解get 方法

   ```
   public E get(int key, E valueIfKeyNotFound) {
   // 二分查找，所以说不能在大量的数据下使用，否则二分查找需要耗费较多时间，因为二分查找和数据量有关
       int i = ContainerHelpers.binarySearch(mKeys, mSize, key);
   
       if (i < 0 || mValues[i] == DELETED) {
           return valueIfKeyNotFound;
       } else {
           return (E) mValues[i];
       }
   }
   ```

4. **还有就是在删除元素时，sparseArray 不会立即删除，会先标记然后复用；或者在后面统一处理，就是gc（） 方法。**

#### **arrayMap** 

这个和稀疏数组相似，也是用两个数组来存放数据，也是通过二分法查找，不适合存放大量数据；