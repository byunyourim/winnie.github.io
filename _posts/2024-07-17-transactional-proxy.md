---
layout: post
title: Transactional Annotation과 Proxy
categories: [Spring]
---


<br>

**@Transactional**은 메서드 실행 전후에 트랜잭션을 시작하고 커밋 또는 롤백하는 기능을 제공하는 어노테이션으로 스프링에서 트랜잭션 관리를 자동으로 처리한다.

<br>

### 트랜잭션 경계 설정
- **트랜잭션 시작:**
    - @Transactional이 적용된 메서드를 호출할 때, 먼저 PlatformTransactionManager를 사용하여 트랜잭션을 시작한다.
    - 트랜잭션 메니저는 트랜잭션 경계를 설정하고, 데이터베이스와의 연결을 트랜잭션 모드로 전환다.
    - 이를 통해 해당 메서드가 트랜잭션 내에서 실행되도록 한다.

- **트랜잭션 커밋:**
    - 메서드가 정상적으로 완료되면 TransactionManager는 트랜잭션을 커밋한다.

- **트랜잭션 롤백:**
    - 메서드 실행 중에 체크드 예외 이외의 예외(런타임 예외, 오류)가 발생하면 TransactionManager는 트랜잭션을 롤백한다.
    - 기본적으로 Checked Exception는 롤백하하지 않는다.
    - Runtime Exception, Error은 롤백을 수행한다.
    - 하지만 개발잦가 명시적으로 지정하여 체크드 예외에 롤백을 발생시킬 수 있다.


<br>

### 트랜잭션 속성
@Transactional 어노테이션은 다양한 속성을 통해 트랜잭션의 동작 방식을 조정할 수 있다.
- propagation: 트랜잭션 전파 방식 (예: REQUIRED, REQUIRES_NEW 등)
- isolation: 트랜잭션 격리 수준 (예: READ_COMMITTED, SERIALIZABLE 등)
- timeout: 트랜잭션이 완료되어야 하는 최대 시간
- readOnly: 읽기 전용 트랜잭션 여부
- rollbackFor: 롤백할 예외 타입
- noRollbackFor: 롤백하지 않을 예외 타입




<br><br><br>


## Proxy
프록시는 실제 객체를 감싸서, 클라이언트와 실제 객체 사이에서 상호작용한다.  
스프링에서 주로 반복되는 부가기능(로깅, 트랜잭션 관리, 보안 등)을 제공하기 위해 사용된다.  

클라이언트는 실제 객체를 직접 호출하지 않고, 프록시 객체를 통해 간접적으로 호출하여, 객체에 대한 접근을 제어하고 부가 기능을 제공한다.


<br><br>


Spring AOP는 프록시 패턴을 기반으로 한다.  
@Transactional은 Spring AOP의 대표적인 예로, Proxy 형태로 동작한다.


스프링에서 프록시 객체를 사용하여 트랜잭션을 관리하는 과정을 보자.
#### 인터페이스 정의
```java
public interface MyService {
    void performTransaction();
}
```
MyService는 트랜잭션을 수행할 메서드를 정의한 인터페이스입니다. 인터페이스를 통해 서비스 구현체의 메서드에 접근합니다.
<br>

#### 서비스 구현
```java
@Service
public class MyServiceImpl implements MyService {
    @Transactional
    public void performTransaction() {
        // 트랜잭션 내에서 수행할 작업
     }
}
```
@Transactional 어노테이션을 사용하여 performTransaction() 메서드를 트랜잭션 처리하도록 한다.  
이 메서드가 호출될 때 스프링이 자동으로 트랜잭션을 시작하고, 메서드가 정상적으로 완료되면 트랜잭션을 커밋하며, 예외 발생 시 롤백한다.  
<br>

#### 프록시 객체 생성 및 사용
```java
   // 프록시 객체 생성 및 사용
MyService myService = (MyService) context.getBean("myService");
myService.performTransaction();
```
context.getBean("myService")는 MyService 타입의 프록시 객체를 반환합니다. 이 프록시는 스프링이 자동으로 생성한 동적 프록시로, 실제 MyServiceImpl의 메서드를 호출하기 전에 트랜잭션 관련 로직을 처리하는 역할을 합니다.
myService.performTransaction()을 호출하면, 프록시 객체가 메서드를 가로채고, 메서드 호출 전에 트랜잭션을 시작하고, 메서드 종료 후 커밋 또는 롤백을 수행합니다
<br>

