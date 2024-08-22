---
layout: post
title: ThreadLocal
categories: Java
---


# ThreadLocal
각각의 스레드 별로 별도의 저장 공간을 제공하는 컨테이너  

각 스레드는 자신만의 별도의 데이터를 가질 수 있고, 다른 스레드와 독립적으로 데이터를 설정하고 조회할 수 있다.  
  
각 인스턴스는 특정 스레드에 연결되어 있는 데이터를 참조하며 다른 스레드에 영향을 주지 않는다.
이를 통해 멀티 스레드 환경에서도 스레드 간 데이터 공유와 동기화 문제를 해결할 수 있다.  

각 요청마다 사용자 인증 정보를 threadLocal 에 저장하여 스레드 별로 관리할 수 있고, 이를 통해 여러 요청 간 사용자 정보를 명시적으로 전달하지 않고도 사용할 수 있다.

또한 데이터베이스 트랜잭션에서 Connection 객체를 ThreadLocal에 저장하여 각 스레드가 독립적으로 데이터베이스 연결을 관리하고, 트랜잭션 내에서 데이터베이스 연결을 공유하여 스레드 간의 충돌을 방지할 수 있.
한 번 계산된 결과를 스레드별로 캐시해서 다시 계산하지 않고 재사용할 수 있다.

```java
public class ThreadLocalExample {
    private static final ThreadLocal<Integer> threadLocalValue = ThreadLocal.withInitial(() -> 0);

    public static void main(String[] args) {
        Runnable task = () -> {
            threadLocalValue.set((int) (Math.random() * 100));
            System.out.println("Thread local value: " + threadLocalValue.get());
        };

        Thread thread1 = new Thread(task);
        Thread thread2 = new Thread(task);
        Thread thread3 = new Thread(task);

        thread1.start();
        thread2.start();
        thread3.start();

        System.out.println(threadLocalValue.get());
    }
}

```

하지만 메모리 누수를 유발할 수 있으며 ThreadLocal에 저장된 데이터는 스레드가 종료되어도 자동으로 해제되지 않는다. 따라서 사용이 끝난 후 remove() 메서드를 호출해서 데이터를 삭제해야 한다.




<br>




## ThreadLocal 클래스의 initialValue() 메서드
ThreadLocal 변수가 처음 사용될 때 호출하며 각 스레드 별로 별도의 초기값을 반환한다.
  
각 스레드가 독립적으로 관리해야 하는 데이터의 초기값을 설정할 때 사용되며 로그인 사용자 정보, 트랜잭션 관리를 위한 초기 상태등을 초기화 한다.  





<br>




## 스레드 재사용과 remove()
데이터를 해제하는 메서드

스레드가 재사용되는 겨우 이전에 설정된 데이터가 존재하기 때문에 이전의 데이터와 공유될 수 있다.

ThreadLocal 변수의 데이터는 스레드마다 독립적으로 관리되지만, 스레드 풀에서 스레드가 재사용되는 동안
여러 스레드가 동시에 같은 변수에 접근하면 데이터의 일관성에 문제가 발생할 수 있다.  

따라서 remove()를 이용하여 이전의 데이터를 해제해 주어야 한다.
사용하지 않는 경우 해당 스레드의 생명 주기 동안 메모리 리소스가 유지되기 때문에 메모리 누수가 발생할 수 있다.

또한, 스레드 풀의 사용을 제한할 수 있다. 스레드 풀을 사용하지 않으면 스레드가 작업을 완료한 후 종료되기 때문에 자동으로 ThreadLocal 변수가 제거된.
사용자 정의 스레드 팩토리를 사용하여 스레드가 종료될 때 ThreadLocal 변수를 명시적으로 제거할 수 있도록 구현하는 방법도 있습니다.다





<br>




### remove()
ThreadLocal의 remove() 메서드를 호출하면, 현재 스레드와 연결된 ThreadLocal 값이 ThreadLocalMap에서 제거됩니다. 이때 변수의 삭제와 관련된 중요한 포인트는 다음과 같습니다:

1. remove() 메서드 호출 시점
   실제 동작: remove() 메서드가 호출되면, 현재 스레드에 할당된 ThreadLocalMap에서 해당 ThreadLocal 객체에 대한 참조가 삭제됩니다. 이로 인해 해당 스레드와 연결된 ThreadLocal 값은 더 이상 접근할 수 없게 됩니다.

메모리 정리: ThreadLocal에 저장된 값에 대한 참조가 사라지므로, 해당 값은 더 이상 사용되지 않으며, JVM의 Garbage Collector(GC)에 의해 수거될 수 있는 상태가 됩니다. 즉, 메모리에서 실제로 제거되는 시점은 GC가 이 객체를 수거할 때입니다.

2. 실제 메모리에서의 삭제 시점
   GC에 의해 수거: ThreadLocalMap에서 참조가 제거되면, ThreadLocal 객체와 그 값은 다른 곳에서 참조되지 않는 한 메모리에서 수거될 수 있는 상태가 됩니다. 하지만 실제로 메모리에서 언제 삭제될지는 GC의 작동 시점에 달려 있습니다.

