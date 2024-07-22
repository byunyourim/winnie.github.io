---
layout: post
title: Transactional과 Proxy
categories: Spring
---

좌석을 선택하고 결제하는 api를 구현하는데 트랜잭션에 대하여 이해가 떨어져서 공부하게 되었다.
개념은 익히 들어서 알고 있지만 온전히 내 것으로 만들지는 못했다.  



# 트랜잭션?

일련의 작업들을 하나로 묶어서 처리하는 단위.
트랜잭션이 성공적으로 완료되면 모든 작업이 영구적으로 반영되고, 하나라도 실패하면 모든 작업이 취소됩니다.
데이터의 일관성과 결성을 유지하는 중요한 개념입니다.  


트랜잭션의 개념은 많이 들어서 알고 있었지만 내가 완전하게 이해하기는 어려웠다.  


콘서트 티켓 예매를 예로 들어보자
  

### 상품을 구매하려면 어떻게 해야할까?
1. 상품의 재고를 확인한 후 장바구니에 추가합니다.
2. 상품을 결제합니다.
3. 결제가 성공하면 주문을 생성하고 재고를 업데이트합니다.  



### 이 과정에서 트랜잭션을 사용하지 않는다고 하면 어떤 문제가 발생할까?
재고가 부족한 상황에서 동시에 여러 사용자가 같은 상품을 구매하려고 하면 재고가 음수가 될 수 있습니다.
또한 결제가 성공했지만 주문 생성이 실패하거나 재고 업데이트가 실패하면 데이터의 일관성이 깨질 수 있습니다.
그리고 결제에는 성공했지만 재고가 부족한 상황에서 주문생성에 실패하면, 사용자는 돈을 지불했지만 주문이 완료되지 않을 수 있습니다.



상품 구매 시스템에서 트랜잭션을 사용하면 여러 단계의 구매 과정을 하나의 단위로 묶어서 처리할 수 있습니다.
모든 단계가 성공적으로 완료됙거나, 도중에 실패하는 경우 모든 변경 사항이 롤백되어 데이터의 무결성과 일관성을 보장할 수 있습니다.
스프링에서는 @Transactional 어노테이션을 사용하여 트랜잭션을 관리할 수 있습니다.


### Java
```java
@Service
public class OrderService {

    // 코드 생략
    
    @Transactional
    public void orderProcess(Long productId, int quantity, PaymentInfo paymentInfo) {
        Product product = productRepository.findById(productId).orElseThrow(() -> new RuntimeException("Product not found"));

        // 재고 확인
        if (product.getStock() < quantity) {
            throw new RuntimeException("Not enough stock");
        }

        // 결제 처리
        paymentService.processPayment(paymentInfo);

        // 재고 감소
        product.setStock(product.getStock() - quantity);
        productRepository.save(product);

        // 주문 생성
        Order order = new Order();
        order.setProduct(product);
        order.setQuantity(quantity);
        order.setTotalPrice(product.getPrice() * quantity);
        orderRepository.save(order);
    }
}
```
클래스나 메서드에 붙여 해당 범위 내 메서드가 트랜잭션이 되도록 보장합니다.
직접 객체를 생성할 필요없이 선언만으로 관리가 가능합니다.



### @Transactional은 어떻게 동작할까?
메서드의 실행이 시작될 때 트랜잭션이 시작되고, 메서드가 정상적으로 종료되면 트랜잭션이 커밋되며,
예외가 발생하면 트랜잭션이 롤백됩니다.

1. 스프링은 @Transactional 어노테이션이 적용된 빈을 프록시 객체로 감쌉니다.   
   이 프록시는 실제 빈의 메서드를 호출하기 전에 트랜잭션 관련 로직을 처리합니다.
   
2. 프록시는 메서드 호출 전에 트랜잭션을 시작합니다. 이 과정에서 트랜잭션 매니저가 트랜잭션 경계를 설정합니다.

3. 실제 메서드가 실행됩니다.

4. 메서드가 정상적으로 종료되면 트랜잭션이 커밋되고, 예외가 발생하면 트랜잭션이 롤백됩니다.
   
 
  
그러면 프록시란 무엇일까?
  


## Proxy
프록시는 다른 객체의 대리자로서 동작하는 객체로 실제 객체를 감싸서 부가 기능을 제공합니다. 스프링은 주로 AOP를 통해 트랜잭션 관리를 위해 프록시를 사용합니다.
  
  

### 프록시의 역할
프록시는 실제 객체의 호출 전후에 부가 기능을 추가할 수 있습니다. 예를 들어, 트랜잭션 시작과 종료, 예외 발생 시 롤백 등을 처리합니다.

