#### ConCurrentHashMap了解认识及深入

- Collections.synchronizedMap
- Hashtable
- ConcurrentHashMap



> Collections.synchronizedMap

​     Collections.synchronizedMap内部有一个普通的Map和一个排斥锁对象,当未进行排斥锁传入时,初始化会将this做为排斥锁,传入时则将传入的排斥锁作为实现线程安全的排斥锁,所有的操作都是由排斥锁保证线程安全



> Hashtable

​	Hashtable的所有数据操作都会上锁,效率比较低

​	Hashtable的Key不允许为Null,因为Hashtable使用的是**安全失败机制**(fail-safe)

- **安全失败机制**(fail-safe):会使你此次读取到的数据不一定是最新值,如果你使用Null值就会使其无法判断对应的Key是不存在还是Null,因为你无法再调用一次contain来对key是否存在进行判断,直接进行Hashtable.get()会直接报空指针异常,可以在多线程下并发使用



​	HashTable继承的是Dictionary(字典)类,HashMap继承的是AbstrartMap

​	HashTable初始容量时11,HashMap初始化容量时16

​	HashTable扩容机制为当前容量翻倍+1,HashMap扩容机制为当前容量翻倍

​	HashTable使用的是**安全失败机制**(fail-safe),HashMap使用的是**快速失败机制**(fail-fast)

- **快速失败机制**(fail-fast) 是Java集合中的一种机制,在用迭代器遍历一个集合对象时,如果遍历过程中集合对象的内容进行了修改则会抛出:ConcurrentModificationException,通过modCount来进行判断



> ConcurrentHashMap

- 1.7

  1.7是通过Segment继承ReentrantLock来实现线程安全的,在ConcurrentHashMap内部维护着一个Segment[]数据结构,每个Segment内部维护着一个HashEntry[],每个Segment来保证各自的HashEntry[]的数据操作安全。

  并发数量等同于Segment[]的容量

  因为在HashEntry内部vlaue和Next使用volatile来修饰变量的

  - volatile

    1. 保证了不同线程对这个变量进行操作时的可见性

    2. 禁止进行指令重排序

    3. 只能保证对单次读/写的原子性。i++ 这种操作不能保证**原子性**

  因为HashEntry[]使用的还是链表的数据结构当数据量比较大的时候效率还是有所下降

- 1.8

  1.8 抛弃了分段锁[Segment方式],采用了CAS + synchronized的方式来保证多线程的并发安全

  HashEntry改成了Node,并且引入了红黑树, 当某一节点的数量等于或大于8时,会转换成红黑树

  - put

    1. 根据key算出hashCode

    2. 判断是否需要初始化table
    3. 当前key定位出的node如果为空表示当前位置可以写入数据,利用CAS尝试写入,失败则自旋保证成功
    4. 如果当前位置的hashcode == MOVED == -1时则进行扩容,
    5. 以上条件都不满足则利用synchronized锁写入数据
    6. 判断当前节点的链表大小是否大于TREEIFY_THRESHOLD,是的话则转换为红黑树

​	



