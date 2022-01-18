昨天遇到了个不常见的错误：ConcurrentModificationException，场景是对于ArrayList中满足条件的元素进行删除，查看代码发现我用的是增强for循环，仔细回想自己大二学Java时好像遇到过这个坑，又踩了一遍...                                                                                                                                                                

## ArrayList遍历删除

初始化需要用到的ArrayList，**注意，此处list当中有两个 2 元素**

```java
private static ArrayList<Integer> list = new ArrayList<>();

static {
    list.add(1);
    list.add(2);
    list.add(2);
    list.add(3);
    list.add(4);
    list.add(5);
}
```

**ArrayList的遍历删除分为4种：**

### 普通for循环删除

```java
@org.junit.Test
public void test2() {
    for (int i = 0; i < list.size(); i++) {
        if (list.get(i) % 2 == 0) {
            list.remove(i);
        }
    }
    System.out.println(list);
}
```

**`踩坑：`**你以为会输出 [1, 3, 5]，但实际输出是

![image-20220115221158668](https://gitee.com/yangtao8453/picgo/raw/master/img/image-20220115221158668.png)

list当中还保留了一个2，因为在删除第一个2以后，list大小发生了变化，而根据索引访问的下标为2的元素时，就访问到了3，第二个2向前推进了1位，**所以普通for循环不适合删除多个满足条件的元素**！



### 增强for循环删除

```java
@org.junit.Test
public void test1() {
    for (Integer integer : list) {
        if (integer % 2 == 0) list.remove(integer);
    }
}
```

如果普通for循环会留巨坑，那么增强for循环相对好点，因为他直接给你报错了，不会留坑

![image-20220115222026096](https://gitee.com/yangtao8453/picgo/raw/master/img/image-20220115222026096.png)



### 通过迭代器删除

```java
@org.junit.Test
public void test5() {
    Iterator<Integer> iterator = list.iterator();
    while (iterator.hasNext()) {
        if (iterator.next() % 2 == 0) {
            iterator.remove();
        }
    }
    System.out.println(list);
}
```

![image-20220115223617495](https://gitee.com/yangtao8453/picgo/raw/master/img/image-20220115223617495.png)

通过迭代器的remove方法可以正确删除满足条件的元素



#### removeIf

```java
@org.junit.Test
public void test3() {
    list.removeIf(integer -> integer % 2 == 0);
    System.out.println(list);
}
```

![image-20220115223957166](https://gitee.com/yangtao8453/picgo/raw/master/img/image-20220115223957166.png)

removeIf方法，传入一个判断型表达式Predicate<? super E>，也可以正确删除元素



## 原理分析

普通for循环的原理已讲过，这里重点分析增强for循环和迭代器删除的区别

For-Each基于内部迭代器Itr的hasNext和next()，源码如下

```java
private class Itr implements Iterator<E> {
    int cursor;       // index of next element to return
    int lastRet = -1; // index of last element returned; -1 if no such
    int expectedModCount = modCount;

    Itr() {}

    public boolean hasNext() {
        return cursor != size;
    }

    @SuppressWarnings("unchecked")
    public E next() {
        checkForComodification();
        int i = cursor;
        if (i >= size)
            throw new NoSuchElementException();
        Object[] elementData = ArrayList.this.elementData;
        if (i >= elementData.length)
            throw new ConcurrentModificationException();
        cursor = i + 1;
        return (E) elementData[lastRet = i];
    }

    public void remove() {
        if (lastRet < 0)
            throw new IllegalStateException();
        checkForComodification();

        try {
            ArrayList.this.remove(lastRet);
            cursor = lastRet;
            lastRet = -1;
            expectedModCount = modCount;
        } catch (IndexOutOfBoundsException ex) {
            throw new ConcurrentModificationException();
        }
    }

    @Override
    @SuppressWarnings("unchecked")
    public void forEachRemaining(Consumer<? super E> consumer) {
        Objects.requireNonNull(consumer);
        final int size = ArrayList.this.size;
        int i = cursor;
        if (i >= size) {
            return;
        }
        final Object[] elementData = ArrayList.this.elementData;
        if (i >= elementData.length) {
            throw new ConcurrentModificationException();
        }
        while (i != size && modCount == expectedModCount) {
            consumer.accept((E) elementData[i++]);
        }
        // update once at end of iteration to reduce heap write traffic
        cursor = i;
        lastRet = i - 1;
        checkForComodification();
    }

    final void checkForComodification() {
        if (modCount != expectedModCount)
            throw new ConcurrentModificationException();
    }
}
```

注意属性expectedModCount，初始化赋值为ArrayList的属性值

```java
int expectedModCount = modCount;
```

modCount理解为ArrayList对象的修改次数

`每次调用next方法都会先调用checkForComodification，检查Itr内部的expectedModCount和ArrayList的modCount是否一致，不一致则抛出ConcurrentModificationException`

再看增强for循环删除元素调用的remove方法源码，`注意：这里的remove为ArrayList的remove`

```java
public boolean remove(Object o) {
    if (o == null) {
        for (int index = 0; index < size; index++)
            if (elementData[index] == null) {
                fastRemove(index);
                return true;
            }
    } else {
        for (int index = 0; index < size; index++)
            if (o.equals(elementData[index])) {
                fastRemove(index);
                return true;
            }
    }
    return false;
}
```

fastRemove

```java
private void fastRemove(int index) {
    modCount++;
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    elementData[--size] = null; // clear to let GC do its work
}
```

fastRemove中修改了ArrayList的属性modCount，让其自增，但是此时Itr内部的expectedModCount并没有修改，所以在执行了本次循环之后进入下一次循环（调用next），**`modCount != expectedModCount`**，即抛出了ConcurrentModificationException。

而采用迭代器删除元素，即Itr的remove方法，是不会改变ArrayList的modCount，此时expectedModCount = modCount是成立的，所以checkForComodification并不会抛出异常，**这就是iterator的remove和list的remove的区别！**

顺便看看removeIf的内部实现

```java
@Override
public boolean removeIf(Predicate<? super E> filter) {
    Objects.requireNonNull(filter);
    // figure out which elements are to be removed
    // any exception thrown from the filter predicate at this stage
    // will leave the collection unmodified
    int removeCount = 0;
    final BitSet removeSet = new BitSet(size);
    final int expectedModCount = modCount;
    final int size = this.size;
    for (int i=0; modCount == expectedModCount && i < size; i++) {
        @SuppressWarnings("unchecked")
        final E element = (E) elementData[i];
        if (filter.test(element)) {
            removeSet.set(i);
            removeCount++;
        }
    }
    if (modCount != expectedModCount) {
        throw new ConcurrentModificationException();
    }

    // shift surviving elements left over the spaces left by removed elements
    final boolean anyToRemove = removeCount > 0;
    if (anyToRemove) {
        final int newSize = size - removeCount;
        for (int i=0, j=0; (i < size) && (j < newSize); i++, j++) {
            i = removeSet.nextClearBit(i);
            elementData[j] = elementData[i];
        }
        for (int k=newSize; k < size; k++) {
            elementData[k] = null;  // Let gc do its work
        }
        this.size = newSize;
        if (modCount != expectedModCount) {
            throw new ConcurrentModificationException();
        }
        modCount++;
    }

    return anyToRemove;
}
```

先拿到modCount并赋值给expectedModCount，再修改ArrayList的内部数组elementData，修改期间检查expectedModCount与modCount是否相等，由此决定是否抛出ConcurrentModificationException。

