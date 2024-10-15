---
layout: post
title: 티켓팅 시스템 - 좌석 선점
categories: [kboticket]
---

기존의 이커머스 시스템은 결제 요청 직전에 재고 확인 후 락을 걸고 결제를 진행하다고 알고 있다.  
최대한 락을 짧게 잡는다.(아닌 경우 알려주세묩..!)   

하자만 티켓 예매의 경우 좌석을 잡고 있는 시간이 존재한다.  
7분 가량 좌석을 잡고 있을 수 있으며, 그 시간 이내에 결제를 완료해야 한다.  
  
두 시스템의 차이는 핵심 비즈니스 로직과 트랜잭션 처리에 대한 우선순위가 달라서 라고 생각한다.


<br>
<br>

## 티켓팅 시스템과 이커머스 시스템  

### 티켓팅 시스템
좌석은 고유한 자원으로, 하나의 좌석에 대해 한 명의 유저만 선택할 수 있어야 한다.    

만약 하나의 자원에 대해 여러 사람이 동시에 선택이 가능하게 되면 중복 예매가 되거나 계속되는 결제 실패로   
유저의 불편함이 증가할 것이다. 

따라서 좌석은 한 번 선점되면 다른 사용자가 선택할 수 없도록 막아야 하기 때문에 긴 락이 필요하다.  
  
선점을 함으로써 사용자 간의 경쟁을 방지하고, 선택된 좌석이 다른 사용자에게 노출되지 않도록 해야 한다.  


<br>

### 이커머스 시스템
이커머스 시스템의 경우, 다수의 사용자가 동일한 상품을 구매할 수 있다.   

이커머스 시스템에서는 재고 수량의 관리가 중요하다.    
따라서, 결제 직전 재고 수량 확인 후 결제를 진행하고, 결제가 성공적으로 마칠 경우 재고를 하나 차감하는 방식이다.  
  
따라서 재고가 있는 경우 락을 걸여 결제를 진행한다.  
티켓 시스템과 같이 상품에 대해 락을 걸고 결제를 진행하는 과정으로 진행한다면,   
다수의 사용자의 구매에 대한 대기시간이 길어질 것이고, 만족도 저하로 이어질 것이다.  

따라서 결제 직전에 재고 확인 후 락을 짧게 걸어 병목 현상을 줄일 수 있다.  
락을 짧게 걸어 여러 사용자가 동시에 구매를 진행하더라도 빠른 처리 속도를 유지하는 것이 시스템의 핵심이다.


티켓팅은 하나의 좌석을 선점하는 것이 중요하고, 이커머스는 재고 수량 관리가 중요하다.  
두 시스템의 목적이 달라서 로직에 차이가 있는 것 같다.  


<br><br><br>

## 구현

### Redisson
다수의 사용자가 동시에 동일한 좌석을 선택하려는 상황에서 데이터 일관성과 경쟁 상태를 방지해야 한다.  
  
여러 서버나 클라이언트가 동시에 좌석에 접근하는 것을 방지하기 위해 분산락을 사용하영ㅆ다.  
이를 통해 중복 선택이 되지않도록 보장한다.   
  
Redis는 메모리 기반 데이터 저장소로 데이터에 접근 속도가 빠르다. 또한 분산락과 TTL 기능을 활용해 기간 설정 충돌을 관리할 수 잇따.  
Redis의 클라이언트 중 Redisson을 사용하였다.
lettuce는 사용자가 직접 스핀락을 구현해야 하며, 락 점유 시도를 실패한 경우 계속해서 락 점유를 시도하게 된다.  
이로 인해 레디스에 부하를 줄 수 있다.
또한, 만료시간을 제공하지 않기 때문에 락을 점유한 프로세스가 비정상적으로 종료되면 락이 해제되지 않아 문제를 일으킬 수 있다.  