```java
    public interface MyService {
        void performTransaction();
    }

    @Service
    public class MyServiceImpl implements MyService {
        @Transactional
        public void performTransaction() {
            // 트랜잭션 내에서 수행할 작업
         }
    }

       // 프록시 객체 생성 및 사용
    MyService myService = (MyService) context.getBean("myService");
    myService.performTransaction();
```
프록시 객체는 실제 MyServiceImpl 객체를 감싸서 트랜잭션 관리를 추가합니다. 클라이언트는 프록시 객체를 통해 트랜잭션이 적용된 메서드를 호출합니다.
  
  

###프록시 생성 방식
- 인터페이스 기반 프록시: 기본적으로 스프링은 JDK 동적 프록시를 사용하여 인터페이스를 구현하는 프록시 객체를 생성합니다.

- 클래스 기반 프록시 (CGLIB): 만약 빈이 인터페이스를 구현하지 않았다면, 스프링은 CGLIB를 사용하여 클래스 기반 프록시를 생성합니다.



### 트랜잭션 경계 설정
- 트랜잭션 시작: 프록시는 메서드 실행 전에 PlatformTransactionManager를 사용하여 트랜잭션을 시작합니다.
- 트랜잭션 커밋: 메서드가 정상적으로 완료되면 TransactionManager는 트랜잭션을 커밋합니다.
- 트랜잭션 롤백: 메서드 실행 중에 체크드 예외 이외의 예외(런타임 예외, 오류)가 발생하면 트랜잭션이 롤백됩니다.



### 트랜잭션 속성
@Transactional 어노테이션은 다양한 속성을 통해 트랜잭션의 동작 방식을 조정할 수 있습니다.
- propagation: 트랜잭션 전파 방식 (예: REQUIRED, REQUIRES_NEW 등)
- isolation: 트랜잭션 격리 수준 (예: READ_COMMITTED, SERIALIZABLE 등)
- timeout: 트랜잭션이 완료되어야 하는 최대 시간
- readOnly: 읽기 전용 트랜잭션 여부
- rollbackFor: 롤백할 예외 타입
- noRollbackFor: 롤백하지 않을 예외 타입




프록시는 @Transactional이 적용된 메서드 호출을 가로채서 트랜잭션 관련 처리를 수행합니다. 이를 통해 개발자는 트랜잭션 관리 로직을 직접 작성하지 않아도 됩니다.
프록시는 다음과 같은 방식으로 트랜잭션을 처리합니다.

1. 메서드 호출 가로채기: 클라이언트가 @Transactional이 적용된 메서드를 호출할 때, 프록시가 이 호출을 가로챕니다.
2. 트랜잭션 시작: 프록시는 PlatformTransactionManager를 사용하여 트랜잭션을 시작합니다.
3. 메서드 실행: 실제 메서드를 실행합니다.
4. 트랜잭션 커밋/롤백: 메서드 실행이 성공하면 트랜잭션을 커밋하고, 예외가 발생하면 트랜잭션을 롤백합니다.
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

위의 OrderService 클래스에서 placeOrder 메서드는 @Transactional 애노테이션을 사용하여 트랜잭션 경계를 정의하고 있습니다.  
스프링은 이 클래스의 프록시를 생성하여 placeOrder 메서드 호출을 가로채고 트랜잭션을 관리합니다.

### 프록시가 트랜잭션을 처리하는 과정
1. 프록시 생성: 스프링이 애플리케이션 컨텍스트를 초기화할 때, OrderService 빈에 대한 프록시를 생성합니다.
2. 메서드 호출 가로채기: 클라이언트가 placeOrder 메서드를 호출하면 프록시가 이 호출을 가로챕니다.
3. 트랜잭션 시작: 프록시는 TransactionManager를 사용하여 트랜잭션을 시작합니다.
4. 실제 메서드 실행: 프록시는 실제 placeOrder 메서드를 호출합니다.
5. 트랜잭션 커밋/롤백: 메서드가 정상적으로 종료되면 프록시는 트랜잭션을 커밋하고, 예외가 발생하면 트랜잭션을 롤백합니다.
  
  

### 트랜잭션이 하는 일을 프록시가 대신하는 이유
1. 일관성 유지: 트랜잭션은 데이터베이스의 일관성과 무결성을 유지하는 데 중요한 역할을 합니다. 프록시를 사용하면 이러한 트랜잭션 관리 로직을 쉽게 적용할 수 있습니다.
2. 코드 간결화: 트랜잭션 관리를 코드에서 직접 구현하지 않아도 되므로 코드가 간결해집니다.
3. 중복 방지: 동일한 트랜잭션 관리 로직을 여러 곳에서 반복해서 작성할 필요가 없습니다.
4. AOP 지원: 프록시는 스프링 AOP(Aspect-Oriented Programming)를 사용하여 트랜잭션 관리와 같은 횡단 관심사를 쉽게 구현할 수 있게 해줍니다.  

