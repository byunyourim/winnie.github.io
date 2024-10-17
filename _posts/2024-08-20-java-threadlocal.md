---
layout: post
title: ThreadLocal
categories: [Java]
---


## ThreadLocal
각각의 스레드 별로 별도의 저장 공간을 제공하는 컨테이너로 스레드 단위로 로컬 변수를 할당할 수 있다.  

각 스레드가 자신만의 데이터를 가질 수 있고, 다른 스레드와 공유하지 않아, 멀티 스레드 환경에서도 스레드 간 데이터 공유와 동기화 문제를 해결할 수 있다.  

일반 변수의 수명은 특정 코드 블록(메서드, for 블록범위 등) 내에서만 유효하다.

반면에 ThreadLocal을 이용하면 쓰레드 영역에 변수를 설정할 수 있기 때문에, 특정 쓰레드가 실행하는 모든 코드에서 그 쓰레드에 설정된 변수 값을 사용할 수 있도록 해준다. 

<br>

#### 특징
- 각 스레드는 ThreadLocal객체에 저장된 값을 독립적으로 관리하기 때문에, 다른 스레드에서 같은 ThreadLocal 객체를 사용하더라도, 스레드의 값은 서로 다르다.
- 특정 스레드가 ThreadLocal에 저장한 값은 그 스레드가 종료될 때까지 유지되기 때문에, 특정 상태나 데이터를 유지할 수 있다.
- ThreadLocal 객체를 생성하고, set(), get(), remove() 메서드를 사용하여 값을 설정, 사용하고 삭제할 수 있다.



### 사용법
- ThreadLocal 객체를 생성한다.
- ThreadLocal.set() 메서드를 이용해서 현재 쓰레드의 로컬 변수에 값을 저장한다.
- ThreadLocal.get() 메서드를 이용해서 현재 쓰레드의 로컬 변수 값을 가져온다.
- ThreadLocal.remove() 메서드를 이용해서 현재 쓰레드의 로컬 변수 값을 삭제한다.



```java
public class ThreadLocalExample {
    // 현재 스레드와 관련된 로컬 변수 생성
    private static final ThreadLocal<Integer> threadLocalValue = ThreadLocal.withInitial(() -> 0);

    public static void main(String[] args) {
        Runnable task = () -> {
            // 값 세팅
            threadLocalValue.set((int) (Math.random() * 100));
            System.out.println("Thread local value: " + threadLocalValue.get());
        };

        Thread thread1 = new Thread(task);
        Thread thread2 = new Thread(task);
        Thread thread3 = new Thread(task);

        thread1.start();
        thread2.start();
        thread3.start();

        // 값 읽어오
        System.out.println(threadLocalValue.get());
    }
}

```


#### 사용
ThreadLocal은 한 스레드에서 실행되는 코드가 동일한 객체를 사용할 수 있도록 해 주기 때문에 쓰레드와 관련된 코드에서 파라미터를 사용하지 않고 객체를 전파하기 위한 용도로 주로 사용한다.

- Spring Security에서 사용자 인증 정보를 전파할 때 사용된다. 인증 정보를 스레드 로컬 저장소에 저장하여 해당 스레드 내에서 어디서든 참조 가능하도록 한다.
- 각 스레드가 독립적인 데이터베이스 연결을 유지할 수 있게 하여, 스레드 간의 데이터베이스 연결 충돌을 방지합니다.
- 특정 스레드에서 트랜잭션 정보를 저장, 관리할 수 있다.
- 트랜잭션 매니저는 트랜잭션 컨텍스트(트랜잭션 ID)를 스레드 단위로 관리한다. 이를 통해 스레드에서 트랜잭션이 유지되는 동안 트랜잭션 정보를 안전하게 저장하고, 다른 스레드와 트랜잭션이 섞이지 않도록 한다.
- 각 스레드가 독립적인 데이터를 보관할 수 있게 하여 스레드 안전성을 보장하며, 동시에 여러 스레드가 같은 변수를 수정하지 않게 하여 **경합 조건(race condition)**을 예방할 수 있.
이 외에도 쓰레드 기준으로 동작해야 하는 기능을 구현할 때 ThreadLocal을 유용하게 사용할 수 있다.

<br>