Redisson은 TTL을 설정할 수 있어 지정된 시간 동안만 락을 점유하도록 할 수 있다.  
이 기능을 통해 락을 점유한 후 장애가 발생해도 일정 시간 후 자동으로 해제되어 다른 프로세스가 락을 점유할 수 있다.  

이러한 이유로 Redisson을 사용하였다.

<br><br><br>
### 좌석 선점 로직
1. 유저가 기존에 선점한 좌석이 있는지 확인한다.
2. 선택한 좌석의 수가 1개 이상 4개 이하인지 확인한다.
3. 선택한 좌석을 돌며 락을 건다.
    - 다른 유저가 이미 선점한(락이 걸린) 좌석이라면 모든 락이 실패한다.
    - 모든 좌석을 선점해야 성공한다.


<br><br>


어노테이션 기반의 AOP를 이용해 분산락을 관리하는 로직을 별도의 컴포넌트를 구성했다.    
  

#### @DistributedLock 어노테이션
![img3.png](https://github.com/user-attachments/assets/7112b61d-30c4-4c93-9e76-c1c9cb7e827a)
메소드에 분산락을 걸기위한 설정을 제공하는 @DistributedLock 어노테이션으로, 메소드에 적용가능하며, 실행 시점까지 유지가 가능하다.

<br>

#### 트랜잭션 내에서 메소드를 실행하기 위해 AopForTransaction 클래스
![img4.png](https://github.com/user-attachments/assets/c009d44c-ed76-43e8-8a14-c56293c4ae53)
특정 메소드가 실행될 때, 새로운 트랜잭션을 시작하고, 해당 트랜잭션 내에서 메소드를 실행한 후, 결과를 반환한다.  
  
ProceedingJoinPoint는 메소드 호출의 세부 정보를 제공하고, proceed() 메소드를 호출하여 실제 타겟 메소드를 실행한다.

<br>

#### Lock의 이름을 Spring Expression Language로 파싱하기 위한 클래스
![img5.png](https://github.com/user-attachments/assets/15214e52-7383-4dc9-bb1e-f14783d057e3)


<br>

### 메인 로직을 위한 AOP
![img6.png](https://github.com/user-attachments/assets/ac68f79f-643a-4f60-be00-f4ff547c3473)
ProceedingJoinPoint은 현재 실행 중인 메서드 호출에 대한 정보를 제공하, 그 메서드를 실행할 수 있는 인터페이스이다.  

aopForTransaction.proceed(joinPoint) : DistributedLock 어노테이션이 선언된 메서드를 별도의 트랜잭션으로 실행한다.




<br><br><br>



## 문제
위와 같이 구성 후 테스트를 진행하는데 AOP가 동작하지 않았다.  

이유를 찾아보니 스프링의 AOP는 프록시 객체를 통해 동작을 하는데, 같은 클래스 내에서 메서드를 호출 하는 경우 프록시가 적용되지 않아서 발생하는 문제였다.  

이 문제를 해결하기 위해 내부 호출을 다른 클래스에(ReserveInternalService)서 호출하도록 분리하였다.


<br>

#### 기존 [내부 호출]
**ReservationService.selectSeats()** 메서드 ->  **ReservationService.lockSeat()** 메서드 호출. 


#### 변경 후 [클래스 분리]
**ReservationService.selectSeat()** 메서드 **ReserveInternalService.lockSeat()** 메서드 호출.

<br><br>

#### ReservationService
![img2.png](https://github.com/user-attachments/assets/7c963bf2-5d3a-4e3a-8187-c0b0b5824f50)

#### ReserveInternalService
![img7.png](https://github.com/user-attachments/assets/582c4a68-f051-426c-a679-b96d5d31137c)

<br>

### 테스트 코드
![img.png](https://github.com/user-attachments/assets/e18dd190-4df1-42ad-9b8b-fc9124fd2662)



<br>




참고 : https://helloworld.kurly.com/blog/distributed-redisson-lock/

