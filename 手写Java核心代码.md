# 手写Java核心代码

## 原则

需求：描述该类的状态和拥有的操作，即某个对象应该有什么状态，可以进行什么操作，操作成功后有什么结果。

概要设计：设计类的实现，应该包含哪些域和方法，描述方法的参数和返回值

详细设计：类的方法的具体实现逻辑，是逻辑清晰的伪代码

代码：根据详细设计写出的代码

## HashMap

需求

1. HashMap 可以存储一组类型固定的 Key-Value 对，根据key可以在O(1)时间内找到对应的Value。
    1. 有一个节点类型 Node<K, V> 的数组table，默认初始容量initialCapacity=16，默认装载因子loadFactor=0.75，
    2. 可以指定initialCapacity和loadFactor，实际初始容量是刚好大于initialCapacity的2的幂。
    3. 对key进行hash运算，再对 table.length 取余，得到在table中的索引值，即获得所在的桶。如果key是null，则hash(key)=0。
    4. 如果桶中没有数据，则将key存储在桶中。
    5. 如果桶中有数据，桶中的数据以链表的形式存储。查找链表中是否有该数据，有，则覆盖，返回oldValue的值；没有，则将数据添加到链表尾部，返回null。
    6. 数据数量size + 1。并判断是否需要扩容
2. 当数组容量不够时，需要扩容。
    1. 扩容时机
        1. table=null时，初始化table
        2. size > threshold(初始=数组长度 * 装载因子)时，将数组、threshold扩大到原来的2倍。
    2. 依次取出oldTable中的数据，存储在newTable新数组中。
    3. 如果容量达到最大容量，则不再扩容，并将 threshold 设置为 Integer.MAX_VALUE，即后续不会再调用扩容方法。

测试

1. 创建一个HashMap，不设置容量
    1. 在 HashMap 中存入一组 key-value，看是否能取出
    2. 再存入一组key相同，value不同的数据，看是否能取出最新值
    3. 存入(null, null)，看是否能取到；再存入 (null, 1)，看是否更新
    4. 存入key不同的数据，看能否取到
2. 创建一个HashMap，设置容量为2
    1. 存入数据后，看是否会扩容，扩容后，是否能取出数据
    2. 设置最大容量为4，存入4个数据后，再存入数据，看是否会扩容

概述设计

```
MyHashMap {
常量
    最大容量
    默认初始容量
    默认装载因子
属性
    table：存储键值对的数组
    threshold：扩容的阈值，= 数组长度 * 装载因子。刚创建时，存储初始容量
    loadFactor：装载因子
    size：hashmap中存储的数据数量
构造方法
    ()
    (初始容量)
    (初始容量，装载因子)
方法
    put：将key-value，放到HashMap中
    get：从HashMap中取出key对应的value值
    resize：扩容
内部类
    Node<K, V> {
    属性
        hash：key的散列值
        key
        value
        next：指向下一个Node
    构造方法
        (hash, key, value, next)
    }
}
```

详细设计

```java
public class MyHashMap<K, V> {
常量
    MAX_CAPACITY = 1 << 10;
    DEFAULT_INITIAL_CAPACITY = 16;
    DEFAULT_LOAD_FACTOR = 0.75f;
属性
    Node<K, V>[] table;
    int threshold;
    float loadFactor;
    int size;
构造函数
    MyHashMap<K, V>() {
    }

    MyHashMap<K, V>(int initialCapacity, double loadFactor) {
        // 参数有效性检验
        initialCapacity < 0
        initialCapacity > MAX_CAPACITY
        loadFactor < 0
        loadFactor > 1
        // 初始化
        this.loadFactor = loadFactor;
        this.threshold = 根据 initialCapacity 得出的合适的初始容量。
    }

    MyHashMap<K, V>(int initialCapacity) {
        this(initialCapacity, DEFAULT_LOAD_FACTOR);
    }

    public void put(K key, V value) {
        计算出key的hash
        找出table中对应的位置
            位置为空，则创建新节点并把节点放进去。
            位置不为空，遍历找出key相同的节点，将value更新；或者将节点加到链表末尾
    }

    public V get(K key) {
        计算出key的hash
        找出table中对应的位置，并遍历。
            找到，则返回value
            没找到，返回null
    }

    private void resize() {
        获取最新的capacity和threshold
        创建newtable
        将oldTable中的链表依次放入newTable中，并释放oldTable的空间
            1. 只有一个节点的链接，直接放入newTable对应位置
            2. 其余链表，拆分成两个链表，放入newTable中
    }
}
```

代码