하지만 스레드가 종료된 후에도 ThreadLocal에 정보가 남아있어 메모리 누수가 생길 수 있다. 따라서 메모리 누수를 방지하기 위해서는 스레드의 사용이 끝난 후
remove 메서드를 호출해 값을 삭제하는 것이 좋다.    
또한 ThreadLocal의 사용은 상태를 관리하는데 오히려 더 복잡해질 수 있다.






<br><br><br>



## ThreadLocal 메서드


### ThreadLocal 클래스의 initialValue() 메서드
각 스레드가 독립적으로 관리해야 하는 데이터의 초기값을 설정할 때 사용되며 로그인 사용자 정보, 트랜잭션 관리를 위한 초기 상태등을 초기화 한다.  




<br>


### withInitial()
각 스레드가 처음 사용할 때 초기값을 지정한다.  
스레드가 해당 변수에 처음 접근할 때만 호출되며, 이후에는 호출되지 않는다.
```java
 private static ThreadLocal<Integer> threadLocalValue = ThreadLocal.withInitial(() -> 1);
```

### set(T value)
ThreadLocal 변수에 값을 저장한다. 각 스레드마다 독립적으로 값을 저장하기 때문에 다른 스레드의 값을 볼 수 없다.  

<br>

### get()
현재 스레드에 저장된 값을 반환한다. 값이 설정된 적이 없으면 기본 값은 null이 된다.

<br>

### remove()
현재 스레드의 ThreadLocal 값을 제거한다.   
이 메서드는 메모리 누수를 방지하는 데 중요한 역할을 하나다. 특히 스레드 풀을 사용하는 경우, 스레드가 재사용 된다ㅏ.   
이전 스레드에서 사용한 값이 남아있을 수 있기 때문에 명시적으로 remove()를 호출해야 한다.

<br>

remove() 메서드를 호출 시, 현재 스레드와 연결된 ThreadLocal 값이 ThreadLocalMap에서 제거된다.
    
1. remove() 메서드 호출 시점
- remove() 메서드가 호출되면, 현재 스레드에 할당된 ThreadLocalMap에서 해당 ThreadLocal 객체에 대한 참조가 삭제된다.
- 따라서, 해당 스레드와 연결된 ThreadLocal 값에 더 이상 접근할 수 없다.  
- ThreadLocal에 저장된 값에 대한 참조가 사라져, 해당 값은 더 이상 사용되지 않기 때문에 JVM의 Garbage Collector(GC)에 의해 수거될 수 있는 상태가 된다.

2. 실제 메모리에서의 삭제 시점
   ThreadLocalMap에서 참조가 제거되면, 더 이상 참조되지 않기 때문에 GC에 의해서 수거된다.
   따라서 GC의 작동 시점에 달려 있다.

3. 가비지 컬렉션과의 관계
약한 참조(Weak Reference): ThreadLocalMap은 ThreadLocal 키를 약한 참조로 저장한다.  
ThreadLocal 객체가 더 이상 강한 참조를 가지고 있지 않으면 GC에 의해 수거될 수 있다. 
하지만, ThreadLocal에 연결된 값은 여전히 강한 참조로 유지되기 때문에 remove()를 호출해 해당 참조를 해제하지 않으면 메모리 누수가 발생한다.




<br><br><br>



### 스레드 풀의 구조
- 스레드 풀은 일정 수의 스레드를 미리 생성한 후, 작업이 들어오면 그 중 쉬고 있는 스레드를 할당해 작업을 처리한다.  
- 작업이 끝나면 그 스레드는 다시 풀로 반환되어 재사용된다.
- 톰캣과 같은 서버는 요청이 들어올 때마다 새로운 스레드를 생성하지 않고, 스레드 풀을 사용해 요청을 처리한다.



<br>

### 왜 ThreadLocal과 스레드 풀에서 주의해야 하나?
ThreadLocal 변수는 스레드마다 고유한 값을 저장할 수 있지만, 스레드를 재사용하는 경우 이전 작업에서 남은 값이 새로운 작업에 영향을 줄 수 있다.  
이전 사용자의 값이 남아있다면 데이터가 섞인다.  

따라서 ThreadLocal을 사용한 스레드가 작업을 완료한 후에는 반드시 remove() 메서드를 호출해 해당 값을 삭제해야, 다른 작업에 영향을 미치지 않는다.  


<br>

