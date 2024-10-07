---
layout: post
title: String 객체를 선언하는 방법
categories: [Java]
---

String 을 선언하는 방법에는 두 가지 방법이 있습니다.  
  
  
두 방식은 **저장 공간(메모리)**에 차이가 있습니다.  


<br>

 
## 리터럴로 선언
리터럴로 선언하는 경우 문자열을 String Constant pool에 저장한다.  
따라서 메모리의 효율이 좋다.
  
리터럴을 사용하여 생성할 때 상수 풀에 같은 값이 존재하면 객체는 이미 존재하는 값을 참조한다.  
따라 동일한 문자열을 리터럴로 선언한 변수는 모두 같은 객체를 참조한다.



<br>



## new 키워드 사용
new 키워드를 이용하여 선언하는 경우 객체가 힙 메모리에 저장된다.  
  
항상 새로운 객체가 생성되기 때문에 항상 새로운 인스턴스가 필요한 경우 사용한다.  
또한 동일한 문자열의 내용이라도 다른 객체를 참조한다.

```java
String a = new String("hello");
String b = new String("hello");

System.out.println(a == b); // false (두 변수는 다른 객체를 참조)
System.out.println(a.equals(b)); // true (두 변수의 문자열 내용은 같음)
```