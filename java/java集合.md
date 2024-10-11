# 集合

## 集合概述

![Java 集合框架概览](./java集合.assets/java-collection-hierarchy.png)

Clollection接口和Map接口派生；Collection 接口存放单一元素，下面有List、Set、Queue接口，Map接口存放键值对。

### List、Set、Queue、Map四者的区别？

- List：存储的元素是有序、可重复的；
- Set：元素不可重复
- Queue：按特定排队规则确定先后顺序，有序可重复；
- Map：键值对存储，key无序不可重复，value无序可重复；

## List

### ArrayList 和 Array 的区别？

```java
int[] numbers = new int[5];
numbers[0] = 1;
ArrayList<Integer> nums = new ArrayList<>();
nums.add(1);
```

- 大小：ArrayList基于动态数组实现，Array是静态数组，初始化需要指定大小；
- 类型：ArrayList使用泛型确保数据安全，Array不行；因为Array在创建时确定数据类型，无法在编译时执行类型检查，发生类型强转导致运行异常
  - **存储类型**：
    - **Array**：可以存储基本数据类型（如 `int`、`char`）和对象。
    - **ArrayList**：只能存储对象，基本数据类型会自动装箱为对应的包装类（如 `Integer`、`Character`）。

- 功能：ArrayList 提供更多内置方法，如增删改查

### ArrayList 插入和删除的时间复杂度？

- 插入：头部插入和指定位置插入O(n)，每个元素后移一位；尾插O(1)
- 删除：头删、指定元素删除O(n), 尾删O(1)

### LinkedList 插入删除时间复杂度？

- 插入：头插尾插都是 O(1); 指定位置O(n) 需要遍历位置
- 删除：头删、尾删O(1); 指定位置删除O(n)

### ArrayList 和 LinkedList的区别？

- 线程安全：都不保证线程安全
- 底层数据结构：ArrayList 使用Object数组，LinkedList 使用双向链表Deque
- 插入删除：操作数据的时间复杂度 LinkedList 比 ArrayList 小；
- 查找：ArrayList 内存地址连续O(1)，可以；LinkedList 链表不可以 O(n)；
- 内存空间占用：ArrayList需要预留空间，LinkedList需要额外保存指针空间；

### ArrayList扩容机制是什么？

ArrayList是动态数组，会自动进行扩容；添加元素时如果当前元素数量已经达到内部数组上限，就会自动进行扩容；

- 初始化容量：如果没有指定初始容量，默认为10
- 动态扩容：扩容时会新创建一个内部数组，新数组的大小通常是原数组的1.5倍；然后将原数组的元素复制到新数组；本质是调用了System.arraycopy() ；
  - Array.copyOf() 里面调用了System.arraycopy() 

## Set

### HasSet如何检查重复？

hashset 的 add() 简单调用HashMap的put，会判断是否存在该key，返回是否存在元素；不管有无都会直接插入；

详细过 程：会先计算元素的hashcode，检查对应位置是否有元素，如果为空则添加进去，如果不为空则equals比较内存地址是否相等，相等表示已存在，不相等则添加

### Comparable 和 comparator的区别？

都是用于排序的接口

- Comparable 接口出自 java.lang ，用 compareTo(obj) 排序，对自己类的对象排序
- Comparator 接口出自 java.util， 用compare(obj, obj2) 排序，适合用于不同类的对象排序

### 无序性和不可重复性的含义是什么？

- 无序性不是随机性，是因为根据hash值存储数据才无序
- 不可重复指，按equals() 判断时返回false，需要同时重写equals 和 hashcode()方法
  - 如果重写equals但没重写hashcode，会导致虽然值相同，equals返回true，但hashcode不同，hashset中就会允许存在两个不同位置的相同值，导致重复


### 比较HashSet、LinkedHashSet 和 TreeSet的异同

- 都是基于Set接口的实现类，都保证数据唯一，都不是线程安全的；
- 主要区别是底层实现不同：hashset底层是hashmap实现；LinkedHashset底层是链表和hashmap，保证key 的`插入顺序`；TreeSet底层是红黑树，保证元素有序；

## Queue

### Queue 和 Deque 的区别？

Deque 是双端队列，扩展了队首队尾的操作方法，可以模拟栈；

### ArrayDeque 和 LinkedList的区别？

