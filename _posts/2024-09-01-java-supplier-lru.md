---
layout: post
title: Supplier 인터페이스와 LRU
categories: [Java]
---


### Supplier 인터페이스
입력 매개변수 없이 출력을 생성하는 인터페이스  
![supplier.png](https://github.com/user-attachments/assets/14423fc5-3bc1-46b8-872e-915bfcb54732)

Supplier 인터페이스는 값을 생성하거나 제공할 수 있다.

<br><br><br>


### 사용 사례 

#### 1. 지연 초기화
객체나 데이터를 지연 초기화하여, 실제로 필요할 때 객체를 생성하여 성능을 높일 수 있다.
```java
public class LazyInitializationExample {
    private Supplier<ExpensiveObject> expensiveObjectSupplier = this::createExpensiveObject;

    private ExpensiveObject createExpensiveObject() {
        System.out.println("Expensive object created");
        return new ExpensiveObject();
    }

    public ExpensiveObject getExpensiveObject() {
        return expensiveObjectSupplier.get();
    }

    public static void main(String[] args) {
        LazyInitializationExample example = new LazyInitializationExample();
        System.out.println("Before accessing expensive object");
        ExpensiveObject obj = example.getExpensiveObject(); // 이 시점에서 객체 생성
        System.out.println("After accessing expensive object");
    }
}

class ExpensiveObject {
    public ExpensiveObject() {
        // 복잡한 초기화 작업
    }
}
```
객체의 생성이 필요할 때까지 지연되기 때문에 초기화 시점에 대한 제어가 가능하며, 따라서 불필요한 리소스 사용을 줄일 수 있다.


<br>

#### 2. 기본 값 제어
어떤 메서드에서 기본적으로 사용될 값을 Supplier를 통해 정의할 수 있다.
코드에서 반복적으로 사용되는 기본값을 관리하거나, 다양하나 상황에서 기본 값을 다르게 설정할 때 용이하다.
```java
import java.util.function.Supplier;

public class DefaultValueExample {
    private Supplier<String> defaultNameSupplier = () -> "Default Name";

    public String getName(String name) {
        return name != null ? name : defaultNameSupplier.get();
    }

    public static void main(String[] args) {
        DefaultValueExample example = new DefaultValueExample();
        System.out.println(example.getName(null)); // 출력: Default Name
        System.out.println(example.getName("John")); // 출력: John
    }
}
```


<br>


#### 3. 캐싱
특정 계산의 결과를 캐싱할 수 있다.  
최초 계산 후 캐싱하여 이후 호출시 동일한 값을 반환한다.  
값을 재사용함으로써 성능을 최적화할 수 있다.
```java
import java.util.function.Supplier;

public class MemoizationExample {
    private Supplier<Integer> expensiveCalculation = this::calculate;

    private Integer calculate() {
        System.out.println("Performing expensive calculation...");
        return 42; // 복잡한 계산의 결과
    }

    public Integer getCalculationResult() {
        return expensiveCalculation.get();
    }

    public static void main(String[] args) {
        MemoizationExample example = new MemoizationExample();
        System.out.println(example.getCalculationResult()); // 첫 번째 호출: 계산 수행
        System.out.println(example.getCalculationResult()); // 두 번째 호출: 캐시된 결과 반환
    }
}

```

<br>

#### 4. 흐름 제어
코드의 실행 흐름을 유연하게 제어할 수 있으며, 조건에 따라 특정 코드를 실행하거나 실행하지 않도록 설정할 수 있다.
특정 값이나 객체의 생성 타이밍을 설정할 수 있고, 반복적인 코드의 중복을 줄일 때 유용하다.
```java
import java.util.function.Supplier;

public class ControlFlowExample {
    private Supplier<String> debugMessageSupplier = () -> "Debug: Something went wrong!";

    public void performAction(boolean debug) {
        if (debug) {
            System.out.println(debugMessageSupplier.get()); // 디버그 메시지 출력
        } else {
            System.out.println("Action performed successfully");
        }
    }

    public static void main(String[] args) {
        ControlFlowExample example = new ControlFlowExample();
        example.performAction(true);  // 디버그 모드
        example.performAction(false); // 일반 모드
    }
}

```

<br><br><br>

## Supplier와 캐싱 최적화 전략
캐싱 최적화를 통해 속도를 개선하고, 자원을 절약하고 캐시된 데이터를 최신 상태를 유지한다.  
주로 자주 조회되는 데이터, 높은 비용, 제한된 자원, 데이터가 최신으로 유지되어야 하는 경우에 캐싱 전략을 최적화 한다.
  
#### 1. 캐시 만료 시간 설정
캐시된 데이터가 오랫동안 사용되지 않으면, 그 데이터는 오래된 정보가 된다. 이 경우 만료 시간을 설정하여 최신 데이터를 유지하고, 필요하지 않은 데이터를 자동으로 제거할 수 있다.
Supplier를 사용하여 만료 시간에 따라 새로 계산된 값을 캐싱한다.  
  
#### 2.LRU (Least Recently Used) 캐싱 알고리즘
가장 오랫동안 사용되지 않은 데이터를 우선적으로 제거하여, 메모리 사용을 최적화한다.   
LinkedHashMap과 같은 자료구조를 사용하여 LRU 캐시를 쉽게 구현할 수 있다.   

Supplier는 LRU 캐시 내에서 데이터를 처음 요청받을 때 실제로 값을 생성할 수 있는 방법으로 사용될 수 있다.
  
#### 3.Cache Invalidation
캐시된 데이터가 더 이상 유효하지 않을 때, 수동 또는 자동으로 캐시를 무효화한다.  
이는 최신 데이터가 필요하거나, 특정 이벤트가 발생했을 때 유용하,.
위의 CachedValue 클래스에서 invalidateCache 메서드는 수동으로 캐시를 무효화할 수 있는 방법을 제공한다.
  
#### 4.Lazy Loading
처음에 데이터를 로드하지 않고, 필요할 때만 데이터를 로드한다.  이 전략은 초기 로딩 시간을 줄이고, 불필요한 리소스 사용을 방지한다.  
Supplier는 Lazy Loading을 구현하는 데 필수 도구로, 값이 필요할 때만 생성되도록 한다.  
  
#### 5.동시성 제어
여러 스레드가 동시에 캐시에 접근하는 경우, 캐시의 일관성을 유지하기 위한 동시성 제어가 필요하다.  
ConcurrentHashMap과 같은 스레드 안전한 자료 구조를 사용하거나, synchronized 블록을 사용하여 동시성 문제를 해결할 수 있다.  


<br><br><br>


## Least Recently Used (LRU)?
가장 오랫동안 사용되지 않은 데이터를 우선적으로 캐시에서 제거하는 방법으로, 이를 통해 최근에 사용한 데이터는 캐시에 유지할 수 있다.

메모리가 제한된 환경에서 오래된 데이터는 제거하고 최신 데이터를 유지하여 메모리를 효율적으로 관리할 수 있다. 또한 자주 사용되는 데이터에 빠르게 접근할 수 있도록 하여 성능을 높일 수 있따. 또한 최신의 데이터를 유지하여 일관성을 보장할 수 있다.

LRU 알고리즘의 핵심은 각 데이터의 사용 시점(최근 사용 시간)을 추적하는 것이다. 

- 데이터 접근 시점 갱신: 데이터를 접근(읽기/쓰기)할 때마다 그 데이터의 최근 사용 시간을 갱신한다.
- 캐시 가득 참: 캐시가 가득 차서 새로운 데이터를 추가해야 할 경우, 가장 오랫동안 사용되지 않은 데이터를 제거한다.
- 새로운 데이터 추가: 제거된 자리에 새로운 데이터를 추가한다.


LRU 캐싱은 이중 연결 리스트, 해시맵을 사용하여 구현할 수 있다.  

- 이중 연결 리스트 (Doubly Linked List): 노드의 삽입, 삭제를 효율적으로 수행할 수 있으며, 가장 최근에 사용된 데이터를 리스트의 앞쪽에 위치시키고, 가장 오래된 데이터를 리스트의 뒤쪽에 위치시킨다.
- 해시맵 (HashMap): 데이터의 빠른 조회와 참조를 위해 사용하며, 키를 통해 해당 노드를 빠르게 찾을 수 있다.  


<br>



### LRU 캐싱 알고리즘의 데이터 구조
새로운 데이터가 추가될 때마다 해당 데이터를 가장 최근에 사용된 데이터로 갱신해야 한다.   
최근에 사용된 데이터를 LinkedHashMap의 뒤로 이동시키고, 가장 앞에 있는 데이터를 LRU 데이터로 판별하여 삭제하는 메커니즘을 구현해야 한다.  
또한, 삭제될 때를 예측하여 불필요한 데이터를 즉시 삭제해야 한다.    
  
- HashMap을 확장한 구조
- doubleLinkedList 형태

<br>

#### DoubleLinkedList?
LinkedList를 이중으로 사용하여 요소의 순서를 유지하는 리스트로,
자바의 링크드리스트는 더블링크드리스트로 구현되어있다.


LinkedHashMap에는 accessOrder 이라는 값이 있다.


Entry 에 access 하는 mode를 나타낸다
- true : access 빈도 낮은 것부터 접근
- false : 입력된 순서로 Entry 에 접근


linkedList는 키, 값, next, after, before를 갖는다.

- before : 이 노드 앞에 삽입된 노드를 가리킴
- after : 이 노드 뒤에 삽입된 노드를 가리킴
- key : 제공된 키
- value : 제공된 값
- next : 배열 테이블의 동일한 버킷에 있는 다음 노드를 가리킴

<br>  

#### HashMap과의 차이
HashMap을 확장한 구조이기 때문에 HashMap의 장점 + 순서보장을 할 수 있는 구조가 linkedHashMap라고 할 수 있다.  
  
순서 지정 기능으로 인해 HashMap보다 더 많은 메모리가 필요하다.
시간복잡도는 HashMap과 동일하게 get,put,remove,containsKey 메소드를 호출할 때 O(1)을 갖는다.
HashMap과 마찬가지로 동기화 처리가 되어있지 않기 때문에 multi-thread환경에서 사용은 적절하지 않다.

  
  
LinkedHashMap 를 사용하여 LRU 캐싱 알고리즘을 구현하고, 데이터를 접근할 때마다 해당 데이터의 "사용 시간" 또는 "접근 시간"을 업데이트하여 최근에 사용된 데이터를 파악하, 
이를 바탕으로 가장 오랫동안 사용되지 않은 데이터를 식별하고 처리한다.  





<br>



### LRU 캐싱 알고리즘을 구현할 때 고려해야 할 핵심 요소
이중 연결 리스트와 해시맵의 조합 형태로,
LinkedHashMap의 생성자에서 accessOrder 파라미터를 true로 설정하면, 데이터가 접근될 때마다 노드가 리스트의 앞쪽으로 이동하게 되어 LRU 캐싱을 자동으로 구현한다.  

이 코드에서 removeEldestEntry 메서드는 캐시의 크기가 설정된 용량을 초과할 때 가장 오래된 항목을 자동으로 제거한다.
```java
import java.util.LinkedHashMap;
import java.util.Map;

public class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int capacity;

    public LRUCache(int capacity) {
        super(capacity, 0.75f, true);
        this.capacity = capacity;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > capacity;
    }
}

```


#### 1. 시간 복잡도 최적화
- 빠른 데이터 접근: 캐시된 데이터를 빠르게 접근할 수 있어야 합니다. 이를 위해 HashMap을 사용하여 O(1) 시간 복잡도로 데이터를 검색할 수 있도록 한다.
- 빠른 데이터 갱신: 이중 연결 리스트를 사용하여 삽입, 삭제시 캐시된 항목을 리스트의 처음이나 끝으로 쉽게 이동시킬 수 있다.
#### 2. 메모리 효율성
- 메모리 사용량: HashMap과 이중 연결 리스트를 함께 사용하면 캐시 효율성이 높아지지만, 추가적인 메모리 오버헤드가 발생한다.
- 캐시 크기 설정: 캐시 크기가 너무 작으면 빈번한 캐시 미스가 발생하고, 너무 크면 메모리 낭비가 발생한다. 따라서, 적절한 캐시 크기를 설정해야 한다.
#### 3. 캐시 갱신
- 데이터 삽입 정책: 새로운 데이터가 캐시에 삽입되면, 이를 가장 최근에 사용된 데이터로 간주하고 이중 연결 리스트의 맨 앞이 된다.  
- 캐시 크기 초과 시 정책: 캐시가 가득 찼을 때 새로운 데이터를 추가해야 하는 경우, 이중 연결 리스트의 맨 끝에 있는, 즉 가장 오랫동안 사용되지 않은 데이터를 제거한다.
#### 4. 동시성 관리
- 스레드 안전성: 멀티스레드 환경에서 캐시가 안전하게 작동하도록 동기화가 필요하다.  
  동기화는 성능 저하를 유발할 수 있으므로 필요한 부분에만 최소한으로 적용해야 한다.
#### 5. 캐시의 초기화 및 무효화
- 캐시 초기화: 시스템 시작 시 캐시를 미리 채우거나, 캐시가 가득 찼을 때 특정 조건에 따라 캐시를 초기화할 수 있어야 한다.
- 캐시 무효화: 데이터가 갱신되거나 시스템 환경이 변할 때, 캐시의 특정 데이터를 무효화할 필요가 있다.  
  이를 통해 캐시가 최신 상태를 유지하도록 할 수 있다.
#### 6. 추가적인 최적화 기법
- 사용 패턴 분석: 캐시 사용 패턴을 분석하여 LRU 외에 다른 캐싱 전략이 더 적합한지 평가할 수 있다.   
  예를 들어, 자주 사용되는 데이터는 캐시에 오래 남도록 하는 전략을 추가할 수 있다.
- 다중 레벨 캐싱: 메모리와 디스크 또는 네트워크 캐시를 조합한 다중 레벨 캐싱을 고려할 수 있다.  
  메모리에 여유가 없을 때 디스크 기반 캐시를 사용하여 적중률을 높일 수 있다.


