## 1、学前说明

#### 学习重点

- Collection体系
- List接口、ArrayList、Vector、Linkedlist的使用和区别
- 线程安全的集合
- Set、Map的概念、以及HashSet、HashMap、TreeMap的区别和使用

#### 进阶学习

- HashMap源码分析，了解HashMap的分桶，扩容原理
- 拓展：哈希环概念和算法

#### 学习视频

https://www.bilibili.com/video/BV1zD4y1Q7Fw?p=34

## 2、集合概述

一：集合概念

对象的容器，定义了多个对象进行操作的常见方法，类似数组

二：集合和数组的区别

- 集合长度不固定
- 数组存储基本类似和引用类型，集合只能存储引用类型(装箱操作)

![image-20220417201723945](D:%5CApplication%5CTypora%5Cimage%5Cimage-20220417201723945.png)

三：Collection父接口

- 特点：代表一组任意类型的对象，无序无下标不能重复

- 方法：

  - boolean add(Object o)：添加
  - boolean addAll(Collection c)：添加全部
  - void clear：清空
  - boolean contain(Object o)：是否包含
  - boolean containAll(Collection c)：是否全部包含
  - boolean isEmpty()：是否为空
  - Iterator iterator()：返回在此collection的元素上进行迭代的迭代器
  - boolean remove(Object o)
  - boolean removeAll(Collection c)：取并集
  - boolean retainAll(Collection c)：取交集
  - toArray()：变成数组

- 遍历

  - 增强for

  - iterator()方法

    ```java
    Collection<Integer> list = new ArrayList<>(){{
        add(1);
        add(2);
        add(3);
    }};
    //取出迭代器
    Iterator<Integer> iterator = list.iterator();
    //遍历
    while (iterator.hasNext()) {
        //遍历过程中不能使用list.remove()方法，会出现并发修改异常
        //应该使用iterator.remove()方法
        System.out.println(iterator.next());
    }
    ```

四：List接口

- 特点：有序，有下标，元素可以重复
- 方法
  - boolean add()
  - boolean addALL()
  - Object get()
  - int indexOf()
  - ListIterator listIterator()：该迭代器可以从任意方向遍历List
  - List subList()
- 常用实现类
  - ArrayList：数组结构实现，查询快，增删慢
  - Vector：线程安全，效率慢一点
  - LinkedList：链表结构实现，增删快，查询慢

## 3、ArrayList

ArrayList继承自Object类，其equals方法是比较内存地址，如果想比如其值，需要重写equals方法。

重写equals方法的步骤：

```java
@Override
public boolean equals(Object obj) {
    //1.判断是否为同一个对象
    if (this == obj) {
        return true;
    }
    //2.判断是否为空
    if (obj == null) {
        return false;
    }
    //3.判断是否为特定的类型
    if (obj instanceof Student) {
        Student s = (Student) obj;
        //4.比较属性
        if(this.name.equals(s.getName)) {
            return true;
        }
    }
    //5.不满足，返回false
    return false;
    
    
}
```

- 遍历：使用普通迭代器和列表迭代器遍历

#### 源码分析

数据结构：

```java
//默认容量
private static final int DEFAULT_CAPACITY = 10;
//数据存放数组
transient Object[] elementData;
//元素个数
private int size;
```

构造方法：初始化一个空的ArrayList时，为空

```java
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```

增加元素过程：

```java
//add方法
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}


private void ensureCapacityInternal(int minCapacity) {
    //calculateCapacity()：首次添加元素返回DEFAULT_CAPACITY，否则返回minCapacity
    //如果当前数组实际个数大于elementData.length，进行数组扩容
    ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}

private static int calculateCapacity(Object[] elementData, int minCapacity) {
    //如果使用空构造方法，且首次添加元素，则返回DEFAULT_CAPACITY，否则返回minCapacity
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        return Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    return minCapacity;
}

private void ensureExplicitCapacity(int minCapacity) {
    modCount++;
    //如果当前数组实际个数大于elementData.length，进行数组扩容
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}

private void grow(int minCapacity) {
    //得到elementData.length，新的elementData大小为原来的1.5倍
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    //针对前几次扩容，可能newCapacity没有minCapacity大，因此需要判断赋值
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    //越界。
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    //扩容
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

查找元素过程

```java
public E get(int index) {
    //检查下标是否越界
    rangeCheck(index);

    return elementData(index);
}

private void rangeCheck(int index) {
    //越界抛出异常
    if (index >= size)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}
```

删除元素过程

```java
public E remove(int index) {
    //检查下标是否越界
    rangeCheck(index);

    modCount++;
    E oldValue = elementData(index);

    int numMoved = size - index - 1;
    //将elementData中index+1后的元素依次复制到index的位置，此时elementData[size] = elementData[size-1]
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    //释放elementData[size]的内存空间
    elementData[--size] = null; // clear to let GC do its work

    return oldValue;
}
```

## 4、LinkedList

源码分析

数据结构：

```java
transient int size = 0;
transient Node<E> first;
transient Node<E> last;
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;

    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```

构造方法：

```java
public LinkedList() {
}
public LinkedList(Collection<? extends E> c) {
    this();
    addAll(c);
}
```

添加元素：

```java
//调用linkLast方法
public boolean add(E e) {
    linkLast(e);
    return true;
}
//调用linkFirst方法
public void addFirst(E e) {
    linkFirst(e);
}
//调用linkLast方法
public void addLast(E e) {
    linkLast(e);
}