프록시는 트랜잭션을 투명하게 처리하여 개발자가 비즈니스 로직에만 집중할 수 있게 해줍니다.
  
  
  

프록시 없이 트랜잭션을 관리하려면, 트랜잭션 시작, 커밋, 롤백을 수동으로 처리해야 합니다. 이것은 상당히 번거롭고 오류가 발생하기 쉬운 작업입니다.

#### 트랜잭션 관리 직접 구현
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

### 프록시 없이 트랜잭션 관리의 문제점
1. 복잡성 증가: 트랜잭션을 시작하고, 커밋하거나 롤백하는 코드가 모든 트랜잭션 메서드에 반복적으로 작성되어야 합니다.
2. 오류 가능성: 트랜잭션 시작, 커밋, 롤백 로직을 수동으로 관리하다 보면 실수로 빠뜨리거나 잘못 구현할 가능성이 높습니다.
3. 비즈니스 로직과 트랜잭션 로직의 혼합: 비즈니스 로직과 트랜잭션 관리 로직이 같은 메서드에 섞여 있어 코드가 지저분해지고 유지보수가 어려워집니다.
4. 재사용성 부족: 트랜잭션 관리 로직을 재사용하기 어렵고, 모든 트랜잭션이 필요한 메서드에서 중복 코드가 발생합니다.


  
  
### 프록시를 사용할 때의 장점
1. 코드 간결성: 트랜잭션 관리 로직을 각 메서드에 작성할 필요 없이 @Transactional 애노테이션을 사용하여 트랜잭션 경계를 지정할 수 있습니다.
2. 오류 감소: 스프링이 트랜잭션 관리를 대신 처리하므로, 개발자가 실수로 트랜잭션 관리 로직을 잘못 구현할 가능성이 줄어듭니다.
3. 비즈니스 로직 집중: 트랜잭션 관리 로직이 분리되어 비즈니스 로직에 집중할 수 있습니다.
4. 재사용성 향상: 트랜잭션 관리 로직을 재사용할 수 있고, 코드 중복이 줄어듭니다.
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

스프링이 프록시를 통해 트랜잭션 시작, 커밋, 롤백을 자동으로 처리하므로 개발자는 비즈니스 로직에만 집중할 수 있습니다.




@Transactional 애노테이션을 이용하면 스프링 프레임워크가 프록시를 통해 트랜잭션 관리를 대신해 줍니다. 
이를 이해하기 위해서는 스프링의 AOP (Aspect-Oriented Programming) 개념과 프록시 패턴을 이해하는 것이 중요합니다.

### @Transactional과 프록시의 관계
1. AOP와 프록시 패턴: 스프링은 AOP를 이용하여 @Transactional 애노테이션을 적용할 때 프록시 패턴을 사용합니다.   
   AOP는 관점 지향 프로그래밍을 의미하며, 코드의 특정 관점에서의 공통된 기능을 분리하여 관리하는 기술입니다.

2. 프록시의 역할: @Transactional 애노테이션이 붙은 메서드가 호출될 때, 스프링 프레임워크는 그 메서드를 호출하기 전후에 추가적인 작업을 수행하기 위해 프록시 객체를 생성합니다.   
   이 프록시 객체는 원본 객체(서비스 클래스)를 감싸며, 트랜잭션 관리와 같은 공통된 기능을 제공합니다.

3. 트랜잭션 관리: @Transactional 애노테이션이 붙은 메서드에서는 트랜잭션을 시작하고, 메서드 실행을 감싸는 프록시가 트랜잭션을 커밋 또는 롤백합니다.   
   이 과정에서 데이터베이스 연산의 성공 또는 실패에 따라 트랜잭션을 적절하게 처리합니다.

4. 간편한 설정: 개발자는 @Transactional 애노테이션을 사용하여 간편하게 트랜잭션 경계를 정의할 수 있습니다.   
   이는 개발자가 직접 트랜잭션을 시작하고, 커밋 또는 롤백하는 코드를 작성하지 않아도 되게 만듭니다.
  
  

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

이 예시에서 @Transactional 애노테이션이 붙은 placeOrder()와 cancelOrder() 메서드는 스프링 프록시가 관리하는 트랜잭션 범위에서 실행됩니다.   
메서드 내의 모든 데이터베이스 연산은 하나의 트랜잭션으로 묶이며, 이 트랜잭션은 메서드 실행 완료 후 자동으로 커밋 또는 롤백됩니다.

따라서 @Transactional을 이용하면 개발자는 트랜잭션 관리에 대한 복잡한 코드를 작성하지 않아도 되며, 스프링 프록시가 이를 대신 처리하여 코드의 간결성과 유지보수성을 높여줍니다.