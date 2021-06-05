---
title: '[Golang] Go template package'
date: 2021-06-02 22:06:72
category: golang
thumbnail: { thumbnailSrc }
draft: false
---

## 목표

Go template 패키지에 대해서 알아보자



이 글은 [go text/template 패키지](https://golang.org/pkg/text/template/)를 기본으로 하여 작성하였습니다.

## Template

템플릿이란 특정 파일이나 문서의 포맷을 미리 정해놓은 틀이다. 따라서 템플릿을 통해서 특정 규격(형식)을 정해 놓고 주어지는 특정 값에 따라서 문서와 파일의 내용을 추가하거나 변환 할 수 있다. 

### Go Template 패키지

 Go Templates 패키지는 파일은 템플릿 형식에 맞춰서 만들어준다. 템플릿 형식은 Go template에서 요구하는 특정 문법에 따라서 작성해야한다. 다양한 생성 파일 형식을 만들 수 있고, `html/template`를 이용하면 `text/template`에서  자동적으로 특정 공격에 대한 보안을 적용한 html을 만들어 준다. 

 간단하게 쓰이는 정적인 웹페이지를 제공하는 경우에서부터 go로 작성된 여러 오픈소스에서 다양하게 사용하고 있다. 예를 들면, Helm의 template은 go template를 기본으로 하여 여러 함수와 추가적인 기능으로 만들어졌고, 프로메테우스의 alertmanager에서도 알림을 전송하는 경우의 상황에 맞는 go template를 통해 알림 형식을 템플릿화하여 알림을 제공할 수 있다.

자세한 사용 방법은 아래에서 살펴보도록 하자

다음과 같은 `Employee` 객체가 있다고 생각하자

```go
type Employee struct {
	Name 	string
  	Age	 	uint
}
```

위의 객체를 go template에 적용하여서 이 객체의 필드들이 삽입 되는 상황을 보자

Go template 파일 안에서 다른 정적 컨텐츠와 위 객체의 필드와 같이 주입 되는 값을 `{{ }}`라는 구분자를 통해서 구분하며 위의 필드 값을 `{{.Name}}` `{{.Age}}` 와 같이 나타낼 수 있으며, 이렇게 구분자로 **다른 정적인 값과 구분되는 주어진 변화할 수 있는 값** or **실행 흐름을 컨트롤 하는 구문**을 `Actions`라고 부른다.

실제 간단하게 템플릿 코드를 작성해보자

```go
package main

import (
	"fmt"
	"os"
	"text/template"
)

type Employee struct {
	Name string
	Age  uint
}

const tmplString = `My name is {{.Name}} and {{.Age}} years old`

func main() {

	employee := Employee{"john", 30}
	tmpl, err := template.New("test").Parse(tmplString)
	if err != nil { fmt.Errorf("template parse error %v", err)}
	err = tmpl.Execute(os.Stdout, employee)
	if err != nil {fmt.Errorf("template execute error %v", err)}
}

//출력: My name is john and 30 years old

```

`template.New("test").Parse(tmplString)`에서 통해 실제 스트링을 가져와서 템플릿을 만들어주고

`tmpl.Execute(os.Stdout, employee)`에서 콘솔로 실제 `employee` 객체의 값을 주입해 필드 값들을 템플릿에서 사용하도록 해준다.
### 공백 처리

디폴트로 위에서 `tmplString`에서 보이듯이 공백이 그대로 유지되어 출력된다. 

Actions에서 `{{` 뒤에 뛰어쓰기 없이 바로 `-` 를 붙이고 필드 값과 공백을 사이에 두개 된다면 왼쪽 공백이 trim 된다. 마찬가지로 오른쪽 `}}` 앞에 `-` 를 붙이는 경우도 오른쪽 공백이 trim 된다. 

여기서 만약 내부 레퍼런스와 `-` 가 값과 붙어있으며 이것을 `-` 공백을 트림하는 것으로 인식하지 않고 **실제 마이너스 부호 or 변수 로 인식**하기 때문에 사이에 공백을 두어 명확히 알려주어야한다.

아래 예제를 확인하자

```go
const parseString1 = `My name is {{.Name -}} and {{.Age}} years old`
//출력: My name is johnand 30 years old
const parseString2 = `My name is {{- .Name}} and {{.Age}} years old`
//출력: My name isjohn and 30 years old
const parseString3 = `My name is {{- .Name}} and {{-3}} years old` 
//출력: My name isjohn and -3 years old 
// -3으로 인식 
```



## Actions

Action은 위에서 언급한 것처럼 **다른 정적인 값과 구분되는 주어진 변화할 수 있는 값** or **실행 흐름을 컨트롤 하는 구문** 을 얘기하며 `{{}}` 로 구분 된다.

Action 안에 들어갈 수 있는 구문은 다음과 같다. 내부에 들어가는 값으로 `pipeline` 과 `argument`이라는 것은 일종의 데이터의 값으로 정확한 정의는 아래서 설명할 것이며, 일단 특정 과정을 통해 나오는 값이라고 생각하면 된다.  

- 조건문 (if-else)
- 반복문 (range)
- define , template 
- with 

### 조건문

기존 조건문과 매우 유사하다. `T1`은 실행되는 템플릿문을 의미한다. 
```go
 // pipeline의 값이 비어있지 않으면 T1, 아니면 지나친다
{{if pipeline}} T1 {{end}}

 // pipeline의 값이 비어있지 않으면 T1, 아니면 T2 실행
{{if pipeline}} T1 {{else}} T2 {{end}}

// pipeline1의 값이 비어있지 않으면 T1, 아니면 pipelin2를 확인하여 비어있지 않으면 T2 실행
{{if pipeline1}} T1 {{else if pipeline2}} T2 {{end}}

```

### 반복문

range를 통한 반복문에서는 pipeline이 반드시 배열, 슬라이스, 맵과 같이 순환할 수 있어야한다.

 ```go
//주어진 pipeline의 길이가 0이 아니라면 각각의 값에 대해서 T1을 실행
{{range pipeline}} T1 {{end}}

//주어진 pipeline의 길이가 0이 아니라면 각각의 값에 대해서 T1을 실행, 0이라면 T2 실행
{{range pipeline}} T1 {{else}} T2 {{end}}
 ```



### define , template 

```go
//define 이라는 것을 통해 T1을 정의
{{define "something"}} T1 {{end}}

//template을 이용하여 정의된 값을 사용
//여기에서 주어지는 pipeline의 값을 T1의 dot(.)으로 접근하는 값이 주어진다.
//nil인 경우 nil data에 의해 정의된다.
{{template "something" pipeline}}

```



### with

위에서 define과 template처럼 여러 번 사용하는 게 아닌 경우 사용한다

```go
//pipeline에서 주어진 값이 T1의 dot(.)으로 접근하는 값으로 주어진다.
{{with pipeline}} T1 {{end}}
```



## Arguments

go 템플릿에서 argument 값은 단순한 값으로 많은 경우를 포함하지만 boolean, string, integer.. 와 같은 기본 값을 포함하여 nil, 아래에서 얘기하는 variable 값, dot(period)로 `.Fields(구조체)`, `.Key(map)` 구조체나 맵 자료 구조의 값을 나타내거나 구조체에서 여러 depth를 들어가는 방식인 `.Field1.Field2`와 같은 방식도 가능하다. 

또한 구조체에서 한개 혹은 두개(두번째 반환 값은 반드시 `error`)를 사용하는 메서드의 경우도 .Method1과 같은 방식으로 arguments로 사용 가능하다.

go template에서 기본으로 정의되어있는 함수(and, len, print, println)과 같은 것을 fun로 사용해 argument로 하여 사용 가능하다. 실제 함수를 템플릿에서 만들어서 사용하는 것 또한 가능하다. 이것에 대해서는 자세히 설명하지 않겠다. [공식문서](https://golang.org/pkg/text/template/#hdr-Functions)를 참조하자 



## Pipelines

CLI에서 사용되는 pipeline command(`|`) 를 동일하게 사용하며 적용되는 방식 또한 유사하다. 앞에서 나온 커맨드의 결과 값을  `|`  뒤에 있는 곳에 체인닝하는 방식이다.  `|` 로 구분되는 곳마다 오는 커맨드로는 Argument, .Method, function이 모두 올 수있다. 가장 마지막에 나오는 결과 값이 pipeline의 결과 값이 된다.

## Variables

go에서 적용되는 문법과 비슷하다 하지만 변수는 쉘스크립트와 비슷하게 `$` 로 구분한다.

```go
$variable := pipeline // pipeline에서 나오는 값을 집어 넣는다
$variable = pipeline // 이미 정의된 값이면 새로 할당해주는 방식
range $index, $element := pipeline // go에서 range문과 비슷하게 순환가능한 pipeline 값을 순회
```



## 마무리

Go template 패키지에 대해서 간단하게 알아보고 읽는 방식 및 용어를 알아 보았다.

실제 [template 패키지](https://golang.org/pkg/text/template/#example_Template_func)에서 제공하는 함수는 위 설명한 것보다 다양하게 더 복잡한 상황에서도 적용할 수 있다. 

### 참고

https://golang.org/pkg/text/template

https://golang.org/pkg/html/template/

https://helm.sh/docs/chart_template_guide/functions_and_pipelines/