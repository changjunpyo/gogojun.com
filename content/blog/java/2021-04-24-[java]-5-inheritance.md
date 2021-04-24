---
title: '[Java] 5. Inheritance'
date: 2021-04-24 22:04:05
category: java
thumbnail: { thumbnailSrc }
draft: true
---

# 5. 상속



우리는 지난 번에 클래스를 배웠다. 클래스를 통해 객체를 정의 할 수 있고 그 객체를 생성하여 객체의 상태와 행동을 정의했다. 하지만 모든 클래스를 일일히 다 작성해야한다면 정말 많은 객체의 설계도를 만드는 것이 되고 객체지향에서 얘기하는 추상화, 일반화를 통해 얻지 못한다. 

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

생성자를 보면 super 라는 키워드가 보인다. 객체지향에서 `Circle` 클래스는 `PlaneCircle`의 수퍼클래스라고 부르며 그 반대로  `PlaneCircle`은 `Circle` 클래스의 서브클래스라고 한다. 따라서 생성자에서 사용된 `super` 메서드는 `PlaneCircle`에서 호출하게되면 그 수퍼클래스의 생성자를 호출하는 것이다. 

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

 Overloading 비슷하게 발음되지만 다르다. 기존의 오버로딩은 메서드 시그니처가 다른 것이였다면, 오버라이딩은 똑같은 메서드 시그니처를 가진 것을 새로 재정의한다. 즉, 상속에서는 부모클래스에서 정의된 메서드를 자식클래스에서 새로 오버라이딩하여 다른 동작을 구현하고자 하는 것이다.

메서드 오버라이딩은 객체지향 프로그래밍에서 매우 중요하고 유용한 기술이다. (다형성)

예제 추가 필요







