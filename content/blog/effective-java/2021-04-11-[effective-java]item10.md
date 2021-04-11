---
title: '[Effective-Java] Item10. equals는 일반 규약을 지켜 재정의하라'
date: 2021-04-11 16:04:46
category: effective-java
thumbnail: { thumbnailSrc }
draft: false
---

`equals` 메서드는 일반적으로 재정의하기 쉬워보이지만 실수하기 쉬운 함정이 많아 조심해야한다. 

## equals를 재정의 할 필요 없는 경우

### 1. 각 인스턴스가 본질적으로 고유하다.

값을 표현하는게 아니라 동작하는 개체를 표현하는 클래스들이 여기에 해당된다. 주로 Thread라는 실행단위를 나타내기 때문 이런 클래스는 기존의 `Object`의 `equals` 메서드를 그대로 사용해도 된다.

### 2. 인스턴스의 '논리정 동치성'을 검사할 일이 없다.

예를 들어, `java.util.Random` 클래스가 존재하는데, 이 클래스에서 `equals`는 랜덤 객체가 같은 난수열을 가지는지 확인할 수 있게 재정의 할 수 있었지만, 이 클래스를 만드는 사람은 그런 기능을 클라이언트가 원할 것이라고 생각하지 않았기 때문에 만들지 않았습니다.

### 3. 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어 맞는다.

상위에서 상속받은 equals가 하위에서 사용하기 무리 없을 때 equals를 재정의 하지 않아도 된다.
예로 대부분은 `Set` 클래스(`HashSet`, `TreeSet` 등)은 `abstractSet`의 `equals` 메서드를 그대로 사용한다. 

### 4. 클래스가 private이거나 package-private이고 equals 메서드를 호출 할 일이 없다.

내부 클래스로 사용하기 위해 private라고 선언하였다면 `equals`를 사용 할 일이 없다.

이럴 경우 `equals`를 재정의 하지 않아도 된다. 혹시나를 위해 아래와 같이 사용하여도 된다.

```java
@Override
public boolean equals(Object o) {
  throw new AssertionError(); // 호출 금지
}
```

## 

## equals 재정의가 필요한 경우

재정의가 필요한 경우는 `equals`를 통해서 논리적 동치성(내부 값이 같음)을 확인하고 싶은데, 상위 클래스의 `equals`는 논리적 동치성을 확인하지 않을 때 재정의 하여 사용하여야 한다.

주로 값 클래스가 위의 경우에 해당하는데, 예를 들어 `String`과 `Integer`처럼 값을 표현하는 클래스를 말한다. 두 값 객체를 비교하고자 하면 객체가 같음을 확인하기 보다 값이 같음을 확인하고 싶어한다. 

따라서 `equals`를 논리적으로 같음을 재정의 한다면, 그 값을 비교하길 원하는 프로그래머의 기대에 부응함은 물론 Map의 키와 Set의 키로 사용할 수 있다. 값 클래스여도 Enum과 같이 여러개의 객체가 만들어지지 않음을 보장한다면 어짜피 객체가 유일하게 존재하기 때문에 객체의 동일성이 논리적 동일성과 같은 의미가 된다.



#### equals를 재정의 하는 경우 필요한 일반 규약(Object 명세에 관한 규약)

equal는 동치 관계를 구현하며, 다음을 만족한다.

- **반사성(reflexivity)**: `null`이 아닌 모든 참조 값 `x`에 대해, `x.equals(x)`는 `true`다

- **대칭성(symmetry)**: `null`이 아닌 모든 참조 값 `x`, `y`에 대해, `x.equals(y)`는 `true`면 `y.equals(x)`도 `true`다.

- **추이성(transitivity)**: `null`이 아닌 모든 참조 값 `x, y, z` 에 대해, `x.equals(y)`가 `true`고 `y.equals(z)`도 `true`이면 x.equals(z)도 true 다. 

