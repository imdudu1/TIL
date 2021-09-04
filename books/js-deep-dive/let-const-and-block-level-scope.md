# let, const 키워드와 블록 레벨 스코프

## var 키워드로 선언한 변수의 문제점

### 중복 선언 허용

```javascript
var x = 1;
var y = 1;

var x = 100;
var y;

console.log(x); // 100
console.log(y); // 1
```

var는 중복 선언을 허용하기 때문에 위 코드와 같이 동일한 식별자를 가지는 변수를 여러번 생성 가능하고 초기값을 주지 않으면 undefined가 아닌 먼저 선언된 값을 그대로 사용하게 된다. 그래서 만약 두 변수 선언이 코드상 떨어진 곳에 위치하게 되면 개발자는 코드의 흐름을 쉽게 예측하지 못해 버그가 발생하기 쉽다.

### 함수 레벨 스코프

var로 선언한 변수는 오로지 함수의 코드 블록만 지역 스코프로 인정한다. 그 외에 모든 스코프는 전역 변수로 인정된다.

```javascript
var x = 1;
if (true) {
  var x = 10;
}
console.log(x); // 10
```

if 내부 코드 블록에서 중복 선언이 이뤄지면서 외부의 x가 덮혀쓰인 경우로 결과는 10이 출력된다.

### 변수 호이스팅

변수 호이스팅은 자바스크립트 엔진에 의해 변수 선언이 먼저 실행되어 변수 선언 행이 실행되기 전에 변수를 사용할 수 있도록 동작하게 된다. 변수 참조에 에러를 발생시키지는 않지만 프로그램의 흐름에 맞지 않고 가독성을 떨어뜨려 오류 발생의 여지를 남길 수 있다.

## let

### 중복 선언 금지

앞서 언급한 var와 달리 let은 중복 선언을 허용하지 않는다.

```javascript
let bar = 123;
let bar = 456; // Uncaught SyntaxError: Identifier 'bar' has already been declared
```

### 블록 레벨 스코프

var의 경우 함수 코드 블록만을 지역 스코프로 인정한 반면에 let의 경우 모든 코드 블록을 지역 스코프로 인정하는 블록 레벨 스코프를 따른다.

```javascript
let foo = 1;
{
  let foo = 2;
  let bar = 3;
}
console.log(foo); // 1
console.log(bar); // ReferenceError: bar is not defined
```

### 변수 호이스팅

let은 호이스팅이 발생하지 않는 것처럼 동작한다.

```javascript
console.log(foo);
let foo;
```

var의 호이스팅이 과정은 자바스크립트 엔진에 의해 암묵적으로 "선언"과 "초기화"를 한번에 진행한다. 따라서 접근은 할 수 있지만 undefined로 초기화된 상태이다. 하지만 let은 "선언"와 "초기화"를 분리되어 진행된다. "선언"은 이뤄졌지만 "초기화"는 변수 선언문에 도달했을 때 실행된다. 따라서 let의 호이스팅의 경우 참조 에러가 발생할 수 있다. 이렇게 실행 컨텍스트의 시작부터 let의 선언문까지를 **일시적 사각지대**라고 한다.

```javascript
let foo = 1;
{
  console.log(foo);
  let foo = 2;
}
```

그렇다면 단순히 호이스팅이 안일어나서 참조 에러가 발생한게 아닌가라는 의문이 생길 수 있다. 하지만 위 코드에서 호이스팅이 안일어났다면 console.log는 외부 foo를 참조해 1을 출력해야하지만 *참조 에러가 발생*하게 된다.

### 전역 객체와 let

var 키워드로 선언한 전역 변수, 전역 함수, 선언하지 않은 변수에 할당한 암묵적 전역은 전역 객체 window의 프로퍼티가 된다.

```javascript
var x = 1;
y = 2;
function foo() {}
```

하지만 let 키워드로 선언한 전역 변수는 window의 프로퍼티가 아니다. 보이지 않는 개념적인 블록 내에 존재하게 된다.

```javascript
let x = 1;
console.log(window.x); // undefined
console.log(x); // 1
```

## const 키워드

### 선언과 초기화

const 키워드로 선언한 변수는 선언과 동시에 초기화가 이뤄져야 한다.

```javascript
const foo = 1;
const bar; // Uncaught SyntaxError: Missing initializer in const declaration
```

### 재할당 금지

const 키워드로 선언된 변수는 재할당이 불가능하다.

### 상수

const 키워드로 선언한 변수에 원시 값을 할당한 경우 변수 값을 변경할 수 없다. 상수는 원시 값에 대한 의미를 표현할 수 있어 가독성과 유지보수에 적극 활용된다.

### const 키워드와 객체

const 키워드는 변경할 수 없다고 했다. 하지만 const로 선언된 변수가 변경이 불가능한 것이지 변수가 가리키고 있는 값에 대한 불변성을 의미하는 것은 아니다. 따라서 객체를 가지는 const 변수의 경우 객체 내부의 프로퍼티에 대해선 변경이 가능하다.
