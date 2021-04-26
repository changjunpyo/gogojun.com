---
title: '[Java] 5. Inheritance'
date: 2021-04-24 22:04:05
category: java
thumbnail: { thumbnailSrc }
draft: false
---

우리는 지난 번에 클래스를 배웠다. 클래스를 통해 객체를 정의 할 수 있고 그 객체를 생성하여 객체의 상태와 행동을 정의했다. 

하지만 모든 클래스를 일일히 다 작성해야한다면 정말 많은 객체의 설계도를 만드는 것이 되고 객체지향에서 얘기하는 추상화, 일반화를 통한 이점을 얻지 못한다. 

아래 다음과 같이 `Circle` 이라는 클래스가 있다.

## 부모클래스

```java
package com.study.java.week5;

public class Circle {
    public final double PI = 3.141592;

    protected double r;

    public Circle(double r) {
        this.r = r;
    }

    public double circumference() {
        return 2 * PI * r;
    }
    public double area(){
        return PI * r * r;
    }
    public double radius() {
        return r;
    }
}
```

원에 대한 객체로 r이라는 반지름 필드를 가지고 있고, 지름, 넓이 등을 계산하는 메서드가 존재한다. 

자, 이제 우리는 여기서 2차원 좌표에서 정의되는 원을 나타내는 클래스를 만들고 싶다. 새로운 클래스 `PlaneCircle`을 만들고자 한다면 이것은 지름뿐만 아니라 `x`,`y` 좌표 값에 대한 정보가 필요하다. 이것을 기존의 `Circle`의 기능과 특성을 잃어버리지 않으면서 새로운 클래스로 확장하고자 한다. 

이런 새로운 기능들을 기존의 클래스의 기능에서 추가하고 확장 할 수있는 기능이 객체지향 프로그래밍에서 핵심적인 부분이다.

## 서브클래스

```java
public class PlaneCircle extends Circle {
    private final double cx, cy;

    public PlaneCircle(double r, double cx, double cy) {
        super(r);
        this.cx = cx;
        this.cy = cy;
    }
    public double getCx() {
        return cx;
    }
    public double getCy() {
        return cy;
    }

    public boolean isInside(int x, int y){
        double dx = x- cx;
        double dy = y- cy;
        double distanceSquare = dx*dx + dy*dy;
        return (distanceSquare < r * r);
    }
}

```

 위 첫 줄에 `extends` 키워드를 통해 자바에게 이 클래스는 `Circle`의 서브클래스이고 그 필드와 메서드를 상속했다는 것을 알려주게 된다. 

생성자를 보면 super 라는 메서드가 보인다. 객체지향에서 `Circle` 클래스는 `PlaneCircle`의 수퍼클래스라고 부르며 그 반대로  `PlaneCircle`은 `Circle` 클래스의 서브클래스라고 한다. 따라서 생성자에서 사용된 `super` 메서드는 `PlaneCircle`에서 호출하게되면 그 수퍼클래스의 생성자를 호출하는 것이다. 

> `extends`를 키워드로 수퍼 클래스를 명시하지 않은 객체는 자동으로 `java.lang.Object`에 정의된 `Object` 객체를 상속하며 따라서 메서드를 상속받는다. 수퍼클래스가 존재하지 않는 객체는 Object가 유일하다. 결국 `Object` 모든 클래스의 조상 클래스이다.

클래스에서 사용되는 `this`와 비슷하게 생성자에서 사용되면 부모 클래스의 생성자를 호출하는 것이고, 필드로 사용된다면 부모 클래스의 멤버를 참조할 때 사용한다. `this`에서 매개 변수와 이름이 같을 때 구분했던과 같이 똑같은 변수를 가질 수 있는 부모와 자식클래스에서 구분을 해주는 역할을 해준다. 

```java
// 모두 같은 x라는 필드를 가진다
// 구분하는 방법
Class A{private int x;}
Class B extends A{private int x;}
Class C extends B{private int x;}


// 객체 C에서 각 변수를 부르는 방법
x 						// C에서 필드 x
this.x				// C에서 필드 x
super.x				// B에서 필드 x
((B)this).x		// B에서 필드 x
((A)this).x		// A에서 필드 x
super.super.x	// 오류;

```



### 상속된 객체 / 서브 클래스의 특징