- **일관성(Consistency)**: `null`이 아닌 모든 참조값 `x, y`에 대해, `x.equals(y)`를 반복해서 호출하면 항상 `true`를 반환하거나 항상 `false`를 반환한다.

- **null-아님**: `null`이 아닌 모든 참조 값 `x`에 대해, `x.equals(null)`은 `false`다.

즉, `equals`를 사용한다면 우리는 위의 규약을 다 지켜진 것이라고 생각하고 사용할 것이므로 규약을 반드시 지켜서 구현해야 한다.

### 반사성

**반사성**의 경우 객체는 자기 자신과 같아야한다는 조건으로 일부러 어기는 경우가 아니라면 대부분 만족한다.

### 대칭성

**대칭성**의 경우 두 객체는 서로에 동치 여부에 똑같이 답해야 한다는 뜻이다. 반사성 요건과 달리 대칭성 요건은 자칫하면 어길 수 있다. 

대소문자를 구별하지 않는 문자열을 구현한 다음 클래스를 예로 보자. 이 클래스의 `toString`은 그대로의 대소문자를 돌려주지만 `equals`의 경우는 대소문자를 무시한다.

```java
public final class CaseInsensitiveString {
  private final String s;
  
  public CaseInsensitiveString(String s){
    this.s = Objects.requireNonNull(s);
  }
  // 대칭성 위배
  @Override
  public boolean equals(Object o){
    if (o instanceof CaseInsensitiveString){
      return s.equalsIgnoreCase(((CaseInsensitiveString) o).s);
    }
    if (o instanceof String){
      return s.equalsIgnoreCase((String) o);
    }
  }
}
```

위와 같은 `equals`를 보면 일반 문자열과도 비교를 시도한다. 하지만 다음과 같은 예제를 보자

```java
CaseInsensitiveString cis = new CaseInsensitiveString("Word");
String str = "word";
```

예상하듯이 `cis.equals(str)`은 `true`이다. 문제는 `CaseInsensitiveString`의 `equals`은 `String` 객체를 알지만, 그 반대 경우 `String`은 `CaseInsensitiveString`의 존재를 모른다. 따라서 `str.equals(cis)`는 `false`가 된다. 이것은 명백하게 대칭성을 위반한 것이다. `equals`의 규약을 어기게 되면 그 객체를 사용하는 다른 객체들이 어떻게 반응하는지에 따라 다른 결과가 나오게 된다. 

따라서 대칭성의 문제를 해결하기 위해서 `CaseInsensitiveString`에서 모든 `String`과도 연동하겠다는 꿈을 버려야한다. 

그렇게 구현한 코드는 다음과 같다.

```java
@Override
  public boolean equals(Object o){
    return o instanceof CaseInsensitiveString &&
      ((CaseInsensitiveString) o).s.equalsIgnoreCase(s);
  }
```

### 추이성

추이성의 경우 첫 번째 객체와 두번 째 객체가 같고, 두번째 세번째 객체가 같다면, 첫번째 객체와 세번째 객체 또한 같아서 모두 같아야 한다는 것이다. 쉬워보이지만 자칫하면 어기기 쉽다. 

상위 클래스에서 하위클래스를 생성하는데 상위클래스에 없는 새로운 필드를 하위 클래스에 추가하는 상황을 생각해보자. equals 비교에 영향을 주는 정보를 추가하는 상황이다.

아래의 예를 보자

```java
public class Point{
  private final int x;
	private final int y;
	
  public Point(int x, int y){
    this.x = x;
    this.y = y;
  }
  
  @Override
  public boolean equals(Object o){
    if (!(o instanceof Point)){
      return false;
    }
    Point p = (Point)o;
    return p.x == x && p.y == y;
  }
  // 나머지 생략...
}
```

자 위의 `Point` 클래스에 `color`를 포함하여 새로운 클래스를 만들어 보자.

```java
public class ColorPoint extends Point{
  private final Color color;
	
  public ColorPoint(int x, int y, Color color){
    super(x,y);
    this.color = color;
  }
  // 나머지 생략...
}
```