```java
package rainy;

public class MyHashMap<K, V> {
    private final static int MAX_CAPACITY = 1 << 2;
    private final static int DEFAULT_INITIAL_CAPACITY = 16;
    private final static double DEFAULT_LOAD_FACTOR = 0.75f;

    private Node<K, V>[] table;
    private int threshold;
    private double loadFactor;
    private int size;
    
    class Node<K, V> {
        // 属性
        int hash;
        K key;
        V value;
        Node<K, V> next;
        // 构造方法
        Node(int hash, K key, V value, Node<K, V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }
    }
    
    // 构造函数
    public MyHashMap() {
    }

    public MyHashMap(int initialCapacity, double loadFactor) {
        // 参数有效性检验
        if (initialCapacity < 0) {
            throw new IllegalArgumentException("容量不能为负数");
        }
        if (initialCapacity > MAX_CAPACITY) {
            this.threshold = Integer.MAX_VALUE;
            initialCapacity = MAX_CAPACITY;
        }
        if (loadFactor <= 0 || loadFactor > 1)
            throw new IllegalArgumentException("loadFactor的范围是(0,1]");
        // 初始化
        this.loadFactor = loadFactor;
        this.threshold = tableSizeFor(initialCapacity);
    }

    public MyHashMap(int initialCapacity) {
        this(initialCapacity, DEFAULT_LOAD_FACTOR);
    }
    
    public V put(K key, V value) {
        // 检查tab是否存在，不存在则初始化
        if (table == null || table.length == 0) {
            resize();
        }
        int n = table.length;
        // 找出table中对应的位置
        Node<K, V> p = table[hash(key) & (n - 1)];
        if(p == null) { // 位置为空，则创建新节点并把节点放进去。
            table[hash(key) & (n - 1)] = new Node<K, V>(hash(key), key, value, null);
        } else { // 位置不为空，遍历找出key相同的节点，将value更新；或者将节点加到链表末尾
            Node<K, V> e = null; // 指向p的前一个节点
            while(p != null) {
                if(p.key == key || (p.key != null && p.key.equals(key))) {
                    V oldValue = p.value;
                    p.value = value;
                    return oldValue;
                }
                e = p;
                p = p.next;
            }
            e.next = new Node<K, V>(hash(key), key, value, null);
        }
        // 更新数组的元素数量
        ++size;
        if(size > threshold)
            resize();
        return null;
    }
    
    public V get(K key) {
        // 获得 key 所在的桶
        Node<K, V> p = table[hash(key) & (table.length - 1)];
        // 遍历该桶
        while(p != null) {
            if(p.key == key || (p.key != null && p.key.equals(key))) {
                return p.value;
            }
            p = p.next;
        }
        return null;
    }
    
    // ------------------- 工具方法 -------------------
    
    private static int tableSizeFor(int cap) {
        int n = (-1 >>> Integer.numberOfLeadingZeros(cap - 1));
        if (n == 0)
            return 1;
        if ((n + 1) > MAX_CAPACITY)
            return MAX_CAPACITY;
        return n + 1;
        // n == 0 ? 1 : ((n + 1) > MAX_CAPACITY ? MAX_CAPACITY : n + 1);
    }
    
    private int hash(K key) {
        if (key == null)
            return 0;
        return key.hashCode() ^ (key.hashCode() >>> 16);
    }
    
    private void resize() {
        // 获取新的capacity和threshold
        int oldCap = (table == null) ? 0 : table.length;
        int oldThr = this.threshold;
        int newThr = 0;
        int newCap;
        Node<K, V>[] tab = table;
        if (oldCap > 0) {
            if(oldCap >= MAX_CAPACITY) { // 检查上限
                newThr = Integer.MAX_VALUE;
                newCap = MAX_CAPACITY;
            } else {
                newCap = oldCap << 1;
                if (newCap > MAX_CAPACITY) {
                    newCap = MAX_CAPACITY;
                    newThr = Integer.MAX_VALUE;
                } else {
                    newThr = oldThr << 1;
                }
            }
        } else if (oldThr > 0) { // 初始化table
            newCap = oldThr;
        } else {
            newCap = DEFAULT_INITIAL_CAPACITY;
        }
        if(newThr == 0) {
            newThr = (int)(newCap * DEFAULT_LOAD_FACTOR);
        }
        // 设置新table，将tab中的node转移到table中
        table = (Node<K, V>[])new Node[newCap];
        this.threshold = newThr;
        System.out.println("expands, newCap: " + newCap + ", newThr: " + newThr);
        if(tab != null) { // tab中有数据
            for(int i = 0; i < tab.length; ++i) {
                Node<K, V> p = tab[i];
                tab[i] = null; // 释放空间
                if(p != null) {
                    Node<K, V> loHead = null;
                    Node<K, V> loTail = null;
                    Node<K, V> hiHead = null;
                    Node<K, V> hiTail = null;
                    while(p != null) {
                        if((hash(p.key) & (table.length - 1)) == i) {
                            if(loTail == null) {
                                loHead = p;
                            } else {
                                loTail.next = p;
                            }
                            loTail = p;
                        } else {
                            if(hiTail == null) {
                                hiHead = p;
                            } else {
                                hiTail.next = p;
                            }
                            hiTail = p;
                        }
                        p = p.next;
                    }
                    if(loHead != null) {
                        loTail.next = null;
                        table[i] = loHead;
                    }
                    if(hiHead != null) {
                        hiTail.next = null;
                        table[i + tab.length] = hiHead;
                    }
                }
            }
        }
    }
    
}
```

