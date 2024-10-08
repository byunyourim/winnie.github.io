---
layout: post
title: Encoding
categories: [Java]
---

## 인코딩?
데이터를 **특정 형식으로 변환하여 저장하거나 전송할 수 있도록 하는 과정**  

인코딩을 통해 데이터를 안전하게 전송할 수 있으며 보다 효과적으로 저장할 수 있다.
  
서로 다른 시스템이나 프로그램 간의 데이터 호환성을 유지하며 데이터 전송 중 보안을 강화하고 민감한 정보를 보호한다.  
또한 데이터를 압축하여 저장 공간을 절약하거나, 특정 형식에 맞추어 데이터를 저장한다.  
데이터를 더 빠르고 안정적으로 전송할 수 있도록 최적화한다.  
 

<br><br><br>


## Mysql 에서 HTML 인코딩
HTML 데이터를 데이터베이스에 저장할 때 특수문자나 HTML 태그로 인해 문제가 발생할 수 있다.  
이러한 문제를 해결하기 위해 데이터 인코딩을 사용할 수 있다.  
  
Mysql에서 HTML 태그를 인코딩하여 데이터베이스에 저장하려면 HTML 특수 문자를 이스케이프 처리해야 한다.  
이를 통해 태그를 안전하게 저장하고 XSS 공격을 방지하는데 도움이 된다.  


<br><br><br>

## 방법

#### Apache Commons Text 라이브러리 설정
Apache Commons Text 라이브러리 추가 필요
<script src="https://gist.github.com/byunyourim/a2abf1e3407b03227f2f92352fad79ca.js"></script>

#### PreparedStatement
HTML 데이터를 그대로 저장한다.  

#### Base64 인코딩
바이너리 데이터를 ASCII 문자열로 변환하는 방법으로,  바이너리 텍스트 형식의 데이터베이스나
텍스트 전송 프로토콜을 통해 안전하게 전송하고 저장할 수 있다.  
HTML 데이터를 Base64로 인코딩하면, 특수 문자를 포함하는 HTML 태그를 안전하게 데이터베이스에 저장할 수 있다.  
<script src="https://gist.github.com/byunyourim/1fa777e2bd9137e9c59b37f7b0b7d269.js"></script>


<br><br><br>

## 그 외 종류
**ASCII 인코딩 (American Standard Code for Information Interchange)**  
가장 오래된 인코딩 방식 중 하나로, 7비트로 표현되며 영문 알파벳, 숫자, 특수 문자를 포함하며, 
확장된 형태 8비트의 ASCII도 있습니다.

**UTF-8 (Unicode Transformation Format - 8-bit)**  
현재 가장 일반적으로 사용되는 문자 인코딩 방식으로 유니코드(Unicode)를 위한 다양한 문자를 다룰 수 있습니다. 
가변 길이 문자 인코딩 방식으로, ASCII와 호환되며 다국적 텍스트 처리에 적합합니다.

**UTF-16**  
유니코드 문자를 16비트 단위로 인코딩하는 방식입니다. 
주로 한글, 한자 등 다국적 문자 처리에 사용됩니다.

**UTF-32**  
유니코드 문자를 32비트 단위로 인코딩하는 방식입니다. 
모든 문자를 고정 길이로 표현할 수 있으며, UTF-16과 UTF-8에 비해 메모리 공간을 많이 사용합니다.

**URL 인코딩 (Percent Encoding)**  
URL에 사용할 수 없는 문자나 특수 문자를 '%XX'와 같은 형태로 인코딩하는 방식입니다.
  
**HTML 엔티티 인코딩**  
HTML 문서에서 사용할 수 없는 문자나 특수 기호를 대체 문자열(&, <, >, 등)로 변환하는 방식입니다.


