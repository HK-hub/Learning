#  CopyOnWriteArrayList 为什么是线程安全的

[toc]

废话少说直接来看源码

````java

    /**
     * Appends the specified element to the end of this list.
     *
     * @param e element to be appended to this list
     * @return {@code true} (as specified by {@link Collection#add})
     */
    public boolean add(E e) {
        synchronized (lock) {
            Object[] es = getArray();
            int len = es.length;
            es = Arrays.copyOf(es, len + 1);
            es[len] = e;
            setArray(es);
            return true;
        }
    }

````

从源码中，我们可以看到，当我们的 cowList 添加元素调用add() 方法时，我们的内部会首先对lock 对象进行加锁，然后我们获取到当前数组array的一个快照吧—— es ，然后紧接着调用 `Arrays.copy()` 函数，拷贝数组，再将元素添加到es 中，最后在将快照设置给我们的成员数组，完成同步。

