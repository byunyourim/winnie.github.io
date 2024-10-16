---
layout: post
title: 티켓팅 시스템 - 대기열 구현
categories: [kboticket]
---


이 시스템은 트래픽이 몰리는 경우를 가정한다.  



## 구현

- (수천 명의 동시 요청) 대용량 트래픽이 몰리는 경우
- 순번 조회 가능


SQS, RabbitMQ, Kafka와 같은 큐 서비스의 장단점 확인 후 적합성 판단.

<br>

### SQS
#### 장점
- AWS에서 관리되는 서비스로 운영 부담이 적다.
- 높은 가용성과 내구성을 보장하며, AWS 생태계와 통합이 용이하다.
#### 단점
- 메시지 큐잉에 최적화되어 있어 실시간 데이터 스트리밍에 부적합하다.
- 초당 수백만 개의 메시지 처리에느 한계가 있을 수 있다.  

#### 적합성
- SQS는 간단한 메시지 큐잉에 적합하지만, 대규모 트래픽을 처리하기에는 한계가 있다.  



<br>

### RabbitMQ
#### 장점
- 다양한 라우팅 옵션과 기능을 제공한다.(우선순위, TTL)
- 클러스터링을 통한 고가용성 지원
#### 단점
- 매우 큰 규모의 메시징 시스템에서 성능이 떨어질 수 있다.  
- 설정 및 운영이 복잡할 수 있다.  


#### 정합성
- 초당 수백만 개의 요청 처리에는 제한이 있다.  


<br>

### Kafka
#### 장점
- 초당 수백만 개의 메시지를 처리할 수 있는 높은 처리량. 
- 내구성을 보장하고 장애 발생 시 데이터 손실 최소화한다.
- 클러스터 확장이 용이하여 트래픽 급증에 유연하게 대응이 가능하다.

#### 단점
- 설정이 다소 복잡할 수 있으며, 관리가 필요하다.


<br>

대량의 트래픽 처리에 최적화되어 있고, 장애 발생 시에도 데이터 손실을 최소화할 수 있어, 수천 명이 동시에 몰리는 티켓 예매 시스템에 적합하다고 판단.