两者都实现了 Deque的接口，都具有队列的功能；

- 底层实现不一样：ArrayDeque 是基于变长数组和双指针实现，LinkedList是基于链表实现；
- ArrayDeque 不能存Null，存在扩容操作；

### 优先队列了解吗？

- PriorityQueue的元素出队顺序是和优先级有关的；
- 底层通过二叉堆数据结构实现，变长数组存储数据
- 堆的上浮下沉实现插入元素时间复杂度O(logn)，删除堆顶元素O(1)
- 非线程安全，默认最小堆

### 阻塞队列了解吗？

BlockingQueue **继承**自Queue的**接口**。支持队列没有元素一直阻塞直到有元素；队列满了等到有空才允许插入元素；有对应的实现类阻塞队列；

### ArrayBlockingQueue 和 LinkedBlockingQueue 有什么区别？

- **线程安全的；**
- 底层实现：前者基于数组实现，后者基于链表
- 是否有界：前者有界，需指定容量大小；后者无界，可指定队列长度成为有界；
- 锁是否分离：前者生产者和消费者用同一把锁；后者是分离的，生产者用putLock，消费者用takeLock，防止生产者二号消费者线程之间的锁争夺；
  - **ArrayBlockingQueue**: 采用一个单一的锁（ReentrantLock）来控制对整个队列的访问。这意味着在执行插入或删除操作时，必须获得同一个锁，这会导致插入和删除操作是互斥的。
  - **LinkedBlockingQueue**: 使用了两个不同的锁（分别用于生产者和消费者），这使得在多线程环境中可以更好地利用并发性。生产者和消费者可以并行执行，不会因为互斥锁的存在而相互阻塞。
  - 因为array的大小是固定的，满了生产者需要等待，一把锁就行且保持简单；linked的大小可以扩容，支持生产者和消费者并行处理，两把锁适合




## Map

### HashMap和Hashtable的区别

**hashtable不咋用了，用ConcurrentHashMap**

- 线程安全：HashMap非线程安全，Hashtable线程安全，内部方法用synchronized修饰
- 效率：前者高；
- HashMap 可以**存null的key和value**；hashtable不行，ConcurrentHashMap也不行；

### HashMap和HashSet的区别？

HashSet 底层是基于HashMap实现的；HashSet只存Key，没有Value；map添加元素用put, set用add

### HashMap和TreeMap的区别？

TreeMap能根据Key排序，默认升序；自定义Comparator 的compare(obj1,obj2) 来根据对象的属性值排序，如存person 对象作为key，根据person的age来排序；

### HashMap的底层实现？

#### JDK1.8 之前

JDK1.8 之前底层是数组和链表结合；通过key的hashcode判断当前元素的存放位置，判断该位置是否有key，有key则判断hashcode是否一致，一致则覆盖value，不一致则hash冲突用拉链法解决接链表；

#### JDK1.8之后

解决hash冲突的链表长度大于阈值(默认8)则：

- 如果当前数组长度小于64，先进行数组扩容
- 否则将链表转为红黑树

#### 扩容

hashMap中的元素数量达到负载因子*容量时，会触发扩容，容量为原来的2倍，会对所有key重新计算hashcode，即rehash；

- 数组默认初始容量为 16，加载因子（load factor）为 0.75。当 HashMap 中的**元素数量**达到容量乘以加载因子时，HashMap 会进行扩容操作。
- **创建**原数组2倍大的数组，重新计算原数组元素hash值并插入新数组；

### HashMap 的长度为什么是2的幂次方？

计算hash值在数组上下标的映射方法：计算数组下标：`(n - 1)& hash` n是数组长度，开始直接用 hash % n 来取余计算，如果除数n是2的幂次，等价于 (n - 1) & hash, 比如 6 % 4 -> 011 & 101 = 001；用位运算效率更高

### HashMap为什么线程不安全？

在多线程环境下，扩容时会造成数据丢失和死循环(两个线程都尝试扩容)；同时会造成多个线程put 导致hash冲突时，数据被覆盖问题；场景：线程a计算hashcode后判空成功，但时间片用完挂起，线程2计算出相同hashcode的key，数组位置判空插入，之后线程a执行插入操作，导致数据覆盖；

### HashMap 的遍历

