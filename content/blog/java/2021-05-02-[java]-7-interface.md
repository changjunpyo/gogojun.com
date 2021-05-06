---
title: '[Java] 7. 인터페이스'
date: 2021-05-02 21:05:92
category: java
thumbnail: { thumbnailSrc }
draft: false
---

## 목표

자바 인터페이스(interface)에 대한 학습



## 목차

- 인터페이스 정의 방법
- 인터페이스 구현 방법
- 인터페이스 레퍼런스를 통해 구현체를 사용하는 방법
- 인터페이스 상속
- 인터페이스의 `default` 메서드(Default method) after Java 8
- 인터페이스 `static` 메서드 after Java 8
- 인터페이스 `private` 메서드, after Java 9



## 인터페이스

자바에서 클래스라는 것은 객체에 대한 설계도의 역할을 했고 그 설계도를 확장하고자 하는 경우 상속을 통해 여러 요소를 추가하기도 하였다. 하지만 자바에서는 다중 상속이 되지 않기 때문에 여러 클래스의 기능을 중복적으로 확장하는 것에는 한계가 있다. 

따라서 인터페이스라는 개념을 도입하였는데, 인터페이스라는 이름과 같이 특정 객체와 클래스를 연결해주는 역할을 하는 설계적인 요소만 담당하기 때문에 기본적으로는 구체적인 메서드의 구현은 포함하지 않는다.  

> 기존에는 인터페이스의 구현에 대한 정보를 포함하는 것이 불가능했지만 자바 8 이후 `default` 예약어를 통해 구현이 가능하다.

인터페이스는 특정 클래스에서 특정 행동(메서드)에 대한 강제적인 역할을 하며 각각의 행동(메서드)은 클래스에서 각각 구현 되어야한다.

## 인터페이스의 정의

- 인터페이스의 모든 메서드는 추상적이다.(기본적으로 `abstract` )
- 인터페이스는 기본적으로 모두에게 열려있는 공용 API를 가정하므로 `public` , 하지만 `default` 접근 지시자 사용 가능하다.
- 인터페이스는 구현을 하는 것이 아니므로 필드를 정의 할 수 없다(상수 필드인 `static final` 필드만 가능하다).
- 인터페이스는 인스턴스화가 불가능하므로 생성자가 없다.
- 인터페이스를 중첩 구현하도록 하는 것이 가능하다.
- 인터페이스가 인터페이스를 상속하여 정의하는 것이 가능하다.

```java
제어자(생략시 public) interface InterfaceName{
  
}
```

```java
public interface 인터페이스명{
  // 상수 필드(변경 불가)
  제어자(static final 만 가능) 타입 상수명 = 값
  
  // 추상 메서드(구현 불가, 오버라이딩 필수)
  (abstract) 타입 메서드명(매개변수...);
  
  // 기본 메서드(구현 필수 , 오버라이딩 가능)
  default 타입 메서드명(매개변수...){
    //구현부
  };
  // 정적 메서드(구현 필수, 오버라이딩 불가능)
  static 타입 메서드명(매개변수 ...){
    // 구현부
  }
}
```



인터페이스는 추상 클래스와 매우 비슷한 측면을 가진다. 

### 인터페이스와 추상클래스의 공통점

- 인스턴스를 생성하는 것이 불가능하다.
- 상속받은 클래스에서 구현하여 그 클래스에서 인스턴스화 하여 사용 된다.

### 인터페이스와 추상클래스의 차이점

- 추상 클래스는 일반 메서드와 추상 메서드를 둘 다 가질 수 있다.(하지만 java 8 이후 default method를 통해 일반 메서드 가능)

- 인터페이스는 존재하는 메서드를 무조건 `public abstract`로 선언하며 이를 생략 가능하다.

- 인터페이스는 존재하는 변수를 무조건 `public static final`로 선언하며 이를 생략 가능하다.

- 추상클래스의 경우 다중 상속이 불가능하지만, 인터페이스의 경우 다중으로 상속하는 것이 가능하다. 

  

### 추상클래스와 인터페이스의 개념

- 추상클래스는 **is-a 관계** 즉,  **~는 ~이다** 의 개념
- 인터페이스는 **has-a 관계** 즉, **~는 ~를 할 수 있다** 의 개념