`equals` 메서드는 어떻게 추가하여야 할까? 그대로 둔다면 `Point`의 구현이 상속되어 색상 정보는 무시한 채 비교를 수행하게 된다. 

이것은 규약은 어긴 것이 아니지만 중요한 정보를 놓치게 된다. 다음 코드와 같이 비교 대상이 또 다른 `ColorPoint`이고 위치 색상이 같을 때만 `true`를 반환하는 것을 생각해보자.

```java
//대칭성을 위배하는 잘못된 코드
@Override
public boolean equals(Object o) {
  if (!(o instanceof ColorPoint))
    return false;
  return super.equals(o) && ((ColorPoint) o).color == color;
}
```

자 이제 이것을 보면 `Point`와 `ColorPoint`를 비교한 결과와 그 반대의 값이 다르게 나올 것이라고 예상이 된다. 

예시를 통해 확인해보자.

```java
Point p = new Point(1 , 2);
ColorPoint cp = new ColorPoint(1, 2 , Color.BLUE);
```

`p.equals(cp)`는 `true`를 반환하지만 `cp.equals(p)`는 `false`를 반환한다. 이건 명백히 대칭성을 위배한다.

이것을 해결하기 위해 아래와 같이 `ColorPoint`에서 `Point`와 비교시 `color`를 무시하고 `equals`를 적용해보자

```java
// 추이성을 위배하는 코드
@Override
public boolean equals(Object o) {
  if (!(o instanceof Point))
    return false;
  // Point의 경우 -> 색상 무시
  if (!(o instanceof ColorPoint))
    return o.equals(this)
    
  return super.equals(o) && ((ColorPoint) o).color == color;
}
```

이 방식은 대칭성은 지켜주지만 추이성을 어기게 된다.

```java
ColorPoint p1 = new ColorPoint(1, 2 , Color.RED);
Point p2 = new Point(1 , 2);
ColorPoint p3 = new ColorPoint(1, 2 , Color.BLUE);
```

위 코드는 `p1.equals(p2)`와 `p2.equals(p3)`는 `true`를 반환하지만, `p1.equals(p3)`가 `false`를 반환한다.

이는 명백히 추이성을 위반한 것이다.

또한 위와 같은 방식으로 다른 `ExtraPoint`라는 `Point`의 하위 클래스를 구현했다고 해보자. 그런 다음 `myColorPoint.equals(myExtraPoint)`를 부르게 되면 무한 재귀에 걸리어 `StackOverflowError`를 일으키게 될 것이다.

사실 이문제는 모든 객체 지향 언어의 동치 관계에서 나타나는 근본적인 문제이다. **구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재하지 않는다.** 객체 지향적 추상화의 이점을 포기하지 않는 한은 말이다.

하지만 이것을 `equals` 안의 `instanceof` 검사를 `getClass` 검사로 바꿔서 규약도 지키고 구체클래스를 상속할 수 있다는 것처럼 생각할 수 있다. 하지만 이것은 리스코프의 치환원칙에 따르면, 어떤 타입에 있어서 중요한 속성이라면 그 하위 타입에서도 마찬가지로 중요하기 때문에 그 타입의 모든 메서드가 하위 타입에서도 잘 작동하여야 한다는 객체지향의 원칙을 위배하는 코드를 만들게 된다.

따라서 구체 클래스의 하위 클래스에 값을 추가할 방법은 없지만 우회적으로 사용할 수 있는 방법이 하나 있다. **그것은 상속대신 컴포지션을 사용하는 것이다.** `Point`를 상속하는 대신 `Point`에 `ColorPoint`의 `private` 필드로 두고, `ColorPoint`와 같은 위치의 일반 `Point`를 반환하는 뷰 메서드를 `public`으로 추가하는 것이다.

