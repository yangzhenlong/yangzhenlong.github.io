# ArrayList（java8）
简要介绍
```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable{}
-
继承AbstractList，实现List接口，提供了相关add、remove、indexOf、get等方法
实现RandomAccess接口，提供了随机访问功能。在ArrayList中，我们即可以通过元素的序号快速获取元素对象；这就是快速随机访问。RandomAccess接口是一个标记接口，它并未定义方法。其目的是用于指示实现类具有随机访问特性，在遍历时使用下标访问较迭代器更快
实现Cloneable，提供了克隆方法
实现Serializable，支持序列化
-
与array相比，ArrayList能动态增长，并且提供了其他复杂的操作方法
可以存放任何数据，包括null值
非线程安全，无法在多线程状态下直接使用
查询效率高
更新操作时，需要复制和移动数组，效率较低
```
重要属性
```java
transient Object[] elementData;  // 存放元素的数组，这里使用transient，表示不自动序列化


但是可以显式的进行序列化和反序列化
-
private void writeObject(java.io.ObjectOutputStream s){}
private void readObject(java.io.ObjectInputStream s){}
-
```
```java
private int size;  // 实际存放元素的数量
```
构造方法
```java
 public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
//指定大小
public ArrayList(int initialCapacity){}
//指定一个集合，会将集合中的元素添加到ArrayList中
public ArrayList(Collection<? extends E> c) {}
```
添加元素 add()
```java
//添加元素，默认在数组尾部添加
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!! 扩容监测
    elementData[size++] = e; //将插入的值放到尾部，并将 size + 1 
    return true;
}
//在指定位置添加元素
public void add(int index, E element) {
    rangeCheckForAdd(index);//校验index是否大于数组长度或者小于0

    ensureCapacityInternal(size + 1);  // Increments modCount!! 扩容监测
    //对数据进行复制，目的是把 index 位置空出来放本次插入的数据，并将后面的数据向后移动一个位置
    System.arraycopy(elementData, index, elementData, index + 1,
                     size - index);
    elementData[index] = element;//将插入的值放到指定位置index
    size++;//将 size + 1 
}
```

扩容 grow()
```java
//扩容检测
private void ensureCapacityInternal(int minCapacity) {
    ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}
//
private void ensureExplicitCapacity(int minCapacity) {
    modCount++;

    // 当数组容量不足，调用grow进行扩容
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
//扩容
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;//获取当前容量大小
    int newCapacity = oldCapacity + (oldCapacity >> 1);//新容量=当前容量的1.5倍
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}

private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE) ?
        Integer.MAX_VALUE :
        MAX_ARRAY_SIZE;
}
```

快速随机访问 get(int index)
```java
public E get(int index) {
    rangeCheck(index);//检查index是否超过数组大小
    
    return elementData(index);//返回数组指定位置的元素
}
```
设置新值并返回旧值 set(int index, E element)
```java
public E set(int index, E element) {
    rangeCheck(index);//检查index是否超过数组大小

    E oldValue = elementData(index);//获取到之前的值
    elementData[index] = element;//设置新值
    return oldValue;//返回之前的值
}
```
移除 remove()
```java
public E remove(int index) {
    rangeCheck(index);

    modCount++;
    E oldValue = elementData(index);

    int numMoved = size - index - 1;
    if (numMoved > 0)
        //重新移动数组
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    //清除数组，让gc收集
    elementData[--size] = null; // clear to let GC do its work

    return oldValue;
}
```

最后说一下 modCount
```java
在父类AbstractList中定义的modCount
protected transient int modCount = 0;
list的所有关于结构变化的操作（add、remove、addAll、removeRange和clear），都会让modCount++

而在私有内部类迭代器Itr中定义了变量expectedModCount和checkForComodification方法
private class Itr implements Iterator<E> {
    int expectedModCount = modCount;
    
    final void checkForComodification() {
        //如果修改数和期望修改数不一致，抛出异常
        if (modCount != expectedModCount)
            throw new ConcurrentModificationException();
    }
}

-

modCount是和expectedModCount配合一起使用
在对集合进行迭代操作时，可能会有add或者remove等操作造成结构改变而发生迭代错误
使用checkForComodification()方法，比较修改数和期望修改数，来规避迭代危险
```

