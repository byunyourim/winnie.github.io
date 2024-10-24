---
layout: post
title: StringBuffer StringBuilder
categories: [Java]
---
 
## StringBuffer & StringBuilder
둘 다 가변 길이 문자열을 처리하는 데 사용되는 클래스이다.  
 
<br>

### StringBuffer 
동기화가 적용되어 있으며 thread-safe 로 멀티 스레드 환경에서 StringBuffer 인스턴스에 접근할 때 안전하다.   

여러 스레드가 동시에 문자열을 수정해야 하는 경우 사용된다.  
예를 들어 멀티 스레드 환경에서 공유 문자열을 처리하는 경우 사용할 수 있다.  
<script src="https://gist.github.com/byunyourim/e1e56c08b6168c66d7324a03d3c1dc3c.js"></script>


<br>


### StringBuilder
내부적으로 동기화가 적용되어 있지 않기 때문에 멀티 스레드 환경에서 안전하지 않다.  
  
단일 스레드 환경에서 문자열을 수정할 때 사용되며 성능이 중요하고 thread-safe가 필요하지 않은 경우 사용하는 것이 좋다.  
<script src="https://gist.github.com/byunyourim/3bc688cd74be0b6d4bf57f4b28300be4.js"></script>


<br><br><br>


> #### StringBuilder 가 단일 스레드 환경에서 사용하기 적합한 이유
> 동기화로 인한 오버헤드가 없어, 단일 스레드 환경에서 빠르다.
> 동기화는 메서드 호출시마다 추가적인 검사와 잠금 획득 및 해제를 필요로 하여 성능을 저하시킬 수 있다.  
>    
> 따라서 동기화가 없는 StringBuilder가 더 빠르게 실행된다.     
> 동기화가 앖는 만큼 StringBuilder의 메서드들은 더 간단하고 경량화되어 빠른 실행이 가능하며, StringBuffer 보다 더 적은 자원을 소모한다.



<br><br><br>


### 불변성
**객체가 한 번 생성된 후로 상태를 바꿀 수 없는 성질**로 여러 스레드가 동시에 읽어도 안전하며 상태가 변하지 않는다.  
따라서 동기화 처리가 없어도 여러 스레드가 동시에 접근할 수 있다.  

또한 불변 객체는 변경될 일이 없기 때문에 캐싱하여 재사용할 수 있으며, 따라서 메모리의 사용을 높일 수 있다.  
  
StringBuilder는 가변 객체로 불변 객체와 반대되는 개념이다.  

StringBuidler은 내부 상태를 변경할 수 있으며 그 과정에서 내부 배열을 재사용한다. 새로운 객체를 생성하지 않고 문자열 수정이 가능하다.
따라서 성능 측면에서는 유리할 수 있지만 멀티 스레드 환경에서는 주의해야 한다.  



<br><br><br>


### StringBuilder를 멀티 스레드 환경에서 안전하게 사용하기 위해서는
외부에서 동기화 처리를 해주어야 한다.  
  
동기화 처리 방법에는 synchronized 와 Lock이 있으며, 
내부적으로 동기화 처리가 되어있는 StringBuffer을 사용하는 방법도 있다.



<br><br><br>


### ReentrantLock과 Synchronized?
ReentrantLock과 Synchronized 둘 다 자바에서 동기화를 제공하지만 사용법과 기능 측면에서 차이가 있다.

**synchronized 키워드**는 블록이나 메서드 수준에서 동기화가 가능하다. 자동으로 락을 획득하고 해제한다.
사용이 간단하며 가독성이 좋다.
<script src="https://gist.github.com/byunyourim/d522eee32b0670d90a681e5929975f23.js"></script>


<br><br>

**ReentrantLock 클래스**는 명시적으로 락을 획득하고 해제하는 작업이 필요하다.
타임아웃을 지정할 수 있으며 Condition 객체를 사용하여 보다 세밀한 스레드 제어가 가능하고 공정성을 설정할 수 있다.
복잡한 동기화 요구에 적합하지만 코드가 복잡해질 수 있다.
<script src="https://gist.github.com/byunyourim/29938a19d1b22b786230fdc2fd6493c9.js"></script>




<br><br><br>



### ReentrantLock의 await 와 signal
ReentrantLock 클래스는 Condition 객체를 통해 스레드 간에 특정한 조건을 기다리고 신호를 보내는 기능을 제공한다.
  
await() 메서드는 현재 스레드를 일시적으로 멈추고 다른 스레드가 signal() 또는 signalAll() 을 호출할 때까지 기다린다.  

singnal() 메서드는 하나의 대기 중인 스레드에게만 신호를 보낸다.   
일반적으로 상태가 변경되어 하나의 스레만 깨어나야 할 때 사용된다.  
공유 자원의 상태가 특정 조건을 만족할 때까지 대기하도록 스레드를 설정하는 경우 사용된다.  
<script src="https://gist.github.com/byunyourim/abc5c521cbbb8b597e7ca4587f021613.js"></script>


<br><br><br>


### StringBuffer의 동기화
StringBuffer 은 내부적으로 동기화 처리가 되어있어 멀티 스레드 환경에서 안전하게 사용이 가능하다. 
  
하지만 모든 메서드가 동기화 되며 단일 스레드 환경에서도 불필요한 락을 걸고 해제하기 때문에 오버헤드가 발생할 수 있다.
복잡한 스레드 상호 작용 과정에서 데드락이 발생할 수 있으며, synchronized는 블록과 메서드 수준에서만 사용이 가능하기 때문에 세밀한 제어가 어려울 수 있다.


<br><br><br>



### 단일 스레드 환경이라면?
단일 스레드 환경에서 동기화가 동작하지 않더라도 synchronized 키워드가 있는 메서드를 호출하면
동기화에 필요한 추가적인 오버헤드가 발생한다.  
    
synchronized 메서드는 항상 모니터 락을 획득하고 해제하는 과정을 거친다. 
비록 단일 스레드 환경에서는 실제로 다른 스레드와의 경쟁이 없더라도, 자바는 이 메서드 호출 시에 락을 확인하는 작업을 한다. 이 과정이 추가적인 비용이 발생한다.  
  
JVM 레벨에서 synchronized 키워드가 붙은 메서드나 블록은 동기화 관련 로직을 처리해야 한다. 
단일 스레드라고 하더라도 이러한 추가적인 검사와 관리가 필요합니다.
반면, StringBuilder는 이런 과정이 없기 때문에 더 빠르다.


