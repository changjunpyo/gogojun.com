---
title: "[Java] 3. 연산자 & 제어문"
date: 2021-04-14 11:04:40
category: java
thumbnail: { thumbnailSrc }
draft: false
---

### 기본 용어

**연산자(Operator)**: 어떠한 기능(연산)을 수행하는 기호(+, - , *, / 등)

**피연산자(Operand)**: 연산자의 작업이 되는 대상(변수, 상수, 리터럴, 수식)

**연산식(Expressions)**: 연산자와 피연산자로 연산을 기술

## 연산자

| 연산자 종류 | 연산자                                                 | 피연산자 수 | 결과값       |
| ----------- | ------------------------------------------------------ | ----------- | ------------ |
| 산술        | +, -, *, /, %                                          | 이항        | 숫자         |
| 부호        | +, -                                                   | 단항        | 숫자         |
| 문자열      | +                                                      | 이항        | 문자열       |
| 대입        | =, +=, -=, *=, /=, %=, &=<br />^=, \|=, <<=, >>=, >>>= | 이항        | 타입별       |
| 증감        | ++, --                                                 | 단항        | 숫자         |
| 비교(관계)  | ==, !=, >, <, >=, <=<br />instanceof                   | 이항        | boolean      |
| 논리        | !, &, \|, &&, \|\|                                     | 단항, 이항  | boolean      |
| 조건        | (조건문) ? A : B                                       | 삼항        | 다양         |
| 비트        | ~, &, \|, ^                                            | 단항, 이항  | 숫자, bolean |
| 쉬프트      | >>, <<, >>>                                            | 이항        | 숫자         |

