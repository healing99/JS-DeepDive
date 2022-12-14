<img src="https://user-images.githubusercontent.com/49135797/192137065-5bbde021-3aed-4dd0-8573-f15eaf9f4f5a.png"  />

## 23.1 소스코드의 타입

ECMAScript 사양은 소스코드를 4가지 타입으로 구분한다. <br/>
소스코드의 타입에 따라 실행 컨텍스트를 생성하는 과정과 관리 내용이 다르기 때문이다.

1. 전역 코드 <br/>
   전역 코드는 전역 변수를 관리하기 위해 최상위 스코프인 전역 스코프를 생성해야한다. <br/>
   그리고 var 키워드를 선언된 전역 변수와 함수 선언문으로 정의된 전역 함수를 전역 객체의 프로퍼티와 메서드로 바인딩하고 참조하기 위해 전역 객체와 연결되어야한다. <br/>
   이를 위해 전역 코드가 평가되면 전역 실행 컨텍스트가 생성된다.

2. 함수 코드<br/>
   함수 코드는 지역 스코프를 생성하고 지역 변수, 매개변수, arguments 객체를 관리해야한다.<br/>
   그리고 생성한 지역 스코프를 전역 스코프에서 시작하는 스코프 체인의 일원으로 연결해야한다.<br/>
   이를 위해 함수 코드가 평가되면 함수 실행 컨텍스트가 생성된다.

3. eval 코드<br/>
   eval 코드는 strict mode(엄격 모드)에서 자신만의 독자적인 스코프를 생성한다.<br/>
   이를 위해 eval 코드가 평가되면 eval 실행 컨텍스트가 생성된다.

4. 모듈 코드<br/>
   모듈 코드는 모듈별로 독립적인 모듈 스코프를 생성한다. 이를 위해 모듈 코드가 평가되면 모듈 실행 컨텍스트가 생성된다.

## 23.2 소스코드의 평가와 실행

자바스크립트 엔진은 소스코드를 2개의 과정, 즉 "소스코드의 평가"와 "소스코드의 실행" 과정으로 나누어 처리한다.<br/>

소스코드 평가 과정에서는 실행 컨텍스트를 생성하고 변수, 함수 등의 선언문만 먼저 실행하여<br/>
생성된 변수나 함수 식별자를 키로 실행 컨텍스트가 관리하는 스코프(렉시컬 환경의 환경 레코드)에 등록한다.

평가 과정이 끝나면 비로소 선언문을 제외한 소스코드가 순차적으로 실행되기 시작한다. 즉, 런타임이 시작된다.<br/>
이때 소스코드 실행에 필요한 정보, 즉 변수나 함수의 참조를 실행 컨텍스트가 관리하는 스코프에서 검색해서 취득한다.<br/>
그리고 변수 값의 변경 등 소스코드의 실행 결과는 다시 실행 컨텍스트가 관리하는 스코프에 등록된다.

```ts
var x;
x = 1;
```

자바스크립트 엔진은 위의 코드를 2개의 과정으로 나누어 처리한다.

- 소스코드 평가 과정: 변수 선언문 `var x;`를 먼저 실행하고, <br/>
  이때 생성된 변수 식별자 x는 실행 컨텍스트가 관리하는 스코프에 등록되고 undefined로 초기화된다.

- 소스코드 실행 과정: 변수 할당문 `x = 1;`만 실행된다.<br/>
  이때 x 변수에 값을 할당하려면 먼저 x 변수가 선언된 변수인지 확인해야한다.<br/>
  이를 위해 실행 컨텍스트가 관리하는 스코프에 x 변수가 등록되어 있는지 확인한다.<br/>
  만약 x 변수가 실행 컨텍스트가 관리하는 스코프에 등록되어 있다면 x 변수는 선언된 변수, 즉 소스코드 평가 과정에서 선언문이 실행되어 등록된 변수이다.<br/>
  x 변수가 선언된 변수라면 값을 할당하고 할당 결과를 실행 컨텍스트에 등록하여 관리한다.

## 23.3 실행 컨텍스트의 역할

```ts
// 전역 변수 선언
const x = 1;
const y = 2;

// 함수 정의
function foo(a) {
  // 지역 변수 선언
  const x = 10;
  const y = 20;

  // 메서드 호출
  console.log(a + x + y); // 130
}

// 함수 호출
foo(100);

// 메서드 호출
console.log(x + y); // 3
```

전역 코드 평가 > 전역 코드 실행 > 함수 코드 평가 > 함수 코드 실행

함수 호출이 종료되면 함수 호출 이전으로 되돌아가기 위해 현재 실행 중인 코드와 이전에 실행하던 코드를 구분하여 관리해야 한다.<br/>
이처럼 코드가 실행되려면 다음과 같이 스코프, 식별자, 코드 실행 순서 등의 관리가 필요하다.

- 선언에 의해 생성된 모든 식별자(변수, 함수, 클래스 등)를 스코프를 구분하여 등록하고<br/>
  상태 변화(식별자에 바인딩된 값의 변화)를 지속적으로 관리할 수 있어야한다.