`isInside` 메서드를 보면 `r` 값은 클래스에서 정의하지 않았지만, 상속된 필드를 사용 하였다. 또한 메서드들도 사용되었기 때문에 `PlaneCircle`에서 `circumference`,`area()`를 마치 `PlaneCircle`에서 정의한 것처럼 호출하는 것 또한 가능하다. 

또한 모든 `PlaneCircle` 객체는 완벽하게 `Circle` 객체로 생각된다. 이게 무슨 의미인지 자세히 보면 아래의 코드처럼 `PlaneCircle`은 `Circle` 변수에 할당되는 것이 가능하다. 즉, 좌표에 대한 것은 모두 생각하지 않고 `Circle`이라고 생각할 수있다는 점이다.

```java
PlaneCircle pc = new PlaneCircle(2.0, 0.0, 0.0);
Circle c = pc;
```

 

위와 같은 할당은 타입 캐스팅 없이 가능하게 된다. 물론 실제로 c에 할당 되어있는 값은 사실 `PlaneCircle` 객체이다. 하지만 이것을 컴파일러는 알지 못하게 되고 다시 `PlaneCircle`에 할당하고자 한다면, 반드시 타입 캐스팅을 해주어야 한다. 서브 클래스의 메서드를 사용하고자 하는 경우도 마찬가지이다.

```java
PlaneCircle pc2 = (PlaneCircle) c;
boolean inside = ((PlaneCircle) c).isInside(0.0, 0.0);
```



## Overriding (오버라이딩)

 Overloading 비슷하게 발음되지만 다르다. 기존의 오버로딩은 **메서드 시그니처**가 다른 것이였다면, 오버라이딩은 똑같은 메서드 시그니처를 가진 것을 새로 재정의한다. 즉, 상속에서는 부모클래스에서 정의된 메서드를 자식클래스에서 새로 오버라이딩하여 다른 동작을 구현하고자 하는 것이다. 

`@override` 어노테이션의 경우 없어도 상관은 없다. 추가하면 이것이 overriding 된 것인지(부모 메서드나, 인터페이스에서 존재하는지 확인) 확인하여 철자가 틀리거나 타입 순서 등이 틀리는 것을 미리 확인하여 알려준다. 만약 철자가 실수하게되면 다른 메서드가 새로 존재하는 것이 된다.

메서드 오버라이딩은 객체지향 프로그래밍에서 매우 중요하고 유용한 기술이다. (다형성)

**[참고] Method Signature vs Method Type** 

**메서드 시그니쳐** : 메서드명, 매개 변수 타입

**메서드 타입**: 리턴 타입, 메서드 타입 파라미터, 메서드 인자 타입, 예외

=> Java 8에 있는 Method Reference가 나오면서 메서드 타입이라는 것을 정의하게 됨



## 다이나믹 메서드 디스패치(Dynamic Method Dispatch)

Method Dispatch: 어떤 메소드를 호출할지 결정하여 실제로 실행시키는 것

#### 정적 메서드 디스패치

컴파일러가 특정 시점에 어떤 메서드가 실행되는지 알고 있고 결정하는 것이다. 컴파일된 바이트코드를 확인해보면 실행되는 메서드의 정보를 가지고 있다. 런타임시 결정되는 것이 아니라 미리 정적으로 호출할 메서드를 알고있다는 뜻이다.

오버로드가 되는 메서드는 역시 정적 메서드 디스패치가 일어난다.  어떤 인자를 받을지 컴파일러가 확인하여 어떤 메서드가 호출되는지 구분할 수 있기 때문에 이렇게 미리 알 수 있다. 



```java
Class Car{
  int prices;
  double efficiency;
  Car(){ 
  }
  Car(int prices){
    this.prices = prices;
  }
  Car(double efficiency){
    this.efficiency;
  }
}
-----
Car c1 = new Car(1); 
Car c2 = new Car(1.0); // 들어오는 타입을 보고 컴파일러가 어떤 오버로딩된 생성자를 가져올지 결정
```



#### 동적 메서드 디스패치  

정적 디스패치 방식은 컴파일러가 어떤 메서드를 가져올지 파악하여 결정하였다면, 다이나믹 메서드 디스 패치는 런타임(실행과정)에서 어떤 메서드를 실행할 지 결정하게 된다. 보통 추상클래스, 인터페이스에서 메서드를 사용하는 경우 가져오는 방식이다.

