---
layout: post
title: LIKE 와 full-scan
categories: DB
---

## LIKE
LIKE 는 SQL에서 문자열 패턴 매칭을 위해서 사용합니다.
주로 where절에서 특정 패턴과 일치하는 문자열을 검색하는데 사용할 수 있습니다.
하지만 데이터가 많아지는 경우 문제가 생길 수 있습니다.  
<br/>  
<br/>



## 어떤 문제가 생길까??
LIKE 연산자는 와일드 카드를 사용하여 패턴 매칭을 합니다.
와일드 카드가 패턴의 시작 부분에 위치하면 Mysql의 인덱스를 효과적으로 사용할 수 없습니다.
예를 들어 LIKE '%123' 과 같은 패턴으로 사용하는 경우 인덱스를 사용할 수 없어 성능이 떨어질  있습니다.  
<br/>  
<br/>



## 왜 LIKE '%abc' 패턴은 인덱스를 사용할 수 없을까?
인덱스는 데이터가 정렬된 순서대로 값을 찾을 수 있도록 합니다.
인덱스를 사용하여 검색할 때 왼쪽에서 부터 일치하는 값을 찾습니다.
예를 들어 'abc%'와 같은 패턴은 인덱스를 사용할 수 있으나 '%abc' 의 패턴의 경우 시작점이 명화하지 않기 때문에 인덱스를 사용할 수 없습니다.

이와 같은 패턴을 사용하는 경우 패턴 매칭을 위해 모든 데이터를 순차적으로 스캔해야 하기 때문에full table scan이 발생합니다.
데이터의 양이 많을수록 성능에 영향을 미치며, I/O 비용이 많이 발생합니다.  
<br/>
<br/>  

### '=' 으로 완전일치 검색하는 경우
   인덱스를 사용하여 검색하는 것을 확인할 수 있다.
![img.png](https://github.com/user-attachments/assets/5793a69e-48b4-4d3d-8044-9afbd22840b8)  
<br/>

### LIKE를 이용해서 검색하는 경우
  % 으로 시작하는 문자열을 검색하는 경우 인덱스를 사용하지 않는 것을 확인할 수 있다.
![img.png](https://github.com/user-attachments/assets/1695099b-8f1e-4c03-b339-764eb26ddc7e)  
<br/>
  
- %가 위에 있는 문자열을 검색하는 경우 인덱스를 사용하는 것을 확인할 수 있다.
![img.png](https://github.com/user-attachments/assets/3884a4ad-1f26-49d8-a27c-63dbbf8bc682)    
<br/>  
<br/>  




##### 이제 속도 측면에서 확인해보자 

### '='을 사용한 경우
![img_1.png](https://github.com/user-attachments/assets/d44d1cbe-f203-42de-9621-5a6aad4fecee)
<br/>  
<br/>



### LIKE을 사용한 경우
- %를 시작 부분에서 사용하는 경우
![img_1.png](https://github.com/user-attachments/assets/5c493e49-75e5-4574-a4f4-dd8889a46733  
<br/>  
  
- %를 마지막 부분에서 사용하는 경우
![img_1.png](https://github.com/user-attachments/assets/7fd3b0fc-179f-455b-8c5a-5f21e8cacfe8)
  


% 가 앞에 나오는 경우가 속도가 떨어지는 것을 볼 수 있다.  

