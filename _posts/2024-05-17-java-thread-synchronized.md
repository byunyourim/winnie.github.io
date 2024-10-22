---
layout: post
title: Thread 구현 동기화와 Lock

categories: [Java]
---
## 스레드 구현 방법
#### 1. 스레드를 확장해서 run() 메서드를 오버라이드한 후 사용
```java
public class ThreadExtends extends Thread {
    private String message;

    public ThreadExtends(String message) {
        this.message = message;
    }

    @Override
    public void run() {
        System.out.println("Thread started: " + message);
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            System.out.println("Thread interrupted: " + message);
        }
        System.out.println("Thread ended: " + message);
    }

    public static void main(String[] args) {
        ThreadExtends thread1 = new ThreadExtends("Hello");
        ThreadExtends thread2 = new ThreadExtends("World");

        // 스레드 시작
        thread1.start();
        thread2.start();
    }
}

```

<br>

#### 2. Runnable 인터페이스를 구현해서 스레드의 파라미터로 넘기는 방법
```java
public class ThreadRunnable implements Runnable {
    private String message;

    public ThreadRunnable(String message) {
        this.message = message;
    }

    @Override
    public void run() {
        System.out.println("Thread started: " + message);
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            System.out.println("Thread interrupted: " + message);
        }
        System.out.println("Thread ended : " + message);
    }

    public static void main(String[] args) {
        ThreadRunnable runnable1 = new ThreadRunnable("Hello");
        ThreadRunnable runnable2 = new ThreadRunnable("World");

        Thread thread1 = new Thread(runnable1);
        Thread thread2 = new Thread(runnable2);

        thread1.start();
        thread2.start();
    }
}

```


<br><br><br>


## Thread 와 임계영역
스레드가 동시에 임계 구역에 접근하는 경우 데이터의 일관성을 보장하기 위해 상호 배제 개념을 사용한다.

<br>

> #### 임계영역이란?
> **다중 스레드 환경에서 공유 자원에 접근하는 코드 영역**    
> 여러 스레드가 동시에 임계 영역에 접근하게 되면 데이터의 일관성이 깨질 수 있다.


<br>

이러한 문제를 방지하기 위한 방법으로 **상호 배제**와 **동기화 기법**이 있다.  

### 상호 배제 (Mutual Exclusion)
하나의 스레드만 임계 영역에 진입할 수 있도록 한다.  
즉, 하나의 스레드가 임계영역에 들어가 있으면, 다른 스레드는 그 스레드가 나올 때까지 기다려야 한다.  
이를 통해 공유 자원에 대한 접근을 순차적으로 제어한다.  

<br>

### 동기화 (Synchronization)
동기화는 스레드들이 공유 자원에 접근하는 순서를 제어하는 방법이다.  
이를 통해 작업 간 충돌을 방지하고, 작업 완료 전에 다른 스레드가 임계 영역에 들어가는 것을 막는다.



<br><br>



#### 상호배제를 구현하기 위한 동기화 방법

#### 1. synchronized 키워드

특정 메서드나 블록을 해당 키워드로 지정하여 한 번에 하나의 스레드만 블록에 접근할 수 있도록 합니다.
```java
public synchronized void add1(int v) {
	this.count += v;
}
// 한 번에 하나의 스레드만 이 메소드를 실행할 수 있다.
// 공유자원인 count 변수에 대한 동시 접근을 방지한다.

public void add2(int v) {
    synchronized(this) {
        this.count += v;
    }
}
// 블록을 사용하여 특정 객체에 대해 동기화 수행
// 메서드 전체를 동기화하는 것보다 세밀한 제어 가능
// this 객체에 대해 동기화블록을 사용하여 count 변수 값 변경
```

<br>

#### 2. lock 인터페이스
더 정교한 제어를 위해 java.util.concurrent.locks.Lock 인터페이스를 사용할 수 있다.  
ReentrantLock 이 대표적인 구현체로  
명시적으로 락을 획득하고 해제할 수 있으며, try-lock, 타임아웃을 이용한 잠금 등 다양한 방법으로 동기화 제어가 가능하다.
```java
private final Lock lock = new ReentrantLock();

public void add(int v) {
	lock.lock();
	try {
		this.count += value;
	} finally {
		lock.unlock();
	}
}
// lock() 메서드로 잠금을 획득하고 finally 블록에서 unlock() 메서드로 잠금 해제
// synchronized 보다 유연한 동기화 제어가 가능합니다.
// tryLock() 메서드를 제공하여 잠금을 시도할 수 있습니다.
// tryLock() : 잠금을 획들할 수 있으면 true, 아닌 경우 false 반환
// 이를 통해 잠금획득에 실패했을 때 대기하지 않고 다른 작업을 수행할 수 있습니다.
```  
  
<br>

#### 3. 세마포어
세마포어는 특정 개수의 스레드가 임계영역에 접근할 수 있도록 허용하는 방법이다.
세마포어의 값이 1 이면 뮤텍스와 동일한 역할을 한다.


<br>

임계영역을 적절히 관리하여 병행성 문제를 해결하고 데이터 일관성을 유지할 수 있다.  
하지만 잘못 사용하게 되면, 성능 저하와 데드락 문제가 생길 수 있다.