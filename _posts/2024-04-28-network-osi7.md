---
layout: post
title: OSI 7 계층과 주소창에 naver.com을 치면 일어나는 일
categories: [Network]
---

# OSI 7 계층
![osi7.png](https://github.com/user-attachments/assets/3473b88d-3e81-4dad-b1ce-6cf225d725fc)

1. 물리 계층
   - 물리적인 연결을 담당하며, 물리적 매체를 통해 데이터를 전송한다.
   - 이더넷 케이블, 광섬유, 전송 매체의 전기적/ 광학적 특성

2. 데이터 링크 계층
   - 데이터의 오류를 감지하고 수정하며, 물리 계층을 통해 전송되는 데이터를 프레임으로 변환하여 전달한다. 
     네트워크 내에서 노드간의 데이터 전송을 담당한다.
   - MAC 주소, 스위치, 이더넷

3. 네트워크 계층
   - 라우터를 이용하여 최적의 경로를 설정하여 데이터를 전달하고 라우팅하는 과정을 수행한다.
   - 트래픽 관리 및 패킷 분할을 수행한다.
   - IP 주소, 라우터, IP 프로토콜

4. 전송 계층
   - 신뢰성 있는 데이터의 전송을 보장한다.
   - 데이터 전송을 수행한다.
   - TCP, UDP

5. 세션 계층
   - 세션을 연결하고 유지한다.
   - 애플리케이션 간의 통신 세션을 관리하고, 데이터 교환의 동기화를 제공한다.

6. 표현 계층
   - 데이터의 표현 방식을 관리한다.
   - 암호화 알고리즘등을 암호화 및 압축을 수행한다. 
   - 데이터 압축, 암호화, 인코딩

7. 응용 계층
   - 사용자와 애플리케이션 간의 인터페이스를 제공한다.
   - 웹 브라우저, FTP
   
<br>

# 주소창에 naver.com을 치면 일어나는 일
## 1. URL 입력 및 DNS 조회 
   사용자가 웹 브라우저에 naver.com을 입력한다.  
      
   브라우저는 먼저 DNS 서버에 도메인 네임(naver.com)의 IP 주소를 요청한다.
   이 과정을 통해 도메인 네임을 IP 주소로 변환한다.

   > #### DNS
   > 도메인 네임에 매핑되는 IP 주소를 알려준다.



<br><br>



## 2. 데이터 링크 계층
   DNS 쿼리로 받은 IP 주소를 가지고 개인의 컴퓨터를 빠져나와 스위치에 패킷을 전송한다. 
   
   스위치에서 ARP 테이블을 통해서 IP 주소에 해당하는 MAC 주소를 찾는다.  
   
   해당 네트워크 내에 있는 장치 중 이 MAC 주소를 가진 장치가 있는지 확인한다.
   ARP 요청을 브로드캐스트하여 MAC 주소를 알아내고, 그 정보를 ARP 테이블에 저장한다.

> ##### ARP 테이블과 동작 원리
> - ARP 테이블은 각 네트워크 장치가 IP 주소와 MAC 주소의 매핑 정보를 저장하는 테이블이다.  
> - 각 장치는 네트워크 내에서 통신할 때, 테이블을 통해 IP에 대응하는 MAC주소를 확인할 수 있다.  
> 
> **[ARP 요청]**  
> 장치 A는 장치 B의 MAC 주소를 알아내기 위해 네트워크 전체에 브로드캐스트로 ARP 요청을 전송한다.    
> "해당 IP 주소를 가진 장치의 MAC 주소를 알려줘~"    
>   
> 네트워크 내의 모든 장치가 브로드캐스트를 수신한다.    
> **[ARP 응답]**  
> 해당 IP 주소를 가진 장치 B는 자신의 MAC 주소를 ARP응답 패킷에 담아 장치 A에 유니캐스트로 응답한다.  
> 장치 A는 B에게 받은 MAC주소를 자신의 ARP 테이블에 저장한다.  
> 향후 다시 B에게 패킷을 전송할 때 ARP테이블에 저장된 MAC주소를 이용한다.

<br>

> #### 브로드캐스트 과정
> 컴퓨터를 켜면 스위치에 신호가 들어간다  
>  나 : 스위치야 ~ 나 켜졌으니까 IP 주소를 주겠니?  
>  스위치 : 자 ~ 이거 너 IP야~~  
> 이런식으로 스위치가 IP를 알려준다
>
> -> 그 후, 스위치는 다른 장치들에 
> "이런 IP 주소에 이런 MAC 주소를 갖는 아이가 들어왔어~~"
> 라며 브로드캐스트를 한다
>
> 그렇게되면 이제 스위치에 연결된 모든 컴퓨터와 네트워크는 ARP 테이블에 해당 정보를 하나 추가한다.


<br>


> #### 랜? 이더넷? arp?토큰링
> 만약 스위치에 1000대의 컴퓨터가 연결되어있다면!!  
1000대의 컴퓨터가 켜졌다/꺼졌다 할 텐데 그럴때마다 모든 컴퓨터에게
브로드캐스트를 날려서 ARP테이블을 업데이트 시켜야한다.
>
> 그렇게 되면 그 1000대의 컴퓨터는 불필요하나 작업을 계속하게 된다.
>
> 그러면 부담이 생기기 때문에 브로드캐스트가 되는 범위를 좁혀줘야한다. 그리고 이 범위를 구분하기 위한 것이 넷 마스크이다.
>
> 즉, 넷 마스크는 네트워크를 구분하기 위한 것이다.
>
> OSI 관점에서 IP와 넷마스크 주소가 동일한 애들은 같은 네트워크로 본다 (AND 연산)
> 같은 네트워크에 있는 컴퓨터는 브로드캐스트와 arp를 공유하게 되고 네트워크가 다르면
영향을 받지 않는다


<br><br>

![router.png](https://github.com/user-attachments/assets/b48a0a42-7f85-4d88-94be-a67afb059cea)

> #### 스위치
> - **네트워크 내에서 여러 장치 간에** 데이터를 전송하는 장치
> - 주로 로컬 네트워크(LAN)에서 사용된다. 
> - MAC 주소를 사용하여 패킷을 네트워크 내에서 전송한다.
> - 여러 포트를 가지고 있으며, 각 포트는 개별적인 네트워크 장치에 연결된다.
> - 포트 간에 데이터를 전송, 관리하며 네트워크 트래픽을 효율적으로 관리한다.
> - 패킷이 동일 네트워크 내의 장치가 아닌 경우, 패킷 공유기(라우터)로 전달한다.
> - 이때, 패킷의 목적지로 가는 최적의 경로는 공유기(라우터)가 처리한다.
> - 2~4 계층 까지 처리할 수 있는 장비이지만 주로 2계층 처리한다.
  
<br><br>

>### 스위치와 공유기로 보는 데이터 이동 과정
> 네트워크 구성에서는 스위치와 공유기가 함께 사용된다.
> 
> ##### 1. 내부 네트워크 (LAN):
   네트워크 내에 연결된 장치(컴퓨터, 서버, 프린터)는 모두 스위치에 연결된다.
   내부 네트워크에서 통신하는 경우 스위치를 통해 데이터가 전달된다 (집 : 나 <-> 아빠  
   ex) 컴퓨터 A에서 컴퓨터 B로 데이터를 보낼 때,
   스위치는 컴퓨터 B의 MAC 주소를 찾아 해당 포트로 데이터를 전송한다.
> 
> ##### 2. 라우터(공유기)를 통한 외부 네트워크 (인터넷) 연결:
>  공유기는 내부 네트워크와 외부 네트워크(인터넷)를 연결하는 역할을 한다   

>  외부 네트워크와의 통신을 관리하고 내부 네트워크의 장치들이 인터넷에 접속할 수 있도록 한다.
>  내부 네트워크에 연결된 모든 장치는 공유기에 연결되어 있으며,
>  데이터가 외부 네트워크로 전송될 때는 공유기를 통해 전송된다  
> 
> 따라서 데이터가 내부 네트워크에서 스위치를 통해 이동한 후, 외부 네트워크로 전송될 때는 공유기를 통해 이동한다.



<br>


> #### IP 주소와 MAC 주소
> ##### ◾️ IP 주소?
   IP 주소는 LAN 카드에 연결되어 있는 회선(랜선)의 주소로, 고정되지 않고, 인터넷 망에 접속할 때마다 달라진다

> 라우팅 기법은 패킷에 포함된 IP주소를 추적하여 최단 경로를 선택한다.  
> IP 주소는 논리적인 주소로, 라우터들은 이 IP 주소를 추적해 목표 네트워크까지 패킷을 전달한다.  
> 목표 네트워크에 도착한 패킷은 IP 주소를 기반으로 해당 장치의 MAC 주소로 변환되며,  
> 그 MAC 주소를 사용하여 최종적으로 해당 컴퓨터로 패킷이 전송된다.
>   
> ##### ◾️ MAC 주소는?
   **네트워크 상에서** 서로를 구분하기 위해 장치마다 할당된 물리적인 주소로,  
   인터넷이 가능한 장비들이 가지고 있는 고정된 값이다.  
>  
>  집에서 naver.com의 웹 서버를 찾는다고 가정했을때,   
>  라우터는 IP 주소를 사용해 목적지 서버까지의 최적의 경로를 찾아서 전달한다.  
>  반면, MAC 주소는 같은 네트워크 내에서 사용되며,IP주소와 MAC 주소 간 변환은 각 네트워크의 마지막 단계에서 이루어진다.
   
> **IP는 최적의 경로를 찾아주는 라우팅을 하기 위한 주소이고, 실질적인 통신은 MAC 주소로 한다!**


<br><br><br>


## 3-1. 해당 네트워크에 MAC 주소가 있는 경우
MAC 주소를 찾은 후 스위치는 해당 MAC 주소를 갖는 컴퓨터로 패킷을 전달한다.   
이 과정에서 데이터 링크 계층의 프레임을 사용한다.


<br>



## 3-2. 해당 네트워크에 MAC 주소가 없는 경우 -> 라우터를 통한 네트워크 계층 
네트워크 내에 MAC 주소가 없으면 스위치에서 라우터(공유기)를 통해 다른 네트워크로 이동한다.  
  
네트워크 계층에서는 네트워크 간의 통신이 일어난다.  
  
naver.com의 IP 주소를 확인하고, 이를 바탕으로 라우팅 테이블을 참조하여 다음 라우터를 결정한다.

출발 장치는 다음 라우터로 네이버의 IP 주소가 포함된 패킷을 전송한다.  
패킷이 출발 장치에서 생성될 때, 출발 장치의 네트워크 카드는 자신의 MAC 주소를 패킷의 출발지 주소로 설정하고,   
패킷의 목적지 주소는 해당 패킷을 처리할 라우터의 MAC 주소로 설정됩니다

라우터를 거치면서 각 라우터는 서로 정보를 공유하고, 라우팅 테이블을 업데이트하여 최적의 경로를 제공한다.  

<br>

#### 출발지 장치의 네트워크 카드를 식별하는 이유
- 네트워크에서 효율적인 통신을 가능하게 하기 위함

1. 패킷 라우팅:   
   네트워크에서 패킷을 보낼 때, 출발지 장치의 네트워크 카드는 **패킷의 출발지를 식별**하는데 사용된다. 
   이를 통해 목적지로 패킷을 전송할 때 어디에서 왔는지 알 수 있다.

2. 네트워크 보안:   
   네트워크 보안에서 출발지 장치의 네트워크 카드는 **인증 및 접근 제어**에 사용된다.
   네트워크에 접근하려는 디바이스가 실제로 인가된 디바이스인지 확인하기 위해 네트워크 카드의 고유 식별 정보가 사용된다.

3. 네트워크 장애 해결:   
   네트워크에서 문제가 발생할 때, 네트워크 카드의 식별 정보를 사용하여 문제를 해결할 수 있다.
   각 장치의 네트워크 카드는 고유한 식별자를 가지고 있어, 문제가 발생한 장치를 식별하고 추적하는 데 사용될 수 있다.

<br>

> #### 라우터?
> - 네트워크 계층에서 동작하며, 패킷을 **서로 다른 네트워크 간에** 데이터 패킷을 전달하는 장치
> - 라우팅 과정을 통해 목적지 네트워크에 도착하면, 해당 네트워크의 스위치가 패킷을 처리합니다.
> - 이 스위치는 다시 ARP 요청을 통해 최종 목적지 MAC 주소를 찾고, 패킷을 최종 장치로 전달합니다.
> - 공유기는 방화벽, DHCP 서버, 포트 포워딩 등의 기능을 제공
  
  
> #### ISP (Internet Service Provider)
> LAN은 집 안 사람들 간에 정보는 전달할 수 있짐만,
  외부의 웹사아트(네이버)에 접속하거나, 멀리 있는 사람과 연락하는 것은 불가능하다.   
> 이때, 필요한 것이 ISP이다. 
>   
> 여기에 우리가 잘 알고있는 SK, KT, U+ 가 있다.  
>  
> 이 회사 들이 우리나라 전역에 인터넷 케이블을 설치하였다.   
> 우리는 돈을 지불하고 이 케이블을 사용한다. 
>   
> 이렇게 ISP가 제공하는 보다 광범위한 네트워크를 WAN이라고 한다.    
> 이렇게 집 안 컴퓨터들은 공유기나 ISP가 제공하는 장비를 통해 WAN에 연결된다.     
> WAN은 여러 LAN(집, 회사, 건물)으로 구성된 네트워크를 서로 연결해준다.



<br><br><br>



## 4. 패킷 전달
패킷은 이러한 과정을 반복하여 네이버 서버에 도착한다.



<br><br><br>




## 5. 전송 계층
네이버 서버가 패킷을 수신한 후, TCP, UDP 프로토콜을 이용하여 클라이언트와 서버 간 연결을 설정한다.  
연결이 정상적으로 이루어지면, 서버는 소켓을 생성하고 열어 데이터를 주고 받을 준비를 한다.  

##### peer 2 peer ( host to host )
 택배 배송 과정으로 비유하면, 그 전까지의 과정은 택배기사가 문 앞까지 배송해 준 과정입니다.  
 반면, 전송계층(4)은 내가 택배의 주인인 집 구성원에게 직접 가져다 주는 과정을 의미한다.  


<br>


#### TCP, UDP의 공통점인 포트 번호의 필요성
> - 클라이언트 프로그램이 네트워크 상의 특정 프로그램을 지정할 때 사용한다.
> - 서버에 접속할 때, 어느 서비스에 연결할지 포트번호를 통해 지정한다.
> - 각 포트 번호는 특정 프로그램이나 서비스에 할당되어 있어, 어떤 프로그램이 해당 서비스를 담당하는지 알 수 있도록 한다.
> - 만약 우리 컴퓨터에 여러 개의 프로그램(브라우저, 워드, 파워포인트)이 동시에 실행되고 있는 경우, 모두가 네트워크를 필요로 할 때,
    어떤 프로그램이 요청했는지 알 수 없다.
> - 따라서, 각 프로그램이 요청한 패킷을 올바른 서비스로 전달하기 위해 포트 번호가 필요하다.   
> - https : 443, http : 80


<br>

#### 소켓?
- 데이터 통신을 위한 읽기 및 쓰기 작업을 수행할 수 있는 하나의 통로이다.
- 4 계층에서 처리된 데이터를 5 계층으로 전달한다.



<br><br><br>




## 6. 세션 계층   
5계층에서 세션이 설정되면 실제 데이터 교환이 이루어진다.  
세션은 클라이언트와 서버 간의 연결을 유지하기 위한 상태 관리 기능을 제공한다.   
데이터의 흐름을 관리하며, 연결된 상태를 유지하여 안정적인 통신을 가능하게 한다.

  
<br>

> #### 세션
> **키워드 : 상태유지**
> - 사용자의 로그인 상태 및 서비스 이용 정보를 유지하기 위해 사용된다.
> - naver.com 에 접속을 한 이후에 재접속할 때, 서버는 사용자의 정보를 저장하고 세션ID를 발급한다.
> - 사용자가 이 session Id를 가지고 다시 접속하면, 서버는 해당 ID를 통해 사용자를 식별할 수 있다.  
> - 즉, 세션은 사용자가 로그인하거나 서비스를 이용하는 동안 상태를 지속적으로 유지하는 것을 의미한다.  
  



<br><br><br>




## 7. 표현 계층   
6계층에서 데이터가 네이버 서버에서 클라이언트로 전달되기 전,  
표현 계층은 필요한 인코딩, 암호화 등을 처리한다.

실제 서비스를 사용하는 것과 유사하게,   
표현 계층은 데이터를 포장해서 물건을 확인하는 과정에 비유할 수 있다.  

우리가 살 수 있는 다양한 물건이 존재하는 것처럼, 7계층에는 다양한 서비스가 존재한다.


<br><br><br>




## 8. 응용계층
데이터 형식이 변환된 후, 7계층에서 브라우저는 웹 페이지를 렌더링한다.  

이 과정에서 사용자가 웹사이트와 상호작용할 수 있도록 필요한 기능을 제공한다.     
사용자와 직접적으로 연결되는 계층으로, 다양한 네트워크 서비스를 제공하여 최종 사용자가 원하는 정보를 손쉽게 접근할 수 있도록 한다.  


<br>

> #### 그 중 HTTP는 뭘까 ?
> - 문자열을 전송하는 프로토콜로 클라이언트와 서버 간 데이터 교환을 담당한다.
> - 텍스트 기반의 통신 규약 : 문자열을 전송할때 문자열의 엔터, 대행문자, 특수문자 등을 처리하기 위해
  어떻게 처리하고 몇 개를 쪼개는 등 이러한 부분에 있어서 사전에 정해진 규칙을 따른다. 데이터를 쪼개거나 포맷팅하는 작업을 포함한다.
> - 연결을 유지하지 않는 비 연결성 프로토콜로 Session 과 Cookie 가 등장했다.
> - 주로 웹 서비스에서 사용되어, 사용자가 웹 페이지를 요청하고 응답받는 과정에서 필수적인 역할을 한다.



<br>


### 웹 서비스와 HTTP 프로토콜
   
인터넷 기반이 대표 서비스 중 하나  

|이름|프로토콜|포트|기능|
|:----:|:----:|:----:|:----:|
|WWW|HTTP|80|웹서비스|
|Email|SMTP/POP3/IMAP|25/110/114|이메일 서비스|
|FTP|FTP|21|파일 전송 서비스|
|DNS|TCP/UDP|53|네임 서비스|
|NEWS|NNTP|119|인터넷 뉴스 서비스|


- **웹 서비스**: 인터넷을 통해 제공되는 서비스    
  사용자가 웹 브라우저를 통해 접근하고 이용할 수 있는 다양한 기능을 포함한다.
- **웹 브라우저**: 사용자가 웹 페이지를 요청하고, 그에 대한 응답을 받아서 표시하는 도구
  이러한 웹 서비스는 웹 브라우저를 통해 사용된다.    
- **HTTP 프로토콜**: 웹에서 정보를 주고받기 위한 규약    
  웹 서비스는 HTTP 프로토콜을 사용하여 데이터를 전송한.  
  클라이언트(사용자)와 서버(웹사이트) 간의 통신을 관리한다.
- **웹 서비스의 도구**:       
  따라서 웹 서비스는 HTTP 프로토콜을 통해 기능을 수행하며, 
  사용자는 이를 통해 인터넷에서 원하는 정보나 서비스를 쉽게 이용할 수 있다.