Ex) LinkedList의 경우 AbstactSequentialList 이면서 List가 하는 기능을 할 수 있다.

```java
public class LinkedList extends AbstactSequentialList implements List...
```



## 인터페이스 구현 방법

인터페이스를 통해 메서드를 호출하게 되면 인터페이스는 객체의 메서드를 호출한다.

객체는 인터페이스의 정의된 메서드와 동일한 시그니처(메서드 이름, 매개변수)를 갖고 있어야 한다. 

이러한 객체를 인터페이스를 구현(implements)한 객체라고 하며, 이 객체를 생성하는 클래스를 구현 클래스라고 한다.



### 인터페이스 구현 클래스

```java
public class 구현클래스 implements 인터페이스명{
  // 인터페이스에 있는 모든 추상 메서드를 구현 하여야한다.
}
```



예제를 위하여 간단한 상황을 통해 이해해보자. 일종의 2D 게임이 존재하며 특정 몬스터는 가로 및 세로로 이동이 가능하다. 

**Movable.java**

```java
// Movable 인터페이스
public interface Movable{
  void move();
}

```

**Monster.java**

```java
public class Monster implements Movable{
  private int x, y;
  private int movingSpeed;
  private boolean toRight;
  ... 이하 생략
  
  @Override
  public void move(){
    if (toRight){
      x += movingSpeed;
    } else{
      x -= movingSpeed;
    }
  }
  ... 생략
}
```

객체가 다수의 인터페이스의 기능을 가지고자 하는 경우가 있을 수 있다. 게임에서 플레이 캐릭터는 좌우로 움직이는 것 뿐만아니라 위로 점프도 가능하다면 `Movable`이라는 인터페이스뿐만아니라 Jumpable 의 기능도 구현해야한다. 따라서 아래와 같은 캐릭터는 여러 가지의 인터페이스를 구현한다.( 점프 이후 착지와 같은 고려사항이 있지만 간단하게 하기 위해 생략)

**Jumpable.java**

```java
// Jumpable 인터페이스
public interface Jumpable{
  void jump();
}

```



**Character.java**

```java
public class Character implements Movable , Jumpable{
  private int x, y;
  private int movingSpeed;
  private int jumpSpeed;
  private boolean toRight;
  ... 이하 생략
  
  @Override
  public void move(){
    if (toRight){
      x += movingSpeed;
    } else{
      x -= movingSpeed;
    }
  }
  @Override
  public void jump(){
    y +=  jumpSpeed;
  }
  ... 생략
}
```

다중 인터페이스 구현의 경우 위와 같이 Movable과 Jumpable의 모든 추상메서드의 구현이 필요하다.

## 인터페이스 레퍼런스를 통해 구현체를 사용하는 방법

기존의 클래스 상속에서 서브클래스의 객체를 부모클래스의 참조로 이용 가능했다. 인터페이스의 경우도 마찬가지로 인터페이스를 통해 구현 객체를 참조하는 것이 가능하며 인터페이스로의 형변환 또한 가능하다. 인터페이스 타입의 매개 변수 또한 인자를 넘겨줄 때 인터페이스를 구현한 객체를 넘겨 받아야한다는 것을 의미한다.

 

## 인터페이스의 상속

인터페이스 또한 클래스와 같이 상속하여 확장하는 것이 가능하며, 클래스와는 다르게 다중 상속이 가능하다. 이것은 인터페이스는 모두 추상 메서드로 서로 겹치는 메서드의 경우도 구현부에서 결정할 수있기 때문이다. 

클래스의 상속과 마찬가지로 자손 인터페이스는 부모의 인터페이스의 모든 멤버와 메서드를 상속받고 자손 인터페이스를 구현하는 경우 부모 인터페이스와 자손의 모든 인터페이스를 전부 오버라이드하여 구현 해주어야 한다.

### 상속과 인터페이스 레퍼런스에 대한 예제

**Bird 인터페이스는 Animal 인터페이스를 상속받은 인터페이스이다.**

```java
interface Animal{
  void move();
  void eat();
}

interface Bird extends Animal{
  void fly();
}
```



`Eagle` 클래스는 `Bird` 인터페이스를 구현한 클래스이다.

