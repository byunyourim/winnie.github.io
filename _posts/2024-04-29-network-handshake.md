---
layout: post
title: TCP 3-way handshake, 4-way handshake
categories: Network
---

# 3-way handshake ?

![3wayhandshake.png](https://github.com/user-attachments/assets/9eaed275-cfb8-4736-b373-1d43cf98089c)

TCP 통신을 이용해서 데이터를 전송하기 위해 네트워크 연결 설정을 하는 과정  

즉, (TCP/IP 프로토콜을 이용해서) 통신을 하는 응용 프로그램이
데이터를 전송하기 전에 정확한 전송을 보장하기 위해 상대방 컴퓨터와의 세션을 수립하는 과정입니다.


> ### FLAG 정보
> - **SYN**  
  연결 설정
> - **ACK**  
  응답 확인, 패킷을 받았다는 것을 의미
  Acknowledgement Number
> - **FIN**  
  연결 해제, 세션 종료, 더 이상 전송할 데이터 없음  
  
  
  

## STEP
### [Step1] Client -SYN-> Server
- 나 너한테 접속할건데 괜찮니?
- SYN : Client가 Server에게 접속을 요청


### [Step2] Client <-SYN + ACK- Server
- 웅 괜찮아!
- 서버가 클라이언트에 SYN + ACK 전송 (LISTEN -> SYN_RECV)
- 그 후 Server는 다시 대기 상태


### [Step3] Client -ACK-> Server
- 오케이 -> 연결
- SYN + ACK 상태를 확인 후
- Client는 서버에게 ACK 전송
- 연결 성립 (Established)







<br>







# 4-way handshake ?
세션 연결을 종료하기 위한 과정  

![4wayhandshake](https://github.com/user-attachments/assets/e0d598a1-280c-4fbd-8948-c7f89dd5056e)

연결 종료는 양쪽 호스트 모두 먼저 시도할 수 있으며, 연결을 먼저 요청하는 Host가 클라이언트,
연결을 요청받는 Host 는 서버이다. 

실제로 TCP는 양방향 통신이기에 ‘**클라이언트-클라이언트**’의 형태



> ### Control bits
> - **ACK(Acknowledgement)**  
> 패킷을 받았다는 응답을 할 때 사용   
> Acknowledgement Number가 유효한지 표시   
> 최초 연결의 첫 번째 세그먼트를 제외한 모든 Segment의 ACK 비트는 1로 설정   
> 최초 연결의 첫 번째 Handshake 과정에서는 응답할 요청이 없음   
>
> - **FIN(Finish)**  
> TCP 연결을 종료할 때 사용   
> 더 이상 전송할 데이터가 없음을 의미  
>
> - **RST(Reset)**  
> TCP 연결을 강제로 종료할 때 사용  
> 비 정상적인 세션 연결 끊기   
> 연결을 즉시 끊고자 할 때 사용



> ### Port 상태 정보
> - **ESTAB**  
>  포트가 연결된 상태
> - **CLOSE-WAIT**  
>  상대방의 FIN(종료 요청)을 받은 상태   
> 상대방 FIN에 대한 ACK를 보내고 애플리케이션에 종료를 알린다
> - **LAST-ACK**  
> CLOSE-WAIT 상태를 처리 후 자신의 FIN요청을 보낸 후 FIN에 대한 ACK를 기다리는 상태
> - **FIN-WAIT-1**  
> 자신이 보낸 FIN에 대한 ACK를 기다리거나 상대방의 FIN을 기다린다.
> - **FIN-WAIT-2**  
> 자신이 보낸 FIN에 대한 ACK를 받았고 상대방의 FIN을 기다린다.
> - **CLOSING**  
> 상대방의 FIN에 ACK를 보냈지만 자신의 FIN에 대한 ACK를 못받은 상태
> - **TIME-WAIT**  
> 모든 FIN에 대한 ACK를 받고 연결 종료가 완료된 상태   
> 새 연결과 겹치지 않도록 일정 시간 동안 기다린 후 CLOSED로 전이한다.
> - **CLOSED**  
> 연결 수립을 시작하기 전의 기본 상태 (연결 없음)
  
  

## STEP
### [STEP1] 클라이언트 -FIN-> 서버
- 연결종료 가능하니?
- 클라이언트가 서버에 연결 종료를 요청하는 FIN 세그먼트를 보낸다
  FIN 비트 : 1
- 이때 FIN 패킷에는 실질적으로 ACK도 포함
- 클라이언트는 전송
- FIN-WAIT-1

### [STEP2] 클라이언트 <-ACK- 서버
- 우웅! 잠만! 확인해볼겡!
- 서버는 FIN을 받고 확인했다는 ACK를 클라이언트에 보낸 후
  자신의 통신이 끝날때까지 기다림
- 남은 데이터가 있는 경우 전송을 마친 후 close() 호출
- 클라이언트는 ACK를 받은 후에 서버가 남은 데이터 처리를 끝내고
  FIN 패킷을 보낼 때까지 기다린다.
- FIN_WAIT_2

### [STEP3] 클라이언트 <-FIN- 서버
- 종료하장!
- 서버가 데이터를 모두 보냈다면 연결 종료에 합의한다는 의미로
  FIN 패킷을 클라이언트에 전송
- 이후 ACK 를 받을때까지 기다림
- LAST_ACK

### [STEP4] 클라이언트 -ACK-> 서버
- 우웅! 아직 안 온 거 있을 수 있으니까 좀만 기다렸다가 종료해야징
- 클라이언트는 FIN 을 받고, 응답 ACK 를 서버에 전송
- 아직 서버로 부터 받지 못한 데이터가 있을 수 있으므로
  TIME_WAIT 를 통해 기다린다

  * TIME_WAIT : 의도치 않은 에러로 인해 데드락으로 빠지는 것을 방지

### [AFTER]
- 서버는 ACK 를 받은 이후 소켓 Closed
- TIME_WAIT 시간이 끝나면 클라이언트도 소켓을 Closed



<br>



## 3-way-handshake와 차이가 나는 이유?
클라이언트가 데이터를 전송을 마쳤다고 하더라도 **서버는 아직 보낼 데이터가 남아있을 수 있기 때문에** FIN 에 대한 ACK 만 보내고 데이터를 모두 전송한 후에 자신도 FIN 메시지를 보내기 때문!

서버는 마지막 FIN 을 보내기 전 아직 전송하지 못한 데이터를 전송할 수 있다

그러나 데이터가 유실되어 재전송하거나 지연되어 FIN 보다 늦게 도착할 수 잇다

따라서 클라이언트는 **FIN을 받고도 TIME_WAIT 를 통해 혹시 모를 패킷 수신을 기다린다**



<br>



## Abrupt Connection Release(갑작스런 연결 해제)

RST(TCP reset) 세그먼트가 전송되면 갑작스러운 연결 해제가 수행된다.

- ACK를 보내거나 기다리는 작업이 필요하지 않고, **바로 연결이 종료**된다.
- RST 비트를 1로 설정한 세그먼트를 전송한다.
  - 송신자는 패킷을 보내고 바로 연결을 종료한다.
  - 수신자는 패킷을 받으면 바로 연결을 종료한다.


#### RST를 사용해 연결을 종료하는 경우
1. 보안 위반의 경우
- 악성 코드가 존재한다고 판단되면 연결을 즉시 종료하여 보호

2. 자원이 부족해 자원 할당을 해제해야하는 경우

3. TCP 연결에 장애가 발생한 경우
- 즉시 연결을 끊고 새로운 연결을 시도