- 스코프는 중첩 관계에 의해 스코프 체인을 형성해야 한다.<br/>
  즉, 스코프 체인을 통해 상위 스코프로 이동하며 식별자를 검색할 수 있어야한다.

- 현재 실행 중인 코드의 실행 순서를 변경(ex. 함수 호출에 의한 실행 순서 변경)할 수 있어야 하며 다시 되돌아갈 수도 있어야한다.

이 모든 것을 관리하는 것이 바로 실행 컨텍스트다.

> 실행 컨텍스트는 소스코드를 실행하는 데 필요한 환경을 제공하고 코드의 실행 결과를 실제로 관리하는 영역이다. <br/>
> 실행 컨텍스트는 식별자(변수, 함수, 클래스 등의 이름)를 등록하고 관리하는 스코프와 코드 실행 순서 관리를 구현한 내부 메커니즘으로, <br/>
> 모든 코드는 실행 컨텍스트를 통해 실행되고 관리된다.

식별자와 스코프는 실행 컨텍스트의 렉시컬 환경으로 관리하고 코드 실행 순서는 실행 컨텍스트 스택으로 관리한다.

## 23.4 실행 컨텍스트 스택

```ts
const x = 1;

function foo() {
  const y = 2;

  function bar() {
    const z = 3;
    console.log(x + y + z);
  }
  bar();
}

foo(); // 6
```

위 예제는 전역 코드와 함수 코드로 이루어져 있다.<br/>
자바스크립트 엔진은 먼저 전역 코드를 평가하여 전역 실행 컨텍스트를 생성한다.<br/>
그리고 함수가 호출되면 함수 코드를 평가하여 함수 실행 컨텍스트를 생성한다.

이때 생성된 실행 컨텍스트는 스택 자료구조로 관리된다. 이를 **실행 컨텍스트 스택**이라고 부른다.<br/>
코드가 실행되는 시간의 흐름에 따라 실행 컨텍스트 스택에는 실행 컨텍스트가 추가(push)되고, 제거(pop)된다.

```
도서 p.365 참고
```

이처럼 **실행 컨텍스트 스택은 코드의 실행 순서를 관리한다.**<br/>
소스코드가 평가되면 실행 컨텍스트가 생성되고 실행 컨텍스트 스택의 최상위에 쌓인다.<br/>
실행 컨텍스트 스택의 최상위에 존재하는 실행 컨텍스트는 언제나 현재 실행 중인 코드의 실행 컨텍스트이다.<br/>
따라서 실행 컨텍스트 스택의 최상위에 존재하는 실행 컨텍스트를 **실행 중인 실행 컨텍스트**라고 부른다.

## 23.5 렉시컬 환경

렉시컬 환경은 **식별자와 식별자에 바인딩된 값, 그리고 상위 스코프에 대한 참조를 기록하는 자료구조로 실행 컨텍스트를 구성하는 컴포넌트다.**<br/>
실행 컨텍스트 스택이 코드의 실행 순서를 관리한다면, **렉시컬 환경은 스코프와 식별자를 관리한다.**

렉시컬 환경은 키와 값을 갖는 객체 형태의 스코프(전역, 함수, 블록 스코프)를 생성하여 식별자를 키로 등록하고 식별자에 바인딩된 값을 관리한다.<br/>
즉, 렉시컬 환경은 스코프를 구분하여 식별자를 등록하고 관리하는 저장소 역할을 하는 렉시컬 스코프의 실체다.

**실행 컨텍스트는 LexicalEnvironment 컴포넌트와 VariableEnvironment 컴포넌트로 구성된다.**<br/>
생성 초기에 LexicalEnvironment와 VariableEnvironment 컴포넌트는 하나의 동일한 렉시컬 환경을 참조한다.<br/>
이후 몇 가지 상황을 만나면 VariableEnvironment 컴포넌트를 위한 새로운 렉시컬 환경을 생성하고,<br/>
이때부터 VariableEnvironment와 LexicalEnvironment 컴포넌트는 내용이 달라지는 경우도 있다.

**렉시컬 환경은 EnvironmentRecord와 OuterLexicalEnvironmentReference 컴포넌트로 구성된다**

- 환경 레코드 (EnvironmentRecord)<br/>
  스코프에 포함된 식별자를 등록하고 식별자에 바인딩된 값을 관리하는 저장소다.<br/>
  환경 레코드는 소스코드의 타입에 따라 관리하는 내용에 차이가 있다.

- 외부 렉시컬 환경에 대한 참조 (OuterLexicalEnvironmentReference)<br/>
  외부 렉시컬 환경에 대한 참조는 상위 스코프를 가리킨다.<br/>
  이때 상위 스코프란, 외부 렉시컬 환경, 즉 해당 실행 컨텍스트를 생성한 소스코드를 포함하는 상위 코드의 렉시컬 환경을 말한다.<br/>
  외부 렉시컬 환경에 대한 참조를 통해 단방향 링크드 리스트인 스코프 체인을 구현한다.