- iterrator 的EntrySet 和KeySet遍历，map.keySet().iterator();  Integer key = iterator.next();

  - ```java
    for (Map.Entry<String, Integer> entry : map.entrySet()) {
                System.out.println("Key: " + entry.getKey() + ", Value: " + entry.getValue());
            }
    ```

- ForEach KeySet： for（Integer key : map.keySet()){}

  - ```java
    // 使用 keySet() 遍历
            for (String key : map.keySet()) {
                System.out.println("Key: " + key + ", Value: " + map.get(key));
            }
    ```

- Lambda: ` map.forEach((key, value)->{})`

  - ```java
    map.forEach((key, value) -> {
                System.out.println("Key: " + key + ", Value: " + value);
            });
    ```

- 单线程：`map.entrySet().stream().forEach((entry) ->{})`

- 多线程：`map.entrySet().parallelStream().forEach((entry) -> {})`

### ConcurrentHashMap 和 Hashtable 的区别？

线程安全的实现不同：

- 底层实现不同：ConcurrentHashMap 和Hashmap底层都是数组 + 红黑树(1.7 分段数组+链表)；后者只有数组+链表
- **线程安全实现方式不同**：
  - ConcurrentHashMap 1.7 引入 **分段数组** ,每把锁锁一个段的数据；1.8 之后放弃segment，采用 **Node 数组 + 链表 + 红黑树** ，并发控制使用Synchronized 和 CAS。
  - Hashtable 使用同一把锁锁方法，会造成线程阻塞访问该方法，既不能put也不能get

### ConcurrentHashMap 的底层实现？

1.7 之前是分段数组 + 链表，对segment加锁每个segment 连接HashEntry数组；对同一个segment并发写还是会阻塞，但可以并发写多个segment；默认Segment数组大小16；

1.8之后放弃Segment, 采用`Node数组 + CAS + Synchronized`保证线程安全；结构和HashMap类似；锁粒度更细，只锁当前Node，即**只锁了当前链表或红黑树的首节点**，**只要hash不冲突不影响读写**；

Node数组是存储键值对以及指向下一个节点引用的数组；CAS(compare and Swap) 是一个原子操作

- Synchronized 锁住整个哈希表保护只有一个线程**扩容**

- CAS 用于node的插入或删除，以及链表转红黑树时锁头node：

  - CAS(node.next, currentNext, node4)

    **`node.next`**: 要更新的内存位置（即当前节点的 `next` 引用）。

    **`currentNext`**: 期望的旧值，也就是我们在操作开始时获取的当前 `next` 节点。

    **`node4`**: 新值，即我们希望将 `node.next` 更新为的值。

  - cas检查`node.next` 的当前值是否与 `currentNext` 相同，如果其他线程修改了node.next则cas会失败重新获取状态执行

### ConcurrentHashMap 为什么key 和 value 不能为null？

避免模糊性和简化实现，因为多线程情况下无法区分是key 的value是null 还是 key不存在返回null；比如有key2: null，当线程A put（key2，2）时，还没有替换key2等于2就被线程B get(“key2”) 读走了null；

而HashMap允许存null是有一个特殊的Entry表示null键值对，NULL Entry；

**多线程下无法正确判定键值对是否存在（存在其他线程修改的情况），单线程是可以的（不存在其他线程修改的情况）。**可以使用一个空对象来代替空值null；

### ConcurrHashMap能保证复合操作的原子性吗？

不能。复合操作只的是多个操作的集合，如检查元素是否存在然后插入，更新；只能保证单个`put`, `get`, `remove` 操作是线程安全的

非原子场景：线程a map.contains(key) 判空成功，但时间片用完挂起，线程2 map.contains(key) ，数组位置判空插入put(key, value2)，之后线程a执行插入操作put(key, value1)，导致数据覆盖；

```java
if (map.get(key) == null) {
    map.put(key, value);
}
```

因为不是concurrentHashMap内部的连续操作

因此提供了原子操作的方法：putIfAbsent、compute等原子性方法

## 集合注意事项

### 集合去重

对一个List datas去重，` new HashSet<>(datas)`；如果通过遍历ArratList，判断contains(data),时间复杂度是O(N);

### 集合转数组

使用 toArray(T[] array) 方法；

### 数组转列表

List<T> mylist = Arrays.asList(arr)



