```java
public class ColorPoint{
  private final Point point;
  private final Color color;
	
  public ColorPoint(int x, int y, Color color){
    point = new Point(x, y);
    this.color = Objects.requireNonNull(color);
  }
  public Point asPoint(){
    return point;
  }
  
  @Override
  public boolean equals(Object o){
    if (!(o instanceof Point)){
      return false;
    }
    ColorPoint cp = (ColorPoint)o;
    return cp.asPoint.equals(point) && cp.color.equals(color);
  }
  // 나머지 생략...
}
```

참고 - 만약 추상 클래스의 하위 클래스에서라면 `equals` 규약을 지키면서도 값을 추가할 수 있다. 예를 들면 `Shape` 추상클래스를 두고 그 아래 `radious` 필드를 가진 `Circle`과 `length`와 `width`를 추가한 `Rectangle` 클래스를 만들 수 있다. 상위 클래스를 직접 인스턴스로 만드는 게 불가능하다면 위의 문제는 일어나지 않는다.

### 일관성

**일관성**은 두 객체가 같다면(어느 하나라도 객체가 수정되지 않는 한) 영원히 같아야한다는 뜻이다.

가변 객체의 경우는 비교 시점에 따라 서로 다를 수도 혹은 같을 수도 있는 반면, 불변 객체는 한번 다르면 끝까지 달라야 한다. 

따라서 불변클래스로 만들기로 했다면 `equals`는 한번 다르면 영원히 다르다고 하고 한번 같으면 영원히 같다고 답하도록 해야한다.

또한 불변 클래스이던 가변 클래스이던 `equals`의 판단에 신뢰할 수 없는 자원을 추가하도록 해서는 안된다. 예를 들어 `java.net.URL`의 `equals`는 URL과 맵핑된 호스트의 IP주소를 이용하여 비교한다. IP 주소는 항상 같은 값을 내지 않기 때문에 문제가 일어난다. 따라서 `equals`의 연산은 항상 메모리에 존재하는 결정적인 계산만을 수행하여야 한다.



### Null-아님

`null` 아님은 간단하게 말 그대로 모든 객체가 `null`과 같지 않아야 한다는 뜻이다. 의도하지 않았음에도 `o.equals(null)`이 `true`를 반환하는 상황은 어렵지만 `NullPointerException`을 내는 코드는 흔하다. 따라서 이것을 방지하기 위해 아래와 같이 하자.

```java
@Override
public boolean equals(Object o){
  if (!(o instanceof MyType)){
    return false;
  }
  MyType my = (MyType) o
}
```

위와 같은 코드로 타입이 다른 것이 주어진 경우 `ClassCastException`을 던지는 것을 예방할 수 있고, `instanceof`는 첫번 째 피연산자가 `null`인 경우 `false`를 반환하고 따라서 `NullPointerException`을 예방할 수 있다.



## Equals를 구현하는 과정

### 과정

1. `==` 연산자를 사용해 입렵이 자기 자신의 참조인지 확인한다. (반사성)
2. `instanceof` 연산자로 입력이 올바른 타입인지 확인한다. -> 만약 인터페이스를 구현한 클래스라면 인터페이스와 비교
3. 입력을 올바른 타입으로 형변환 한다.
4. 입력 객체와 자기 자신의 대응되는 '핵심' 필드들이 모두 일치하는지 하나씩 전부 검사한다.

`Equals`를 구현한 뒤 3가지만 자문해보자. **대칭적인가? 추이성이 있는가? 일관적인가?** 

자문에서 끝내지 말고 단위 테스트를 통해 반드시 확인하자. 

### 주의사항

- `equals`를 재정의할 땐 `hascode`도 반드시 재정의 하자
- 너무 복잡하게 해결하려 들지 말자-> 필드의 동치성만 확인해도 되는 경우가 많다
- `Object`외의 타입을 매개변수로 받는 `equals` 메서드는 선언하지 말자

### 추가

- 구글의 **AutoValue** 프레임워크를 이용화 하여 `equals`를 작성하고 테스트하는 코드를 짜는 것을 간단하게 할 수 있다.