private void linkFirst(E e) {
    final Node<E> f = first;
    //构造新结点，next为first，此时的first可能为空
    final Node<E> newNode = new Node<>(null, e, f);
    //将新结点变成头结点，现在first不可能为空
    first = newNode;
    //如果是第一次添加元素，新结点既为头结点也为尾结点
    if (f == null)
        last = newNode;
    //否则，将原来的头结点的prev设为新结点
    else
        f.prev = newNode;
    size++;
    modCount++;
}

void linkLast(E e) {
    final Node<E> l = last;
    //构造新结点，将新结点的prev设为尾结点，此时last可能为空
    final Node<E> newNode = new Node<>(l, e, null);
    //将新结点变成尾结点
    last = newNode;
    //如果是第一次添加元素，新结点既为头结点也为尾结点
    if (l == null)
        first = newNode;
    //否则，将原来的尾结点的next设为新结点
    else
        l.next = newNode;
    size++;
    modCount++;
}
```

查找元素：

```java
public E get(int index) {
    //检查index是否越界
    checkElementIndex(index);
    return node(index).item;
}
private void checkElementIndex(int index) {
    if (!isElementIndex(index))
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}
private boolean isElementIndex(int index) {
    return index >= 0 && index < size;
}
```

修改元素：

```java
public E set(int index, E element) {
    //检查是否越界
    checkElementIndex(index);
    Node<E> x = node(index);
    E oldVal = x.item;
    x.item = element;
    return oldVal;
}
```

删除元素：

```java
public E remove(int index) {
    //检查是否越界
    checkElementIndex(index);
    return unlink(node(index));
}

E unlink(Node<E> x) {
    // assert x != null;
    final E element = x.item;
    final Node<E> next = x.next;
    final Node<E> prev = x.prev;

    //如果prev为null，说明要删除的是first结点，直接把头结点设为要删除的结点的next
    if (prev == null) {
        first = next;
    } else {
        //如果prev不为null，说明要删除的是非头结点，调整prev的next为next，并且将x的prev设为null。
        prev.next = next;
        x.prev = null;
    }
	//如果next为null，说明要删除的是last结点，直接把尾结点设为要删除的结点的prev
    if (next == null) {
        last = prev;
    } else {
        //如果next不为null，说明要删除的是非尾结点，调整next的prev为prev，并且将x的next设为null。
        next.prev = prev;
        x.next = null;
    }
	//经过上面的步骤，x的next和prev都为null，再把x的item设为null，x在GC的时候就会被回收
    x.item = null;
    size--;
    modCount++;
    return element;
}
```

## 5、HashMap

源码分析

数据结构

```java
//初始容量
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;
//最大容量
static final int MAXIMUM_CAPACITY = 1 << 30;
//负载因子
static final float DEFAULT_LOAD_FACTOR = 0.75f;
//链表转化成红黑树阈值
static final int TREEIFY_THRESHOLD = 8;
//红黑树退化成链表阈值
static final int UNTREEIFY_THRESHOLD = 6;
//最小转化容量
static final int MIN_TREEIFY_CAPACITY = 64;
//Node结点
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;

    Node(int hash, K key, V value, Node<K,V> next) {
        this.hash = hash;
        this.key = key;
        this.value = value;
        this.next = next;
    }

    public final K getKey()        { return key; }
    public final V getValue()      { return value; }
    public final String toString() { return key + "=" + value; }

    public final int hashCode() {
        return Objects.hashCode(key) ^ Objects.hashCode(value);
    }

    public final V setValue(V newValue) {
        V oldValue = value;
        value = newValue;
        return oldValue;
    }
	//重写了equals方法，当键和值都相等时，Node结点相等
    public final boolean equals(Object o) {
        if (o == this)
            return true;
        if (o instanceof Map.Entry) {
            Map.Entry<?,?> e = (Map.Entry<?,?>)o;
            if (Objects.equals(key, e.getKey()) &&
                Objects.equals(value, e.getValue()))
                return true;
        }
        return false;
    }
}
```

构造器

```java
public HashMap() {
    this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
}
public HashMap(Map<? extends K, ? extends V> m) {
    this.loadFactor = DEFAULT_LOAD_FACTOR;
    putMapEntries(m, false);
}
public HashMap(int initialCapacity) {
    this(initialCapacity, DEFAULT_LOAD_FACTOR);
}
```

添加元素

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
//取hash
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
//添加值
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    //tab为空，第一次添加元素
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    //新加入的元素取hash后的index为null说明该结点为头结点
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    //不为头结点
    else {
        Node<K,V> e; K k;
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        //加入的p为树结点，调用红黑树插入方法。
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        //此时还为链表
        else {
            //遍历至尾结点：jdk1.8之前为头插法，1.8之后为尾插法
            for (int binCount = 0; ; ++binCount) {
                //插入结点
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    //如果此时链表长度大于阈值，链表变成红黑树
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
    //当size大于阈值时，扩容
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}

//扩容
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;
    int newCap, newThr = 0;
    if (oldCap > 0) {
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold
    }
    else if (oldThr > 0) // initial capacity was placed in threshold
        newCap = oldThr;
    else {               // zero initial threshold signifies using defaults
        //第一次添加元素，初始大小为16
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    if (oldTab != null) {
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                oldTab[j] = null;
                if (e.next == null)
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // preserve order
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```















总结：

- hashmap刚创建时，table为null，为了节省空间，添加第一个元素后table变成16
- 当hashmap的size大于table长度的0.57时，会进行二倍扩容
- jdk1.8后table大于64，且链表长度大于8时，会转化成红黑树。红黑树结点数小于6时，会退化成链表
- jdk1.8之前是头插法，之后是尾插法。