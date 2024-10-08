---
layout: post
title: HashMap의 구조
categories: [Algorithm]
---

**키-값**의 쌍으로 데이터를 저장하는 자료구조  

  
각 키를 해시 함수로 변환하여 배열의 인덱스에 저장하여 데이터 추가, 검색, 삭제 등의 작업을 수행한다.
키의 중복을 허용하지 않으며, 하나의 키에 하나의 값이 매핑된다.

![hashmap.png](https://github.com/user-attachments/assets/8bffadb9-b7c8-4540-b120-da08e088dec4)  


<br>



해시 함수는 키를 해시 코드로 변환하여 HashMap의 버킷 인덱스를 정하는데, 서로 다른 두 개의 키가 같은 해시 값을 갖는 경우가 발생한다.
이 현상을 해시 충돌이라고 한다.  


HashMap에서는 해시 충돌을 방지하기 위해 Chaining, Open Addressing을 이용한다.
  
<br>

- **Chaining**  
  충돌이 발생한 버킷 안에서 연결리스트, 트리 구조로 여러 키-값을 저장하는 방식
- **Open addressing**  
  다른 빈 버킷을 찾아 데이터를 저장하는 방식



![hashmap2.png](https://github.com/user-attachments/assets/3f8e2ac4-9be9-40fe-9861-326f10766a20)


<br><br>


자바의 HashMap은 hashCode() 메서드를 이용해 해시 값을 얻고, 배열이 찬 경우 resize() 메서드를 이용하여 
버킷 배열의 크기를 늘릴 수 있다. 기존의 항목을 새로 해싱하여 새 배열에 재분배한다.



> ##### 로드 팩터 (Load Factor)
> Load Factor은 HashMap이 일정 수준 채워지면 버킷 배열을 확장할지 정하는 기준이다.
> 기본적으로 Java의 HashMap은 로드 팩터 0.75를 사용한다. 따라서 버킷의 75%가 차면 배열의 크기를 resizing하고 재해싱 한다.
> 로드 팩터를 낮게 설정하는 경우 리사이징이 더 자주 일어나며, 높게 설정하는 경우 충돌 가능성이 증가한다.

<br>

HashMap의 시간 복잡도는 평균적으로 **O(1)**이다.
하지만, 해시 충돌이 많이 발생하면, 특정 버킷에 연결된 리스트를 탐색해야 하기 때문에 시간 복잡도가  **O(n)**에 도달한다.
하지만, Java 8 이후 트리 구조를 사용하여 최악의 경우에도 **O(log n)**으로 성능을 보장할 수 있.



<br><br><br>   



## 주요 메서드
#### 1. put(key, value)
키와 값을 HashMap에 추가한다.
```java
HashMap<String, String> dataMap = new HashMap<>():
dataMap.put("key1", "value1");
dataMap.put("key2", "value2");
dataMap.put("key3", "value3");
```
1. 해시 함수를 통해 키를 버킷 배열의 인덱스로 변환한다.
2. 해당 버킷이 비어있는 경우 새로운 노드를 추가한다.
3. 충돌이 발생하는 경우, 연결 리스트를 순회하여 동일 키가 있는지 확인
    - 동일 키가 있는 경우 값 update
    - 동일 키가 없는 경우 리스트의 끝에 새 노드 추가
    
<br>

#### 2. get(key)
키에 대응하는 값을 반환한다.
```java
dataMap.get("key1");
```
1. 키를 해시 함수로 변환하여 버킷의 인덱스를 찾는다.
2. 해당 버킷에 연결된 리스트를 순회하여 일치하는 키를 찾아 대응하는 값을 반환한다.
3. 키가 없는 경우 -1 또는 null 반

<br>

#### 3. remove(key)
해당 키를 삭제한다.
```java
dataMap.remove("key1");
```
1. 키를 해시 함수로 변환해 버킷을 찾는다.
2. 해당 버킷에서 연결 리스트를 순회하여 키를 찾는다.
3. 키를 찾아 해당 노드를 리스트에서 제거한다.





<br><br><br>



### HashMap 구조
버킷 배열과 노드로 구성  

- 버킷 배열 : 키의 해시 값에 따라 값이 저장되는 배열 
- 노드 : 각 노드는 키-값과 다음 노드 정보를 갖으며, 연결리스트, 트리 형태로 연결된다.


> #### 버킷
> 배열의 한 칸으로, 해시 함수에 의해 변환된 키가 매핑되는 위치이다.
버킷 배열의 크기는 보통 2의 제곱수로 설정되어 있으며, 해시 충돌을 처리하기 위해 각 버킷에 여러 개의 키-값이 저장될 수 있다.



<br><br><br>



배열과 연결리스트의 결합으로 해시맵은 키에 대해 해시 함수를 사용해서 키가 저장될 배열의 인덱스를 정한다. 만약 서로 다른 두 키가 동일한 해시 값을
갖게 되면, 해시 충둘이 발생한다.
충돌이 발생하면, 같은 버킷에 여러 엔트리가 저장되는데, 이때 연결리스트로 연결한다.


자바 8 이전에는 충돌이 발생하는 경우 연결리스트를 이용하였지만, 하나의 버킷에 많은 엔트리가 저장되면,
검색 속도가 O(n)이 된다. 이를 개선하기 위해 엔트리가 일정 수준을 넘어가게되면 이진트리로 변환된다.
이진 트리는 최악의 경우에도 검색 성능이 O(log n)으로 유지된다.


해시코드를 이용해 데이터의 저장 위치를 빠르게 찾을 수 있으며, 검색 속도를 향상시킨다.



<br><br><br>



## MyhashMap 구현
```java
public class MyHashMap {

    class Node {
        int key, value;
        Node next;

        Node(int key, int value) {
            this.key = key;
            this.value = value;
            this.next = null;
        }
    }

    private Node[] buckets;
    private int capacity = 10000;

    public MyHashMap() {
        buckets = new Node[capacity];
    }

    public void put(int key, int value) {
        int index = hash(key);
        // insert or update
        if (buckets[index] == null) {   // 없는 경우 새로운 노드 추가
            buckets[index] = new Node(key, value);
        } else {    // 있는 경우 연결 리스트를 탐색, 키가 있으면 update, 없으면 node 추
            Node current = buckets[index];
            while (current != null) {
                if (current.key == key) {
                    current.value = value;
                    return;
                }
                if (current.next == null) {
                    current.next = new Node(key, value);
                    return;
                }
                current = current.next;
            }
        }
    }

    private int hash(int key) {
        return key % capacity;
    }

    public int get(int key) {
        int index = hash(key);
        Node current = buckets[index];
        while (current != null) {
            if (current.key == key) {
                return current.value;
            }
            current = current.next;
        }
        return -1;
    }

    public void remove(int key) {
        int index = hash(key);
        Node current = buckets[index];
        Node prev = null;
        while (current != null) {
            if (current.key == key) {
                if (prev == null) {
                    buckets[index] = current.next;
                } else {
                    prev.next = current.next;
                }
            }
            prev = current;
            current = current.next;
        }
    }
}
```