疑问

Node 类型为什么要重写 equals 和 hashcode 函数？

## LinkedHashMap

## TreeMap

## String

需求

1. 字符串可以赋给String变量（JVM的功能，自己不用实现？）
2. 创建：通过new传递字符串、字节数组、byte数组……创建String变量
3. String变量一旦创建就不能更改
4. 功能有：获取长度、判断是否为空、获取某个位置的字符、获取对应的字符数组、字节数组、判等、比较、查找、截取、拼接、替换、分割……
5. 判等：两个字符串长度相等，对应字符相等
6. 比较：对应位置的字符依次比较，直到遇到不相同的字符，字符大的字符串 > 字符小的字符串
7. 查找：查找string字符串中是否有substring字符串，有，则返回第一个匹配字符串的第一个字符的下标；没有，则返回-1。
8. 截取：截取原字符串从off开始的len个字符，并返回一个新的字符串
9. 拼接：将str1和str2拼接成一个新字符串并返回
10. 替换：给出regex表达式的字符串形式，使用replacement字符串替换被匹配的部分。
11. 分割：找到字符串中的分割符，并将分隔符前后拆分成2个字符串。返回分割后的字符串数组

测试

1. 构造一个空字符串，打印看结果是否正确
2. 传入字符串，构造一个String变量，打印看是否正确
3. 传入字符数组，构造一个String变量，看是否正确

概要设计

```
MyString {
属性
    byte[] value：存放MyString变量中的每个字符
构造
    ()
        结果：构造一个""字符串
    (MyString str)
        结果：一个和str相等的新字符串
    (char[] value)
        结果：由value构成的字符串
方法
    toString()
        结果：打印出字符串的值
    equals(MyString str)
        结果：相等返回true；不相等返回false。
    compare(MyString str)
        结果：this > str，返回正值；this < str，返回负值；this = str，返回0。
    indexOf(MyString str)
        结果：返回this中第一个str开始的位置，如果没找到str，则返回-1
    subString(int off, int len)
        结果：从this的off位置开始，截取len个字符，生成一个新的字符串，并返回
    replace(String regex, Mystring replacement)
        结果：根据regex正则表达式，匹配this中的字符，并使用replacement替换
    split(String regex)
        结果：被splitToken分割出的字符串数组
}
```

详细设计

```java
public class MyString {
    private final byte[] value;
    public MyString() {
        value = "".value;
    }
    public MyString(String s) {
        value = s.value;
    }
    boolean equals(String s) {
        1. 看两个字符串的内存地址是否相等，相等则返回true
        2. 检查参数的类型是否是String，是，查看两个String的长度是否相等，是，则依次比较2个字符串的字符是否相等。
            1. 都相等，则返回true；
            2. 否则返回false
    }
    int compare(MyString str) {
        return StringUtil.compare(value, str.value);
    }
    int indexOf(MyString str) {
        if(value.length < str.length)
            return -1;
        // 算法1：蛮力算法
        1. 找到this中str的第一个字符
        2. 从这个字符开始和str比较，如果到最后都相等，则返回这个字符的索引
        3. 否则，从该字符开始重复步骤12，都没有找到，则返回-1
        // 算法2：KMP算法（仅当str和this部分重复较多时，效率才会比蛮力算法高）
        1. str自匹配获得next数组
        2. str和this进行匹配
    }
    MyString subString(int off, int len){
        0. 验证参数有效性：0 < off < value.length; 0 < len < value.length - from。
        1. 确定新字符串的长度，创建一个byte数组value
        2. 从off开始，将值赋给value对应的位置
        3. 生成新的字符串并返回
    }
    void replace(MyString oldstr, Mystring newstr) {
        1. 找到this中oldstr的个数，返回oldstr的位置
        2. 创建一个byte[]数组，长度是替换后的长度
        3. 复制this到新数组，当遇到oldstr时，替换为newstr
    }
    String[] split(String regex) {
        // 当regex是一个字符时，或是类似\t, \n的字符时
        1. 创建字符串数组
        2. 找到regex在this中的位置，分割字符串，并加入到字符数组中。从该位置之后再进行查找，直到字符串结束。
        // 其他情况
        Pattern.compile(regex).split(this, limit);
    }
}

final class StringUtil {
    public static int compare(byte[] value, byte[] others) {
        int len = Math.min(value.length >> 1, others.length >> 1);
        for(int i = 0; i < len; i++) {
            int result = getChar(value, i) - getChar(others, i);
            if (result != 0)
                return result;
        }
        return value.length - others.length;
    }
}
```

代码

```java

```

经验

不要人为地增加复杂度。

