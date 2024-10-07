---
layout: post
title: 상속과 다형성
categories: [Java]
---

# 상속
<span style="background-color: #D0E4FC">**상위 클래스의 기능을 하위클래스로 확장하는 것**</span>  
하위 클래스는 상위 클래스의 속성과 메서드를 사용할 수 있고 확장하거나 변경하여 사용할 수 있다.  
  
   
<br><br><br>
  
   
# 다형성
부모 클래스나 인터페이스 타입의 참조 변수를 통해 **여러 자식 클래스의 객체를 다룰 수 있는 특징**   

메서드 오버라이딩을 통해 구현되며 실제 메서드 호출은 실행 시간에 결정된다.  
  
컴파일 시간의 다형성과 런타임 다형성이 있다.  


<br>  

### 컴파일 다형성
메서드 오버로딩을 통해, 컴파일 시점에 호출될 메서드가 결정된다.

<br>  

### 런타임 다형성
메서드 오버라이딩을 통해, 실행 시점에 실제 객체의 타입에 따라 호출될 메서드가 결정된다.
  
즉, 부모 클래스의 참조 변수가 자식 클래스의 객체를 참조할 때, 실제 객체의 타입에 따라 메서드가 호출된다.


<br>

#### 참조변수 타입
참조 변수의 타입은 Animal 부모 클래스일 수 있지만, 실제로 가리키는 객체는 자식 클래스 Dog, Cat이므로,  
런타임에 자식 클래스에서 오버라이딩된 메서드가 실행된다.  
이로 인해 코드가 더 유연하고 확장 가능하며, 새로운 객체나 기능을 쉽게 추가할 수 있다.  


<br>  

#### 실제 객체 타입
myDog 와 myCat는 각각 Dog 와 Cat 객체를 참조하므로 런타임시 Dog 와 Cat 클래스의 makeSound 메서드가 호출된다.  
  
참조 변수의 타입이 아닌 실제 객체의 타입에 따라, 메서드가 실행되는 것이 바로 다형성의 본질이다.  

이러한 다형성 덕분에 같은 타입의 참조 변수를 사용하여 다양한 객체를 다룰 수 있다.
이것은 코드를 더 유연하고 확장이 가능하게 한다.      
   
예를 들어 여러 종류의 동물들이 있고 이들을 하나의 Animal 타입으로 처리할 수 있게 되면,   
동물 종류가 추가되더라도 기존 코드를 거의 수정하지 않고 새로운 동물 종류를 추가할 수 있다.

<br>  



```java
class Animal {
    void makeSound() {
        System.out.println("Some sound");
    }
}

class Dog extends Animal {
    void makeSound() {
        System.out.println("Bark");
    }
}

class Cat extends Animal {
    void makeSound() {
        System.out.println("Meow");
    }
}

public class Main {
    public static void main(String[] args) {
        Animal myDog = new Dog();
        Animal myCat = new Cat();

        makeAnimalSound(myDog);
        makeAnimalSound(myCat);
    }

    static void makeAnimalSound(Animal animal) {
        animal.makeSound();
    }
}
```

**makeAnimalSound** 메서드는 Animal 타입의 매개변수를 받아서 호출이 되기 때문에, 다양한 동물 객체를 동일한 방식으로 처리할 수 있다.
  
이렇게 하면 동물의 종류가 늘어나더라도 makeAnimalSound 메서드는 수정할 필요가 없다.


<br><br>
   

## 사용 이유
다형성을 통해서 다양한 객체를 동일한 방식으로 처리할 수 있다.   
예를 들어 여러 종류의 동물을 처리하는 메서드를 작성할 때 각 동물 별로 별도의 메서드를 작성할 필요없이 Animal 타입의 매개변수를 받아서 처리할 수 있어 보다 **유연하다.**
  
또한 동일한 코드 블록이 여러 타입의 객체에 대해 동작할 수 있어 **코드의 중복을 줄이고 유지보수를 용이**하게 한다.

새로운 클래스가 추가되더라도 기존의 코드를 수정할 필요 없이, 새로운 클래스의 객체를 처리할 수 있다. (확장성)