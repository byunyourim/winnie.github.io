---
layout: post
title: 쿠키, 세션 그리고 토큰 인증
categories: [Network]
---

HTTP는 상태를 Stateless 의 비연결 프로토콜이다.   
따라서, 서버는 이전의 요청이 정보에 대해서 알 수 없다.  

HTTP 비연결성, 보안 문제를 해결하기 위한 방법에는 쿠키, 세션, 토큰 방식이 있다.  


## 쿠키 인증

쿠키는 서버가 사용자의 웹 브라우저에 전송하는 작은 데이터 조각이다.  
key=value 형식의 문자열 데이터 묶음으로,  

브라우저는 이 문자열 데이터 조각을 저장해두었다가, 동일한 서버에서 재 요청시 쿠키 데이터를 전송한다.  

서버와 클라이언트 간의 연결 유지를 구현하기 위해 서로를 인식할 수 있는 식별데이터이다.

- 브라우저에 정보를 저장하여, 사용자가 웹 사이트에 다시 방문할때 로그인 정보나, 사용자의 정보를 유지할 수 있다.  
- 쿠키는 서버에서 생성하고 클라이언트에 저장된다.
- 이후 요청마다 자동으로 서버로 전송된다.


<br>

#### 인증 방식  

<br>

#### 단점
- 이 방식은 요청시 쿠키의 값을 그대로 보내기 때문에 유출 또는 조작될 위험이 크다.
- 용량에 제한이 있어 많은 정보를 담을 수 없다.
- 브라우저마다 쿠키에 대한 지원 형태가 달라 브라우저가 공유가 불가능하다.
- 쿠키의 사이즈가 커질수로 네트워크 부하가 심해진다.




<br><br><br>

## 세션 
쿠키 방식은 상태를 유지할 수는 있지만 보안의 위험이 있다.  
이를 해결하기 위해 중요한 정보를 서버측에서 관리하는 방식이다.  



- 세선은 서버에 데이터를 저장한다.  
- 사용자가 로그인을 하면, 서버는 그 사용자에 대한 세션 ID를 생성한다. 
- 그리고 그 세션ID을 클라이언트에 보낸다.
- 이후 요청시, 클라이언트는 세션 ID를 서버에 보내고, 서버는 ID를 바탕으로 사용자의 상태를 얻는다.  
- 민감한 데이터가 서버에 저장되며, 클라이언트 측에서 접근할 수 없기 때문에, 보안에 좋다. 

세션은 쿠키를 기반으로 동작한다.  




<br>

## 인증방식



<br>

## 단점
- 탈취자가 세션ID 자체를 탈취하여 클라이언트로 위장할 수 있다.
- 사용자가 많아지는 경우 서버에 부하가 심해진다.



<br><br><br>

## 토큰 인증

클라이언트가 서버에 접속하면 서버에서 해당 클라이언트에 인증되었다는 의미로 '토큰'을 부여한다.  

토큰은 유일하며, 토큰을 발급받은 클라이언트는 다시 서버에 요청을 보낼 때 요청 헤더에 토큰을 심어서 보낸다.  

서버에서는 클라이언트로부터 받은 토큰을 서버에서 제공한 토큰과의 일치 여부를 확인하여 처리한다.  


토큰은 서버가 아닌 클라이언트에 저장되기 때문에 서버에 부담을 줄 수 있다.  


- 서버가 세션을 유지할 필요 없이, 클라이언트가 요청 시 토큰을 사용해 인증할 수 있다. 
- JWT 같은 토큰은 서버가 아닌 클라이언트 측에서 발급 및 저장되며, 각 요청 시 토큰을 함께 보내 인증한다. 
- 토큰에는 필요한 정보(사용자 정보, 만료 시간 등)가 포함되어 있어,
  서버가 매번 데이터베이스를 조회할 필요 없이 사용자 신원을 확인할 수 있다.
- API 및 분산 시스템에 적합하며, 서버에 부하가 적다.


<br>

## 인증방식



<br>

## 단점
- 토큰 자체의 데이터 길이가 길어져, 인증 요청이 많아질수록 네트워크 부하가 심해질 수 있다.  
- Payload는 조회가 가능하기 때문에 유저의 중요한 정보를 담아서는 안된다.
- 탈취시 대처 방법이 없다.