(참고: https://blog.naver.com/hsm622/222150928707)

## 산술 연산자

**산술 연산자(Arithmetic Operators)**는 일반적으로 이야기하는 사칙 연산의 덧셈, 뺄셈, 곱셉, 나눗셈과 거의 유사하다. 여기에 나머지 연산이 추가된 다섯가지의 연산을 말한다.

**쉬프트 연산**(>>, <<, >>>)도 일반적으로 숫자를 2배 곱하거나 나누는 방식으로 진행되기 때문에 산술 연산으로 보기도 한다.

문자열에서 `+` 연산은 내부적으로 String에서 연산자 오버로딩을 통해 문자열을 연결하며, 숫자와 더해져도 문자로 변경하여 문자를 만들어준다.(java는 유저가 연산자 오버로딩을 할 수 없음)  

#### 사칙 연산

```java
int a = 3;
int b = 7;
double c = 3.0;
System.out.println(a + b);

System.out.println(a - b);

System.out.println(a * b);

System.out.println(a / b);
System.out.println(a / 0); // 정수를 0으로 나누게 되면 ArithmeticException 발생
System.out.println(c / 0.0); // 실수를 0으로 나누면 NaN 혹은 Infinity가 결과로 나온다.

System.out.println(a % b);
System.out.println(a % 0); // 정수를 0으로 나머지 연산을 하게 되면 ArithmeticException 발생
System.out.println(c % 0.0); // 실수를 0으로 나머지 연산을 NaN가 결과로 나온다.
```

#### 쉬프트 연산

쉬프트 연산은 <<, >>, >>> 연산 세종류가 존재한다.

`<<` 연산의 경우 비트를 왼쪽으로  옮겨주게 되고, 왼쪽의 비트들은 사라진다. 새로 추가되는 오른쪽 비트들은 0으로 추가된다.

`>>` 연산의 경우 비트를 오른쪽으로  옮겨주게 되고, 오른쪽의 비트들은 잘려서 사라지게 된다. 왼쪽에 새로 추가되는 비트들은 음수의 경우는 1을 추가하고 양수의 경우는 0을 넣어주게 된다.

`>>>` 연산의 경우 비트를 오른쪽으로 옮겨주지만 `>>` 연산에서 부호를 신경써서 왼쪽에 1, 0을 넣어주지만 이것은 부호를 신경쓰지 않고 1을 넣어주게 된다.

주의 자기 자신의 타입 비트 수를 넘어가는 `a << b` 연산을 하게되면  `b` 대신  a << (b % bit)로 나눈 결과로 쉬프트를 해준다.

```java
// << 연산 
int a = 1 					// 00000000 00000000 00000000 00000001 
a = a << 1  				// 00000000 00000000 00000000 00000010

// int 에서는 MSB가 1이면 음수 
int b = -2147483648 // 10000000 00000000 00000000 00000000 
b = b << 1					// 00000000 00000000 00000000 00000000
  
// >> 연산 
int a = 2147483647	// 01111111 11111111 11111111 11111111
a >>= 1  						// 00111111 11111111 11111111 11111111 (1073741823)
int b = -2147483648 // 10000000 00000000 00000000 00000000 
b >>= 1							// 11000000 00000000 00000000 00000000 (-1073741824)
  
// >>> 연산 
int a = 2147483647	// 01111111 11111111 11111111 11111111
a >>>= 1  					// 00111111 11111111 11111111 11111111 (1073741823)
int b = -2147483648 // 10000000 00000000 00000000 00000000 
b >>>= 1						// 01000000 00000000 00000000 00000000 (1073741824)

```



## 비트 연산자

비트 연산은 피연산자를 비트 단위로 연산한다.

`~` 연산 : 비트에서 0 -> 1로 1-> 0으로 바꿔준다.

`|` (or) 연산 : 피연산자 중 한개라도 1이 있다면 1이다.

`&`(and) 연산 : 피연산자 두개 모두가 1인 경우만 1이다.

`^`(xor) 연산: 피연산자가 서로 다를 때만 1이다.

주어지는 입력 값에 따라 연산자의 결과를 보여주는 것을 진리표라고 한다.

아래의 진리표를 확인해보자

|  x   |  y   |  ~x  | x \| y | x & y | x ^ y |
| :--: | :--: | :--: | :----: | :---: | :---: |
|  0   |  0   |  1   |   0    |   0   |   0   |
|  0   |  1   |  1   |   1    |   0   |   1   |
|  1   |  0   |  0   |   1    |   0   |   1   |
|  1   |  1   |  0   |   1    |   1   |   0   |



## 관계 연산자

관계 연산자의 경우 관계 연산자를 중심으로 두 연산자의 어떤 관계를 가지고 있는지 확인하는 연산이다.

예를 들어 어떤 값이 큰지, 같은지, 작은지 등을 확인하는 연산자이다.



|   연산자   |                             설명                             |
| :--------: | :----------------------------------------------------------: |
|     ==     |             같은 값이면 true 아니면 false를 반환             |
|     !=     |             다른 값이면 true 아니면 false를 반환             |
|     >      |          왼쪽 값이 더 크면 true 아니면 false를 반환          |
|     >=     |     왼쪽 값이 더 크거나 같으면 true 아니면 false를 반환      |
|     <      |         오른쪽 값이 더 크면 true 아니면 false를 반환         |
|     <=     |    오른쪽 값이 더 크거나 같으면 true 아니면 false를 반환     |
| Instanceof | 왼쪽 참조 변수의 값이 오른쪽 참조 변수의 타입과 같으면 true 아니면 false를 반환 |

## 논리 연산자 

비트 연산과 유사한 방식을 가지고 있지만 피연산자의 대상이 비트 연산에서는 비트끼리 비교를 했다면 논리 연산에서는 boolean 끼리 비교하여 연산을 진행한다.

|   x   |   y   | x && y | x \|\| y |  !x   |
| :---: | :---: | :----: | :------: | :---: |
| false | false | false  |  false   | true  |
| false | true  | false  |   true   | true  |
| true  | false | false  |   true   | false |
| true  | true  |  true  |   true   | false |

`&&`와 `&`, `||` 과 `|` 의 차이는 무엇일까?

결과적인 값이 나오는 것은 똑같다. 

하지만 `&&`에서는 사실 효율적인 계산을 위해 왼쪽이 참이 아니면 결과가 무조건 거짓이기 때문에 오른쪽 항의 값을 찾아서 검사하지 않는게 효율적이다. 하지만 `&`을 이용하게 되면 오른쪽 항도 값을 찾아서 검사한다. 마찬가지로 `|`의 경우 왼쪽이 참이여도 오른쪽 항을 검사한다(비트 연산이기 때문에).

## 기타 연산자(assignment, 화살표, 3항 연산자)

### 대입(assignment) 연산자

대입 or 할당 연산자라고 부른다. 왼쪽에는 변수가, 오른쪽에는 리터럴 또는 리터럴이 담긴 변수를 받는다.

값을 초기화하는데 쓰인다. 또한 할당 연산자의 결과는 할당이 된 값을 보내준다.

+=, -=, %=, <<= 과 같이 다른 연산을 할당하려는 왼쪽 값에 해주고 할당해주는 방법이 있다.

 ```java
// 할당 연산
int num = 5; // 초기화

// 할당 연산은 할당된 값을 리턴
int a = num = 6; // num = 6 이 할당이 되면 a에 할당된 6의 값이 들어간다.

// += 
a += 5; // a = a + 5와 같은 표현식
 ```

#### 3항 연산자

3항 연산자는 `if ~ else` 문장을 하나의 연산자로 표현하여 조건에 따라 결과를 반환해줍니다.

사람마다 차이는 있지만 너무 자주 남발하여 하용하는 것은 가독성을 저해할 여지가 있습니다.

`조건식 ? 조건식이 참인 경우 : 조건식이 거짓인 경우`

```java
// 일반 메서드 
int min(int i, int j){
  return i < j ? i : j
}
```

위와 같이 min을 다음과 같이 한줄로 표현할 수 있는 장점이 있습니다.

### 화살표(->) 연산자

이 부분에서는 람다식을 다루는 부분에서 자세히 다룰 예정이므로 가볍게 설명하겠다. 

`->` 표현식은 JDK 8 이상에서 나왔다.

`->` 연산자(람다 표현식) 라는 것은 메서드(함수)를 하나의 식으로 표현하도록 하는 것이다.

람다 표현식은 아래와 같은 방식으로 작성할 수있다. 자세히 살펴보자

`(매개 변수 목록)` -> `{ 함수 몸체 }`

```java
// 일반 메서드 
int min(int i, int j){
  return i < j ? i : j
}
// 람다 표현식
(i, j) -> i < j ? i : j
```

자바에서 함수는 일급시민이므로 메소드의 매개변수도 가능하고, 메서드의 결과값으로 람다 표현식으로 반환될 수 있다.

**주의사항** 

1. 매개 변수 타입이 추론이 가능한 경우에만 타입 생략 가능
2. 매개 변수 하나인 경우 소괄호 생략 가능
3. 함수 몸체의 명령문이 하나인 경우 중괄호 생략가능 (; 또한 생략)
4. 함수 몸체가 하나의 리턴문으로 이루어진 경우는 중괄호 생략 불가
5. 리턴문 대신 표현식을 사용할 수 있는데 리턴 값으로 자동으로 표현식의 결과를 반환

## 연산자 우선 순위

| 순위 |                            연산자                            |          동작          | 연산 방향 |
| :--: | :----------------------------------------------------------: | :--------------------: | :-------: |
|  1   |                             `.`                              |     객체 멤버 접근     |    ->     |
|      |                           `[`, `]`                           |       배열 요소        |    ->     |
|      |                           `(arg)`                            |      메소드 호출       |    ->     |
|      |                         `i++`, `i--`                         |       후위 증감        |    ->     |
|  2   |                         `++i`, `--i`                         |       전위 증감        |    <-     |
|      |                           `+`, `-`                           |      단항 부호식       |    <-     |
|      |                           `~`, `!`                           |    비트 보수, 부정     |    <-     |
|  3   |                            `new`                             |       객체 생성        |    <-     |
|      |                           `(type)`                           |         캐스팅         |    <-     |
|  4   |                        `*`, `/`, `%`                         | 곱하기, 나누기, 나머지 |    ->     |
|  5   |                           `+`, `-`                           |      더하기, 빼기      |    ->     |
|      |                             `+`                              |      문자열 결합       |    ->     |
|  6   |                      `<<`, `>>`, `>>>`                       |      시프트 연산       |    ->     |
|  7   |                     `<`, `<=`, `>`, `>=`                     |  비교 연산(대소비교)   |    ->     |
|      |                         `instanceof`                         |       타입 비교        |    ->     |
|  8   |                          `==`, `!=`                          |       등호 비교        |    ->     |
|  9   |                             `&`                              |          AND           |    ->     |
|  10  |                             `^`                              |          XOR           |    ->     |
|  11  |                             `|`                              |           OR           |    ->     |
|  12  |                             `&&`                             |          AND           |    ->     |
|  13  |                             `||`                             |           OR           |    ->     |
|  14  |                            `? :`                             |       3항 연산자       |    <-     |
|  15  | `=`, `*=`, `/=`,`%=`, `+=`<br />, `-=`, `<<=`,`>>=`<br />,`>>>=`, `&=`, `^=`, `|=` |      대입 연산자       |    <-     |
|  16  |                             `->`                             |      람다 표현식       |    ->     |



## Java13 Switch 연산자

기존 자바에는 Switch문과 Case문으로 구성된 선택문이 있다.

```java
int x;
switch(i){
  case 1:
    x = 1;
    break;
  case 2:
    x = 2;
    break;
  case 3:
    x = 3;
    break;
  default:
    x = 4; 
}
```

Java 12에서 부터는 Switch 연산자를 단일 케이스문이 아닌 Multicase 문을 허용하였고 화살표 연산자를 허용

```java
int x = switch(i){
  case 1 -> 1; // 화살표를 통한 리턴
  case 2 -> 2; 
  case 3,4 -> 3; // 멀티 케이스 구문
}
```

java 13에서부터는 `return` 역할을 `yield` 키워드로 변경

```java
int x = switch(i){
  case 1: yield 1; 
  case 2: yield 2;
  case 3,4: yield 3;
}
```

## 제어문 

코드 실행 흐름은 상태, 조건에 따라서 제어되어여야 하며 그것이 반복적인 코드 실행일 수도, 조건에 따라 제어하거나, 분기를 통한 순서의 변경일 수도 있다. 따라서 자바에서 어떤 식으로 실행 흐름을 제어하는지, 어떻게 사용되는지 살펴보자.

## 선택문 

> 조건에 따라서 실행되는 코드를 선택하는 구문

#### if-then

`if-then` 구문은 가장 기본적인 제어문으로 대부분의 언어들이 다 가지고 있다. `if`문에서 조건을 만족하면 내부의 코드를 실행한다.

```java
if (조건문){
  실행 구문
}
```

####  if-then-else

`if` 문이 조건을 만족시키는 경우 실행되었다면 조건을 만족시키지 않는 모든 구문이 `else`문을 타게 된다.

```java
if (조건문){
  실행 구문
} else{
  //if 조건을 만족하지 않는 모든 상황에서 여기를 실행
  실행 구문
}
```

`else if` 라는 구문을 이용하여 `if` 문의 조건에 해당되지 않는 것들을 다시 조건으로 확인하여 선택할 수 있다.

```java
if (조건문1){
  // 조건 1 만족
  실행 구문
} else if (조건문2){
  // 조건 1 만족 X & 조건 2 만족
  실행 구문
} else if (조건문3){
  // 조건 1 만족 X & 조건 2 만족 X & 조건 3 만족
	실행 구문
} else{
  // 위 모든 조건 불만족
  실행 구문
}
```

#### 

#### Switch문

`switch` 구문은 모두 `if-then`, `if-then-else` 구문으로 표현 가능하지만 입력 변수에 대해서 어떤 case문을 실행 시키도록 할지 구분한다.

입력변수로 `byte, short, int ,long, enum` 유형과 `Byte, Short, Int` 및 `Long`과 같은 일부 래퍼 유형을 허용한다.

```java
switch(입력 변수){
  case 입력값1(리터럴만 가능):
    break; // break를 하지 않으면 break를 찾을 때까지 바로 아래 구문을 실행한다.
  case 입력값2
    break;
}
```

위와 같은 방식으로 사용 된다. 

자바의 버전이 올라감에 따라 더 추가된 기능들이 있는데, 자세한 내용은 위의 내용(Java13 Switch문)을 통해 확인할 수있다.



## 반복문

반복문이라는 것은 특정 명령들을 반복적으로 수행해야 할 때 사용한다.

대표적으로 `for`문과 `while`문이 존재하는데 하나로 다른 것을 대체할 수 있다. 간단한 

#### while문

대표적인 반복문으로 조건문이 참인 경우 계속 반복한다. 반복 횟수를 지정하지 않을 때 주로 쓰인다.

```java
while(조건문){
  실행
}
```

 

#### do-while문

`while`문과는 다르게 처음에 실행구문을 무조건 한번 실행하고 그 다음 조건을 비교하게 된다.

```java
do{
 실행구문 
} while(조건문); // 세미콜론이 들어간다 
```

#### for문

`while`문과는 다르게 조건뿐 아니라 초기화, 증가/감소하는 부분이 들어간다. 무조건 각 부분을 채워야 할 필요는 없지만 세미콜론으로 구분해주어야한다.

처음 `for`문에 들어가게 되면 

1. 초기화
2. 조건문
3. 실행 구문
4. 증가/ 감소
5. 2번

다음 과정을 통해 for문을 반복하다가 조건문이 false인 경우 반복문을 나오게 된다.

```java
for (초기화; 조건문; 증가/감소){
  실행 구문
}
```



#### for-each문

`for`문에서 추가적으로 자바 1.5버전 이후에 foreach문이 추가되었다. 물론 명령어는 for문을 이용하지만 다른 언어에서 foreach라는 용어를 사용하고있어 foreach문이라고 불린다.  

```java
for(type var : iterate){
  실행 구문
}
```

들어가는 `iterate`는 루프를 돌릴 수 있는 `Array` 형태나 `Collections`가 가능하고 `Iterable<E>`를 상속받은 객체라면 가능하다. 루프를 핸들링하거나 스텝을 지정해서 하는 경우는 불가능하기 때문에 순차적으로 사용하고자 할 때만 사용할 수있다.

## 분기문

#### continue

반복문에서만 쓰이는 분기문이다. `continue`를 만나게 되면 다음 스텝으로 넘어가고 조건문을 확인하는 순서부터 실행하게 된다.

다음스텝을 넘어간다는 의미는 `for`문의 증감에 영향을 받는다는 의미이다.

#### break

`break`의 경우는 반복문 / `switch`문에서 사용되는데 반복문에 경우는 만나는 순간 루프를 무조건 빠져나가게 되고, `switch`문에서도 빠져나가게 되지만 `break` 이후 인자를 주는 방법(switch문 참조) 또한 존재한다.  

#### return

리턴의 경우는 모든 메서드 어느 경우든 `return` 을 만나게 되면 메서드를 빠져나가게 되고 메서드가 전달하는 값을 `return` 이후로 값으로 전달하게 된다. 