강제 수거: 메모리 누수를 방지하기 위해, ThreadLocal.remove()를 호출한 후에는 가능한 ThreadLocal 객체가 가비지 컬렉션 대상이 되도록 강제로 참조를 해제하는 것이 좋습니다.

3. 가비지 컬렉션과의 관계
   약한 참조(Weak Reference): ThreadLocalMap은 ThreadLocal 키를 약한 참조로 저장합니다. 이는 ThreadLocal 객체가 더 이상 강한 참조를 가지고 있지 않으면 GC에 의해 수거될 수 있음을 의미합니다. 하지만, ThreadLocal에 연결된 값은 여전히 강한 참조로 유지되기 때문에 remove()를 호출해 해당 참조를 해제하지 않으면 메모리 누수가 발생할 수 있습니다.




<br>




## 스레드 팩토리
스레드 클래스의 run 메서드에섯 ThreadLocal.remove()를 호출하여 스레드 종료 전에 변수를 제거할 수 있다.
ThreadFactory 인터페이스를 구현해 새로운 스레드를 생성하고 생성된 스레드에서 remove() 메서드를 호출해 자원의 누수를 방지해야한다. 
스레드 풀은 스레드를 재사용하기 때문에 재사용 이전에 ThreadLocal 값이 남아있지 않도록, 사용후 값을 제거해주어야 한다.
remove()메서드를 이용하여 변수를 정리하여 메모리 누수를 방지해야 한다.





<br>





## set() 메서드? 
변수를 제거한 후 해당 변수를 다시 사용하기 위해 set() 메서드를 사용해 새로운 값을 설정할 수 있다.




<br>




## 스레드가 종료
ThreadLocal 변수를 제거하지 않고도 스레드가 종료될 때 자동으로 제거되는 방법은 스레드가 짧은 생명주기를 갖는 경우에 실행될 수 있습니다. ㅎ

스레드가 종료되면 스레드에 바인딩된 ThreadLocal 변수들도 함께 제거된다.

단위테스트나 통합 테이스에서도 일반적으로 테스트가 완료되면 종료되기 때문에 제거할 필요가 없다.





<br>





## ThreadLocal.withInitial()
초기값을 설정하는 메서드로 변수의 초기화 로직을 간소화할 수 있다.   

모든 스레드가 동일한 초기값을 갖기 때문에 일관성을 유지할 수 있으며 초기값 설정이 변수 선언과 동시에 일어나기 때문에 코드가 가독성이 좋다.






<br>





## 제거한 변수를 다시 사용하려면?
### 1. get() 또는 set() 메서드를 호출하여 초기화
**get() 메서드 호출**  

remove() 메서드가 호출된 후에 get() 메서드를 호출하면, ThreadLocal 변수는 초기화된다. 만약 ThreadLocal이 기본값 초기화 로직을 가지고 있다면, get() 메서드를 호출할 때 이 로직이 실행되어 새로운 값이 설정된다.
만약 초기화 로직이 없다면, get() 메서드는 null을 반환한다.


**set() 메서드 호출**  

set(T value) 메서드를 사용하여 ThreadLocal 변수에 새로운 값을 명시적으로 설정할 수 있다. 이렇게 하면 해당 스레드에 대해 새로운 값이 할당된다.





<br>




## 사용자 정의 스레드 팩토리
스레드 클래스의 run 메서드에섯 ThreadLocal.remove()를 호출하여 스레드 종료 전에 변수를 제거할 수 있다.

ThreadFactory 인터페이스를 구현해 새로운 스레드를 생성하고 생성된 스레드에서 remove() 메서드를 호출해 자원의 누수를 방지해야한다.
스레드 풀은 스레드를 재사용하기 때문에 재사용 이전에 ThreadLocal 값이 남아있지 않도록, 사용후 값을 제거해주어야 한다.
remove()메서드를 이용하여 변수를 정리하여 메모리 누수를 방지해야 한다.




<br>




remove() 메서드를 호출하는 것이 중요한 이유는 스레드가 종료되도 변수값이 메모리에 남아있기 때문에 제거해주어야한다... 메모리 누수가 발생하지 않도록
다른 방법으로는 변수를 초기화하거나 제거하여  데이터가 남지않도록 하거나 beforeExecute(), afterExecute() 메서드에서 threadLocal 변수의 값을 초기화하는 방법이 있다.





<br>




### beforeExecute(), afterExecute()
beforeExecute()와 afterExecute() 메서드를 이용 스레드가 작업을 시작하고 종료할 때 ThreadLocal 변수의 상태를 관리할 수 있다. 
주의할 점은 메서드 내부에서의 동기화 문제와, 스레드가 재사용될 때 데이터가 남지않도록 해야한다.
또한 스레드 종료 후 변수를 제거하여 메모리 누수를 방지해야 한다.




<br>




### ThreadPoolExecutor의 execute(), submit()
execute는 Runnable 작업을 제출하고 결과를 반환하지 않는다
submit는 Runnable 또는 Callable 작업을 제출하고 Future를 통해 결과를 얻을 수 있다.