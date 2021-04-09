## LRU算法学习

#### 概念

LRU是Least Recently Used的缩写，即最近最少使用，是一种常用的[页面置换算法](https://baike.baidu.com/item/页面置换算法/7626091)，选择最近最久未使用的页面予以淘汰。

#### Demo示例

参考了尚硅谷阳哥的视频、力扣算法[LRU 缓存](https://leetcode-cn.com/problems/lru-cache-lcci/)

```java
package com.lly.lru;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * 最近最少使用算法学习
 * LRU（Least Recently Used）
 *
 *
 * @Author lyluo
 * @Date 2021/1/25
 */
public class MyLRU {

    /**
     * 定义节点
     * @param <K> 键类型
     * @param <V> 值类型
     */
    static final class Node<K, V> {
        /**
         * 键
         */
        private K key;
        /**
         * 值
         */
        private V value;
        /**
         * 上一个节点
         */
        private Node<K, V> prev;
        /**
         * 下一个节点
         */
        private Node<K, V> next;

        /**
         * 空参构造函数
         */
        public Node() {
            // 新建的节点，上一个节点，下一个节点都为空，（还没放入队列，所以前后赋值为空）
            this.prev = this.next = null;
        }

        /**
         * 带参构造函数
         * @param key
         * @param value
         */
        public Node(K key, V value) {
            this.key = key;
            this.value = value;
            this.prev = this.next = null;
        }

    }

    /**
     * 定义链表
     * @param <K> 键类型
     * @param <V> 值类型
     */
    static final class DoubleLinkedList<K, V> {
        /**
         * 队列里面的头结点
         */
        private Node<K, V> head;
        /**
         * 队列里面的尾节点
         */
        private Node<K, V> tail;

        /**
         * 构建空队列
         */
        public DoubleLinkedList() {
            // 创建头结点(虚拟节点)
            this.head = new Node<>();
            // 创建尾节点(虚拟节点)
            this.tail = new Node<>();

            // 构建指向关系，形成双向队列
            this.head.next = tail;// 头指向尾
            this.tail.prev = head;// 尾指向头
        }

        /**
         * 添加节点到队列中(从左边进行插入)
         * @param node
         */
        public void addHead(Node<K, V> node) {
            // 构建节点的指向关系
            node.next = head.next;
            node.prev = head;
            // 改变头节点的指向关系
            this.head.next.prev = node;
            this.head.next = node;
        }

        /**
         * 从队列中删除(出队列)
         * @param node
         */
        public void remove(Node<K, V> node) {
            // 重新构建指向关系
            node.next.prev = node.prev;
            node.prev.next = node.next;

            // 清空引用，便于及时GC
            node.next = null;
            node.prev = null;
        }

        /**
         * 获取最后一个节点
         * @return
         */
        public Node<K, V> getLast() {
            // 尾节点的上一个节点为最后一个节点
            return tail.prev;
        }

    }

    /**
     * 双向链表
     */
    private DoubleLinkedList<Integer, Integer> list;
    /**
     * 缓存map
     */
    private Map<Integer, Node<Integer, Integer>> cache;
    /**
     * 容量
     */
    private int capacity;
    private static final Lock lock = new ReentrantLock();

    public MyLRU(int capacity) {
        // 初始化
        list = new DoubleLinkedList<>();
        cache = new HashMap<>();
        this.capacity = capacity;
    }

    /**
     * 获取value
     * @param key
     * @return
     */
    public int get(int key) {
        if (!cache.containsKey(key)) {
            return -1;
        }

        lock.lock();
        try {
            Node<Integer, Integer> node = cache.get(key);
            list.remove(node);
            list.addHead(node);
            return node.value;
        } finally {
            lock.unlock();
        }
    }

    /**
     * 如果key已经存在就修改，否则新增
     * @param key
     * @param value
     */
    public void put(int key, int value) {
        lock.lock();
        try {
            if (cache.containsKey(key)) {
                Node<Integer, Integer> node = cache.get(key);
                node.value = value;
                cache.put(key, node);
                list.remove(node);
                list.addHead(node);
                return;
            }

            if (cache.size() == this.capacity) {
                Node<Integer, Integer> last = list.getLast();
                cache.remove(last.key);
                list.remove(last);
            }

            Node<Integer, Integer> newNode = new Node(key, value);
            cache.put(key, newNode);
            list.addHead(newNode);
        } finally {
            lock.unlock();
        }
    }

    public static void main(String[] args) {
        MyLRU cache = new MyLRU( 2 /* 缓存容量 */ );
        cache.put(1, 1);
        cache.put(2, 2);
        System.out.println(cache.get(1));// 返回  1
        cache.put(3, 3);// 该操作会使得密钥 2 作废
        System.out.println(cache.get(2));// 返回 -1 (未找到)
        cache.put(4, 4);// 该操作会使得密钥 1 作废
        System.out.println(cache.get(1));// 返回 -1 (未找到)
        System.out.println(cache.get(3));// 返回  3
        System.out.println(cache.get(4));;// 返回  4
    }

}

```