스프링은 @Transactional이 붙은 메서드를 실행할 때, JDK 동적 프록시나 CGLIB을 사용해 프록시 객체를 생성한다.
이 프록시는 원래 메서드 호출 전에 트랜잭션을 시작하고, 메서드 완료 시 트랜잭션을 커밋하거나, 예외 발생 시 롤백한다.

이 코드에서는 인터페이스를 사용했으므로 JDK 동적 프록시가 사용된다.  
만약 인터페이스가 없다면 CGLIB 프록시가 사용된다.

<br>

> ### 프록시 생성 방식
> - 인터페이스 기반 프록시:
>  스프링은 기본적으로 JDK 동적 프록시를 사용해서 인터페이스를 구현하는 프록시 객체를 생성한다.
>
> - 클래스 기반 프록시 (CGLIB):   
>  만약 빈이 인터페이스를 구현하지 않았다면, 스프링은 CGLIB를 사용하여 클래스 기반 프록시를 생성한다.






<br><br><br>

## 그러면 @Transactional은 어떻게 동작할까?
메서드의 호출이 시작됨면 트랜잭션이 시작되고, 메서드가 정상적으로 종료되면 트랜잭션이 커밋되며,
예외가 발생하면 트랜잭션이 롤백된다.
  
#### 1. 프록시 객체 생성
- 스프링은 @Transactional이 적용된 클래스 또는 메서드를 감싸는 프록시를 생성한다.
- 이 프록시는 실제 비즈니스 로직을 수행하는 객체를 호출하여, 트랜잭션 관련 로직을 처리한다.  

#### 2. 트랜잭션 시작
- 메서드 호출이 시작되면, 프록시는 먼저 트랜잭션 매니저를 통해 새로운 트랜잭션을 시작한다.
- 이 과정에서 트랜잯션 경계가 설정되며, 데이터베이스에 대한 변경 사항이 기록된다.  

#### 3. 비즈니스 로직 실행
- 실제 비즈니스 로직이 담긴 메서드가 호출된다. 이 메서드는 데이터베이스에 대한 읽기, 쓰기 작업을 한다.  

#### 4. 트랜잭션 커밋 또는 롤백
- 메서드가 정상적으로 종료되면, 프록시는 트랜잭션을 커밋한다. 
- 메서드 실행 중 예외가 발생하면, 프록시는 트랜잭션을 롤백한다.  

 ```java
@Service
public class OrderService {
    
    @Autowired
    private OrderRepository orderRepository;

    @Transactional
    public void placeOrder(Order order) {
        // 트랜잭션 시작
        orderRepository.save(order);
        // 추가 로직
        // 트랜잭션 커밋
    }
}
```
위의 OrderService 클래스에서 placeOrder 메서드는 @Transactional 애노테이션을 사용하여 트랜잭션 경계를 정의하고 있다.  
스프링은 이 클래스의 프록시를 생성하여 placeOrder 메서드 호출을 가로채고 트랜잭션을 관리한다.



<br>


### 프록시가 트랜잭션 관리를 대신하는 이유
1. 일관성 유지: 
   - 트랜잭션 관리 로직을 자동으로 처리해 줌으로써 데이터베이스의 일관성과 무결성을 쉽게 유지할 수 있다. 
2. 코드 간결화:
   - 트랜잭션 관리를 코드에서 직접 구현하지 않아도 되므로 코드가 간결해진다.  
3. 중복 방지: 
   - 동일한 트랜잭션 관리 로직을 여러 곳에서 반복해서 작성할 필요가 없다.  
4. AOP 지원: 
   - 프록시는 스프링 AOP를 사용하여 트랜잭션 관리와 같은 횡단 관심사를 쉽게 구현할 수 있게 해준다.  
   

프록시는 트랜잭션 시작, 커밋, 롤백과 같은 작업을 대신 처리하여 개발자가 비즈니스 로직에 집중할 수 있게 하고, 이 과정에서 발생할 수 있는 오류 가능성을 줄여준다.  


<br>

#### 트랜잭션 관리를 Proxy 없이 직접 구현
```java
@Service
public class OrderService {

    @Autowired
    private OrderRepository orderRepository;
    
    @Autowired
    private PlatformTransactionManager transactionManager;

    public void placeOrder(Order order) {
        TransactionStatus status = transactionManager.getTransaction(new DefaultTransactionDefinition());
        try {
            // 트랜잭션 시작
            orderRepository.save(order);
            // 추가 비즈니스 로직 수행
            
            // 트랜잭션 커밋
            transactionManager.commit(status);
        } catch (Exception ex) {
            // 예외 발생 시 트랜잭션 롤백
            transactionManager.rollback(status);
            throw ex;  // 예외 재발생
        }
    }
}
```  

