---
layout: post
title: URL (Uniform Resource Locator)
categories: Network
---

![uri_diagram.png](https://github.com/user-attachments/assets/80ffb354-5059-4ac1-a922-1f55fc29d933)


## URI (Uniform Resource Indentifier)
<span style='background-color:#D0E4FC'>**리소스를 식별할 수 있는 문자열**</span>  

URL은 URI의 한 부분으로, URI는 더 광범위한 개념
- URL (Locator) : 리소스의 위치 
- URN (Name) : 리소스의 이름, 위치와 무관하게 리소스를 유일하게 식별
  
  
모든 URL은 URI 이지만, 모든 URI가 URL인 것은 아니다.





<br>






## URL(Uniform Resource Locator)
<span style='background-color:#D0E4FC'>**웹 상의 리소스의 위치, 주소**</span>  

어디에서 해당 리소스를 찾을 수 있는지 나타냅니다.  

- 특정 책의 선반 위치
- https:www.example.com/page




<br>



## URN(Uniform Resource NAME)
<span style='background-color:#D0E4FC'>리소스의 이름</span>  

리소스의 위치과 관계없이 리소스를 유일하게 식별합니다.

- 책의 고유한 ISBN 
- urn:isbn:0451450523
  
  

> **URI** : 주소 체계 전체  
> **URL** : 어디에 있는지  
> **URN** : 무엇인지




![urlgraph.png](https://github.com/user-attachments/assets/72e092d7-f897-4ea8-bb2b-fc0a851421ba)



<br>



## https://www.naver.com:443/ 은 무슨 의미일까?

### 1. https
- **Hypertext Transfer Protocol Secure**
- 웹 브라우저와 웹 서버 간의 데이터 전송을 암호화하여 안전한 통신 보장
- https는 일반적으로 TLS/SSL을 사용하여 암호화된다. 
- 데이터의 도청, 변조, 위조 방지  
  
### 2. www
- **호스트 명** : 서버를 식별
- www 는 웹 서버를 의미

### 3. naver.com
- 도메인 네임
- 인터넷에서 특정 웹 사이트 식별
- 읽기 쉬운 형태의 주소

### 4. 443
- 포트 번호
- 웹 서버의 특정 서비스를 식별하는 번호
- 서버가 여러 서비스를 동시에 제공할 수 있도록 합니다. 
- 웹 브라우저가 URL에서 포트 번호를 보면, 해당 포트로 연결을 시도하여 서버와 통신을 시작
- 일반적으로 HTTPS를 사용하는 경우 443 포트 이용


### 5. /
- 리소스 경로
- 서버의 특정 리소스에 접근하기 위한 경로
- /는 서버의 루트 디렉터리를 의미 (홈페이지, 메인 페이)지


#### 도메인과 서브 도메인
- 도메인을 산다 -> naver.com 와 같은 도메인 이름을 등록하고 사용할 권리를 갖는 것  
이 도메인을 사고 그 앞에 www 라는 웹 서버를 만들어 놓은 것이다
- 도메인 이름은 전 세계에서 유일
- 서브 도메인인 www 는 naver.com 도메인 하에서 운영되는 특정 웹 서비스를 나타냅니다.
- 서브 도메인은 특정 서비스나 서버를 분리하고 식별하는 데 사용
  
> www : 웹 서비스
> ftp : 파일 전송 서비스
> mail : 이메일 서비스
  

일반적으로 웹 서비스를 담당하는 호스트는 www를 사용하는 것이 관례이다


    
    naver.com 이라는 도메인을 가진 www 서버에 443 포트로
    https 프로토콜을 이용해서 이 홈페이지로 접속해라