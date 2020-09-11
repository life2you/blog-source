#### HashMap 知识点

数据结构:**由数组和链表组合构成**的数据结构

- HashMap默认容量大小为16[1<<4],最大容量为1073741824[1<<30]

- 在设置初始容量时,并不会直接采用我们设置的参数,而是经过计算,得到一个新值

- Java7在进行第一个参数添加时才去进行容量初始化,Java会在创建Map时进行初始化

- HashMap中的链表大小超过8时会转换成红黑树,当删除到小于6时会转换成链表

- 当Key为Null时,会放在数组中的第一位中的节点中

- 阿里巴巴规范插件建议最好赋初始值,而且最好是2的幂,使用2的幂原因是因为非2的幂的数字的时候,Length-1的值是所有二进制位全为1

- 数组中每个节点都存放了Key-Value这样的实例,在Java7中叫Entry,Java8中叫Node

- Java8之前新增的数据会在链表中进行**头插法**，Java8之后修改为**尾插法**

  修改为**尾插法**的原因是因为hashMap在进行扩容[resize]时会出现问题,扩容分为两步:

​         1.创建一个新的链表空数组,长度是原数组的2倍

​         2.遍历原链表数组,把所有的链表数据重新hash到新数据

​         不使用复制的方式扩容是因为原本的hash规则不适用当前扩容后的规则,hash规则为:index = HashCode（Key） & （Length - 1）        

​        **头插法会发生的原因是在多线程进行插入时,进行了扩容后因为hash规则的调整可能会出现环形链表**

- Java7扩容代码

  ```java
  void transfer(Entry[] newTable, boolean rehash) {
  int newCapacity = newTable.length;
  for (Entry<K,V> e : table) {
  while(null != e) {
     Entry<K,V> next = e.next;
  	 if (rehash) {
         e.hash = null == e.key ? 0 : hash(e.key);
      }
  		int i = indexFor(e.hash, newCapacity);
      e.next = newTable[i]; // 头插法 进行resize的时候会将原本存在链表中的顺序导致过来
      newTable[i] = e; 
      e = next;
               }
           }
       }
  ```

- Java8添加代码

  ```java
  final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                     boolean evict) {
          Node<K,V>[] tab; Node<K,V> p; int n, i;
          if ((tab = table) == null || (n = tab.length) == 0)
              n = (tab = resize()).length;
          if ((p = tab[i = (n - 1) & hash]) == null) // 当发现没有hash碰撞时直接插入,此处会发生线程安全问题
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

  