<br>

### 프록시 없이 트랜잭션 관리의 문제점
1. 복잡성 증가: 
   - 트랜잭션을 시작하고, 커밋하거나 롤백하는 코드가 모든 트랜잭션 메서드에 반복적으로 작성되어야 한다.
2. 오류 가능성: 
   - 트랜잭션 시작, 커밋, 롤백 로직을 수동으로 관리하다 보면 실수로 빠뜨리거나 잘못 구현할 수 있다.
3. 비즈니스 로직과 트랜잭션 로직의 혼합: 
   - 비즈니스 로직과 트랜잭션 관리 로직이 같은 메서드에 섞여 있어 가독성이 떨어지고 유지보수가 어렵다.
4. 재사용성 부족: 
   - 트랜잭션 관리 로직이 특정 메서드에 고정되기 때문에 재사용이 어렵다.




<br><br><br>

  
### 프록시를 사용할 때의 장점
1. 코드 간결성: 트랜잭션 관리 로직을 각 메서드에 작성할 필요 없이 @Transactional 애노테이션을 사용하여 트랜잭션 경계를 지정할 수 있다.
2. 오류 감소: 스프링이 트랜잭션 관리를 대신 처리하므로, 개발자가 실수로 트랜잭션 관리 로직을 잘못 구현할 가능성이 줄어든다.
3. 비즈니스 로직 집중: 트랜잭션 관리 로직이 분리되어 비즈니스 로직에 집중할 수 있다.
4. 재사용성 향상: 트랜잭션 관리 로직을 재사용할 수 있고, 코드 중복이 줄어든다.
```java
@Service
public class OrderService {

    @Autowired
    private OrderRepository orderRepository;

    @Transactional
    public void placeOrder(Order order) {
        orderRepository.save(order);
        // 추가 비즈니스 로직 수행
    }
} 
```  

@Transactional 애노테이션을 이용하면 스프링 프레임워크가 프록시를 통해 트랜잭션 관리를 대신해준다.



<br><br><br>

## @Transactional과 프록시의 관계
1. AOP와 프록시 패턴: 
   - 스프링은 AOP를 이용하여 @Transactional 애노테이션을 적용할 때 프록시 패턴을 사용한다.   
   - AOP는 코드의 핵심 기능과 공통된 기능(부가 기능)을 모듈화하여 관리하는 기술이다.

2. 프록시의 역할: 
   - @Transactional 붙은 메서드가 호출될 때, 스프링 프레임워크는 그 메서드를 호출하기 전후에 추가적인 작업을 수행하기 위해 프록시 객체를 생성한다.   
   - 이 프록시 객체는 원본 객체(서비스 클래스)를 감싸며, 트랜잭션 관리와 같은 공통된 기능을 제공한다.

3. 트랜잭션 관리: 
   - @Transactional 붙은 메서드는 트랜잭션 프록시에 의해 감싸져 실행된다 
   - 프록시는 트랜잭션을 시작한 후 메서드를 실행하고, 그 결과에 따라 커밋 또는 롤백한다.
   - 이로 인해 데이터의 일관성을 유지할 수 있다.

4. 간편한 설정: 
   - 개발자는 @Transactional 사용하여 간편하게 트랜잭션 경계를 정의할 수 있다.   
   - 이는 코드를 간결하게 유지하고, 일관된 트랜잭션 처리를 보장한다.
  

```java
@Service
public class OrderService {

    @Autowired
    private OrderRepository orderRepository;

    @Transactional
    public void placeOrder(Order order) {
        // 비즈니스 로직 수행
        orderRepository.save(order);
        // 추가적인 데이터베이스 작업 등
    }

    // 다른 트랜잭션 메서드
    @Transactional
    public void cancelOrder(Order order) {
        order.setStatus(OrderStatus.CANCELED);
        orderRepository.save(order);
        // 추가적인 로직 수행
    }
}
```

<br>
이 예시에서 @Transactional 애노테이션이 붙은 placeOrder()와 cancelOrder() 메서드는 스프링 프록시가 관리하는 트랜잭션 범위에서 실행된다.   
메서드 내의 모든 데이터베이스 연산은 하나의 트랜잭션으로 묶여 실행된다.

따라서 @Transactional을 이용하면 개발자는 트랜잭션 관리에 대한 복잡한 코드를 작성하지 않아도 되며, 스프링 프록시가 이를 대신 처리하여 코드의 간결성과 유지보수성을 높여준다.