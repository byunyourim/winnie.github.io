---
layout: post
title: 오버로딩과 오버라이딩
categories: [Java]
---

# 오버로딩 ️Overloading
<span style="background-color: #D0E4FC">**같은 이름의 메소드나 생성자를 여러 개 정의하는 것**</span>으로 매개변수의 타입 또는 개수가 달라야 한다.


<br>


### 예시
- println() 메서드  
    - System.out 객체의 메소드로 여러 형태로 오버로딩 되어있다.
    - 정수형, 실수형, 문자열 등 다양한 타입의 매개 변수를 받는 메서드를 오버로딩하여 제공한다.  

클래스의 생성자를 여러 개로 정의하여 다양한 초기화 방법을 제공할 수 있다.



<br><br><br>


# 오버라이딩 ️Overriding
<span style="background-color: #D0E4FC">**부모 클래스에서 정의된 메서드를 자식 클래스에서 재정의하는 것**</span>  
자식 클래스에서 재정의된 메소드는 부모 클래스의 메소드와 이름, 매개변수타입, 반환 타입이 동일하다. 


<br>


### 오버라이딩을 했을 때 부모 클래스 메서드의 동작
자식 클래스에서 정의된 메소드가 호출이 되면 부모 클래스의 메서드는 가려진다.   

부모 클래스의 메소드 내용은 무시되고 자식 클래스에서 정의된 메서드가 실행된다.  
만약 자식 클래스에서 부모 클래스의 메서드를 호출하고 싶으면 super키워드를 사용한다.  


<br><br><br> 


## super
부모 클래스로부터 상속받은 필드나 메서드를 자식 클래스에서 참조하는데 사용하는 참조 변수  
자식 클래스의 생성자에서 super() 메소드를 호출하면 부모 클래스의 기본 생성자가 된다.    
  
만약 super(매개변수)로 호출하면 부모 클래스에서 매개변수를 받는 생성자를 호출한다.  
  
  
> #### 주의해야 할 부분!
> super()은 부모 클래스의 생성자는 자식 클래스의 생성자보다 먼저 실행되어야 한다.  
> 그래서 생성자 첫 줄에 super() 을 호출해야하며, 그렇지 않으면 컴파일 에러가 발생한다.  
>
> 또한 자식 클래스에 생성자가 여러 개 있으면 모든 생성자에서 super()을 사용해야한다.   
> 그렇지 않으면 사용되지 않는 생성자에서는 부모 클래스의 생성자 호출이 누락되어 오류가 생길 수 있다.  
>
> 자식 클래스에서 super()을 사용하지 않으면 부모 클래스의 기본 생성자가 호출이 되는데  
> 부모 클래스에 매개변수를 받는 생성자는 존재하나, 기본 생성자가 없는 경우 명시적으로 부모 클래스에 기본 생성자를 생성해 주어야 한다.


<br> 


#### 자식 클래스의 생성자에서 super()를 호출하지 않은 경우 발생하는 컴파일 에러
자식 클래스에서 super()을 호출하지 않으면 "implicitly super constructor is undefined"
컴파일 에러가 발생한다.    
해결하기 위해서는 부모 클래스에 기본 생성자를 생성하거나, 부모 클래스에 다른 매개변수를 받는
생성자를 super()을 사용해서 호출해야 한다.

