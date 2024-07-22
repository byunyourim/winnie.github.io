---
layout: post
title: static
categories: Java
---


## static 변수
변수나 메서드를 정의할때 사용하는 키워드로, 프로그램의 시작과 동시에 메모리에 로드되며 모든 인스턴스가 공유할 수 있는 변수입니다.

모든 객체마다 존재하는 인스턴스 변수와는 다르게 클래스 당 한 개만 존재합니다.
인스턴스 변수는 객체마다 개별적인 값을 갖지만 정적 변수는 모든 객체가 동일한 값을 공유합니다.

클래스에 종속되어 있으며 객체의 생성과는 무관하게 클래스가 로드될 때 딱 한 번 메모리에 할당됩니다.
동일한 데이터를 여러 인스턴스에 저장할 필요가 없기 때문에 메모리 사용의 중복을 줄일 수 있습니다.

JVM의 클래스 영역(Method Area, Metaspace)에 저장됩니다.
Static 변수는 클래스가 처음 로드될 때 한 번만 초기화되며, 프로그램 종료 시까지 유지되기 때문에 메모리 할당과 해제에 따른 오버헤드를 줄여줍니다.


### 정적 메서드

클래스 수준에서 호출할 수 있는 메서드입니다.
해당 클래스의 모든 객체들에 의해서 공유되는 메서드로 객체를 생성하지 않고 클래스 이름으로 직접 호출 합니다

또한 정적 메서드는 정적 변수만 사용할 수 있다.
그리고 정적 메서드는 특정 객체에 속해있지 않기 때문에 this 키워드를 사용할 수 없는 특징이 있습니다.

정적 메서드는 클래스 레벨에서 정의되므로, 메서드 호출 시 객체의 타입이 아닌 클래스의 타입에 따라 호출이 결정됩니다.
또한 정적 메서드는 오버라이딩 되지 않습니다.

일반적으로 Math 함수와 같이 유틸리티 함수나 독립적인 작업을 수행하는 메서드에서 사용됩니다.



## static으로 인한 메모리 낭비
static는 클래스로드 시점부터 메모리에 로듣됩니다. 따라서 큰 객체를 저장하는 경우 해당 객체가 사용되지 않을 때도 메모리를 차지하게 됩니다.
이로 인해 메모리 낭비가 발생할 수 있습니다.


## static 주의 사항
모든 인스턴스에서 공유되기 때문에 여러 스레드에서 동시에 접근할 수 있습니다.
동시성 문제가 발생할 수 있기 때문에 스레듣 간에 동기화를 보장하기 위한 추가적인 처리가 필요합니다.
이렇게 되면 코드의 의존성이 증가하고 클래스 간의 결합도가 높아질 수 있습니다.

Static 변수는 프로그램 종료 시까지 메모리에 유지되기때문에 불필요한 Static 변수 사용은 메모리 사용량을 증가시킬 수 있습니다.
Static 변수는 상태를 공유하기 때문에 단위 테스트 시 테스트 간 의존성이 생길 수 있습니다. 
따라서 Static 변수의 상태를 테스트 전후에 초기화하거나 가능한 한 사용을 자제해야 합니다.

## 동시성 문제 해결 방법
동시성 문제를 해결하는 방법으로는 동기화와 불변성이 있습니다.
동기화는 여러 스레드가 동시에 스태틱 변수를 읽고 쓰는 것을 방지하기 위해 사용합니다.

방법에는 synchronized 키워드를 사용해서 임계영역을 설정하는 방법이 있습니다. 이렇게 되면 한 번에 하나의 스레드만
임계영역에 접근할 수 있게됩니다.

## 불변성
불변성은 스태틱 변수를 변경할 수 없도록 만드는 것입니다.. 변수를 final 로 선언하고 불변 객체를 할당합니다.
이렇게 되면 동시에 여러 스레드가 해당 변수를 읽을 수는 있지만 변경할 수는 없게됩니다.

## 의존성 주입을 통한 테스트 가능성을 확보하는 방법
외부에서 의존 객체를 주입 받아 사용하는 방법이 있습니다.
외부 의존성을 mock 객체로 대체 하여 테스트 케이스를 작성할 수 있습니다.

## static 변수의 사용을 최소화하기 위해 고려해야 할 것
의존성 주입을 통하여 외부에서 필요한 객체를 주입 받도록 설계하는 방법이 있습니다.
이를 통해 클래스 간의 결합도를 낮추고 유연한 테스트 가능한 코드를 작성할 수 있다

또한 하나의 인스턴스를 생성하여 사용하는 싱글톤 패턴을 사용하는 방법입니다. 이를 통해 static 변수 사용을 최소화하고
필요한 경우에만 인스턴스를 생성하여 메모리를 절약할 수 있습니다.

그리고 스프링과 같은 의존성 주입 프레임워크를 사용하여 의존 관계를 관리하고 필요한 객체를 주입 받는 방법이 있다