```java
public abstract class Animal{
  abstract void move();
  abstract void eat();
}
class Human extends Animal{
		@override
  	void move(){
      System.out.println("human move");
    }
  	@override
  	void eat(){
      System.out.println("human eat");
    }
}
class Dog extends Animal{
		@override
  	void move(){
      System.out.println("dog move");
    }
  	@override
  	void eat(){
      System.out.println("dog eat");
    }
}

List<Animal> a = Arrays.asList(new Human(), new Dog());
a.forEach(x -> {
  x.move;
  x.eat;
});
// 출력
// human move
// human eat
// dog move
// dog eat
```

동적 디스패치는 어떤 방식으로 작동이 가능할까? 

자바에서는 기본적으로 명시되어있지는 않지만(java8 부터는 명시하여 보내는 방식도 가능) implicit하게 항상 호출하는 객체를 같이 보내게 된다. 따라서 보낸 객체를 this를 이용하여서 호출 객체를 참조하여서 메서드를 실행하게 되고, 이것을 통해 동적 메서드 디스패치가 가능하게 된다.


## 추상 클래스(abstract)

### 추상클래스란?

추상이라는 것은 실체 간에 공통되는 특성을 추출한 것을 의미한다.

추상클래스는 객체를 직접 생성할 수있는 실제 클래스들의 공통적인 상태와 행위(필드, 메서드)를 추출하여 선언한 클래스 

### 추상클래스의 목적

1. 여러 실체 클래스들의 공통 필드와 메서드 이름을 통일하기 위함
2. 실체 클래스의 작성 기간을 절약하기 위함
3. 규격에 맞는 실제 클래스 구현

따라서 스스로 완성된 객체가 아닌 추상적인 틀만 가지고 있기 때문에 추상클래스명으로 객체를 생성하지 못한다.

### 추상 클래스의 문법

```java
public abstract class 클래스명{
  public int x;
  public void 그냥메서드(){...}
  public abstract void 추상메서드(); //구현부 없음
}
```

위와 같이 클래스에 추상 메서드를 포함하는 클래스를 추상 메서드라고 정의한다. 그리고 이 추상메서드를 상속받은 클래스에서 추상메서드들을 재정의하여서 구현해주어야 한다. 

간단한 예시로 위 `Animal` 클래스를 확인하자. 위에서 보면 `Animal` 클래스로 `Human`, `Dog` 객체가 자동 형변환이 일어나고 재정의된 메서드를 동적 디스패치를 통해 호출함으로 다형성을 만족시키고 있다.



### final 키워드

클래스, 필드, 메서드에 선언하는 키워드로 해당 선언을 최종으로 추후 수정이 불가능한 마지막 상태임을 명시한다.

#### final 클래스

```java
public final class InheritanceImpossible{...}
```

클래스에 선언에 사용을 하게 되면 이 클래스는 상속을 해줄 수 없는 클래스, 즉 부모 클래스의 역할을 해줄 수 없다. 

유명한 final 클래스로는 `String` 클래스가 존재하게 되며, 상속하려 하는 경우 컴파일 에러가 발생하게 된다.

#### final 메서드

메소드에 final 키워드를 적용하면, 이 메서드는 오버라이딩이 불가능한 메서드임을 명시한다. 자식 클래스에서 부모클래스의 final 메서드는 재정의 하는 것이 불가능하다. 재정의 시 컴파일 에러가 발생한다.

```java
public final 리턴타입 메서드명(매개변수 ...) {...}
```



### Object 클래스

모든 클래스의 루트 조상인 Object 클래스는 다음과 같은 메서드를 포함하고 있다. 

자세한 내용은 [Object 명세 참고](https://docs.oracle.com/javase/10/docs/api/java/lang/Object.html)

```java
    public final native Class<?> getClass()
    public native int hashCode()
    public boolean equals(Object obj) 
    protected native Object clone() throws CloneNotSupportedException;
    public String toString()
    public final native void notify()
    public final native void notifyAll()
    public final void wait() throws InterruptedException
    public final native void wait(long timeoutMillis) throws InterruptedException
    public final void wait(long timeoutMillis, int nanos) throws InterruptedException
      
    @Deprecated(since="9")
    protected void finalize() throws Throwable 
```





#### 참고

1. Java in a Nutshell 7th edition by Benjamin J.Evans & David Flanagan
2. https://defacto-standard.tistory.com/413