- `move()` 와 `eat()`은 `Bird`가 상속 받은 `Animal`의 추상 메서드를 구현
- `fly()`는 `Bird`의 추상 메서드를 구현

```java
public class Eagle implements Bird{
  @Override
  void move(){
    System.out.println("Eagle can move");
  }
  @Override
  void eat(){
    System.out.println("Eagle can eat");
  }
  @Override
  void fly(){
    System.out.println("Eagle can fly");
  }
  void printName(){
    System.out.println("My name is Eagle");
  }
}
```



- 클래스 레퍼런스와 인터페이스 레퍼런스의 사용

```java
public static void main(String[] args){
  Eagle eagle = new Eagle();
  // 클래스 타입 레퍼런스
  eagle.move();
  eagle.eat();
  eagle.fly();
  eagle.printName();
  
  // 인터페이스 타입 레퍼런스
  Bird bird = eagle;
  bird.move();
  bird.eat();
  bird.fly();
  
  Animal animal = bird;
  animal.move();
  animal.eat();
}
```

클래스 타입 레퍼런스는 클래스에 정의된 모든 메서드를 호출 할 수 있다.

인터페이스 레퍼런스는 해당 인터페이스를 구현한 모든 클래스를 가리킬 수 있고 해당 인터페이스의 메서드만 호출이 가능하다.

### 다중 인터페이스에서 인터페이스 레퍼런스

- 한 클래스가 여러 인터페이스를 구현하였다면 인터페이스로 구분하여 그 객체를 이용 가능하다.

즉 어떤 인터페이스를 통해 사용하느냐에 따라서 사용 규칙이 달라지게 된다.

위 `Character` 예제에서 보면

```java
public static void main(String[] args){
  Character ch = new Character();
  ch.move();
  ch.jump();
  
  Movable m = ch;
  m.move(); // 좌우 이동만 가능
  
  Jumpable j = ch;
  j.jump(); // 점프만 가능
  
}
```



## 인터페이스의 기본 메서드(default method) 

인터페이스가 변경이 일어나게 되는 것은 보통 추가적으로 인터페이스에 기능이 추가되는 경우를 말한다. 기능이 추가된다는 것은 추상메서드를 추가하는 것이고 이것을 구현한 객체 모두에서 추가적인 구현이 필요하게 된다. 

물론 인터페이스를 변경하지 않는 것이 최선이지만 변경이 필요한 경우는 생길 수 있다. 이 문제를 해결 하기 위해 기본 메서드라는 것이 java8에서 추가 되었다. 기본 메서드라는 것은 추상 메서드에서 기본적으로 구현을 제공하는 메서드로, 추상 메서드가 아니기 때문에 인터페이스를 구현한 객체에서 모두 오버라이드하여 구현을 추가할 필요가 없기 때문에 구현 클래스를 변경하지 않아도 된다.

```java
interface InterfaceName{
  void method1();
  default void method2() {
    // 구현부
  }
}
```

기본 메서드를 추가한다면 기존의 클래스는 조상 클래스에서 구현된 메서드를 사용하는 것과 동일한 효과를 얻게 된다.

##### Default method 충돌

새로 추가된 기본 메서드가 충돌하는 경우 다음과 같은 규칙이 적용 된다.

- 여러 인터페이스 간의 충돌 -> 구현 클래스에서 오버라이딩하여 구현하여 사용해야한다.
- 디폴트 메서드와 조상 클래스에서의 충돌 -> 조상 클래스의 메서드가 상속되며 디폴트 메서드는 무시된다.



## 인터페이스 static 메서드

자바 8 이후 정적 메서드라는 것을 인터페이스에서 사용 가능하다.

- 해당 인터페이스를 구현한 모든 인스턴스에 타입과 관련된 유틸리티, 헬퍼 메서드를 제공하고 싶다면 static 메서드를 사용 가능하다.
- 인스턴스 없이 수행할 수있는 작업을 정의할 수 있다.
- 오버라이딩이 불가능하다.

## 인터페이스 private 메서드

자바 9 이후 private 메서드를 인터페이스에서 사용 가능 하다.

- 메서드의 구현이 존재하며 추상메서드가 아니다.
- 구현체에서 정의하는 것이 불가능하고 자식 인터페이스에서 상속할 수 없다.
- `static` 메서드도 `private`가 가능하다.

