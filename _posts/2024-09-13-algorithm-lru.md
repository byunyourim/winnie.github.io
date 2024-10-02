---
layout: post
title: LRU(Least Recently Used)Cache 알고리즘
categories: [Algorithm]
---


캐시는 데이터나 값을 미리 복사해 놓는 임시 저장소를 말한다.
데이터를 접근하는 시간이 오래 걸리는 경우나 값을 다시 계산하는 시간을 절약하고 싶은 경우 사용한다.
캐시에 데이터를 미리 복사해 놓으면 계산이나 접근 없이 더 빠른 속도로 데이터에 접근이 가능하다.  


<br>

LRU (Least Recently Used)
**가장 최근에 사용되지 않은 데이터를 제거하는 방식**  

빠른 접근과, 업데이터가 가능하지만 많ㄱ은 공간을 차지한다는 단점이 있다.

새로운 데이터가 캐시에 저장될 때, 캐시에 있는 데이터에 접근할 때마다 해당 데이터를
**가장 최근에 사용된 데이터**로 표시하고 가장 오래 전에 사용된 데이터를 제거한다.
따라서 가장 최근에 사용된 데이터를 캐시에 보존한다.  

이중 연결 리스트나 해시 맵과 같은 자료 구조를 사용하여 구현할 수 있다.


<br>

### 구현
![lru.png](https://github.com/user-attachments/assets/863a880f-3ede-4108-8446-4b1e659fd9c4)


  
  






## 구현
```java
public class LRUCache {

    class Node {
        int key, value;
        Node prev, next;
        Node(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }

    private HashMap<Integer, Node> cache;
    private int capacity, size;
    private Node head, tail;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        this.size = 0;
        cache = new HashMap<>();

        this.head = new Node(0, 0);
        this.tail = new Node(0, 0);
        head.next = tail;
        tail.prev = head;
    }

    public int get(int key) {
        Node node = cache.get(key);
        if (node == null) return -1;

        moveToHead(node);
        return node.value;
    }

    private void moveToHead(Node node) {
        removeNode(node);
        addToHead(node);
    }

    private void removeNode(Node node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    private void addToHead(Node node) {
        node.prev = head;
        node.next = head.next;

        head.next.prev = node;
        head.next = node;
    }

    public void put(int key, int value) {
        Node node = cache.get(key);

        if (node == null) {
            Node newNode = new Node(key, value);
            cache.put(key, newNode);
            addToHead(newNode);
            size++;

            if (size > capacity) {
                Node tail = removeTail();
                cache.remove(tail.key);
                size--;
            }
        } else {
            node.value = value;
            moveToHead(node);
        }
    }

    private Node removeTail() {
        Node res = tail.prev;
        removeNode(res);
        return res;
    }
}
```