톰캣과 같은 서블릿 컨테이너는 스레드 풀을 사용해 HTTP 요청을 처리한다.




<br><br><br>


### 스레드 팩토리
Java에서 스레드를 생성할 때 사용되는 인터페이스로, 개발자는 스레드를 직접 커스터마이징할 수 있다.  
특히, 스레드 풀에서 새로운 스레드를 만들 때 유용하다.  

<br>

- 스레드 생성 방식을 표준화할 수 있다. 스레드의 이름, 우선순위 설정, 데몬 스레드 여부를 통일할 수 있다.
- 스레드 풀을 사용할 때, 스레드 팩토리를 지정하면 스레드 풀이 내부적으로 새로운 스레드를 생성할 때 
스레드 팩토리를 통해 생성된 스레드를 사용한다.
- 스레드팩토리를 구현하면 로깅, 모니터링 등 특정 동작을 수행하는 스레드를 만들 수 있다.  


스레드 클래스의 run 메서드에서 ThreadLocal.remove()를 호출하여 스레드 종료 전에 변수를 제거할 수 있다.  



<br>





### ThreadLocal.withInitial()
초기값을 설정하는 메서드로 변수의 초기화 로직을 간소화할 수 있다.   

모든 스레드가 동일한 초기값을 갖기 때문에 일관성을 유지할 수 있으며 초기값 설정이 변수 선언과 동시에 일어나기 때문에 코드가 가독성이 좋다.






<br><br><br>





## 제거한 변수를 다시 사용하려면?
ThreadLocal의 remove() 메서드를 호출하면 해당 스레드에 대한 값이 삭제되지만, 해당 ThreadLocal
변수가 여전히 존재하기 때문에 다시 접근할 수 있다.  

1. 초기화 : set() 메서드를 사용하여 새로운 값을 설정한다.
2. 재사용 : remove() 메서드를 사용하여 값을 제거한 후, 다시 get()메서드를 호출함면
초기값이 제공되는 경우 초기값을 반환받거나, get() 멤서드로 직접 새로운 값을 설정해야 한다.





<br><br><br>

## ThreadPoolExecutor
java.util.concurrent 패키지에 포함된 클래스 중 하나로 스레드 풀을 관리한다.  

- beforeExecute()와 afterExecute() 메서드는 작업이 실행되기 전, 후에 실행된다. 
- execute(), submit() 메서드는 작업을 큐에 추가하고 실행하는 방법을 정의한다.


### beforeExecute(), afterExecute()
#### beforeExecute()
스레드가 작업을 실행하ㅏ기 전에 호출된다.  
이 메서드를 오버라이드하여 작업 실행 전 특정 작업(로깅, 매트릭 수집)을 수행한다.
```java
protected void beforeExecute(Thread t, Runnable r) {
    super.beforeExecute(t, r);
    log.info("Before executing ====> {}", r);
}
```

<br>

#### afterExecute()
스레드가 작업을 완료한 후 호출된다.    
이 메서드를 오버라이드하여 작업 완료 후 특적 작업(로깅, 리소스 정리)을 수행한다.
```java
protected void afterExecute(Runnable r, Throwable t) {
    super.afterExecute(r, t);
    if (t != null) {
        log.info("Task encountered an exception ====> {} ", t);
    } else {
        System.out.println("Task completed successfully ====> {}", r);
    }
}
```


<br><br><br>




### execute(), submit()
위의 메서드를 사용하여 작업을 큐에 추가하고 실행할 수 있다. 

#### execute()
Runnalbe 작업을 받아들이고 실행한다.  
결과를 반환하지 않고, 작업이 완료될 때 예외가 발생하는 경우 예외를 처리할 수 없닫.  
주로 결과가 필요없는 단순작업에 사용된다.

```java
ThreadPoolExecutor executor = new ThreadPoolExecutor(2, 4, 60, TimeUnit.SECONDS, new LinkedBlockingQueue<>());
executor.execute(() -> {
    // 로직 수행
});
```

<br>

#### submit()
Callable 작업을 실행하고, 결과를 Future 객체로 반환한다.     

작업의 결과를 비동기적으로 받을 수 있으며, 예외가 발생할 경우 Future를 통해 예외를 확인할 수 있다.
```java
Future<Integer> future = executor.submit(() -> {
    // 로직 수행
    return 42;
});
```