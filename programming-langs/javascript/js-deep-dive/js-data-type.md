# 데이터 타입

## 데이터 타입

데이터 타입이란 데이터의 종류를 말한다. 자바스크립트 \(ES6\)에서는 원시 타입\(primitive type\)과 객체 타입\(object/reference type\)으로 총 7개의 데이터 타입을 제공한다.

* 원시 타입

| 데이터 타입 | 설명 |
| :---: | :---: |
| 숫자 타입 | 숫자 타입은 정수, 실수 구분 없이 하나의 숫자 타입만 존재한다. |
| 문자열 타입 | 문자열 |
| 불리언 타입 | 논리적인 참\(True\)와 거짓\(False\)를 나타낸다. |
| undefined 타입 | var 키워드로 선언된 변수에 암묵적으로 할당되는 값 |
| null 타입 | 값이 없다는 것을 의도적으로 명시 |
| 심벌 타입 | ES6에 추가된 7번째 타입 |

* 객체 타입
  * 객체, 함수, 배열 등

이처럼 개발자가 표현하려는 데이터의 타입을 표현하면 데이터의 생성한 목적과 용도가 분명해지고, 자바스크립트 엔진은 타입에 따라 적절한 메모리 공간을 할당하여 값을 취급할 수 있다.

### 숫자 타입

대부분의 언어에서는 정수와 실수를 구분하여 int, long, float, double과 같이 다양한 타입을 제공한다. 하지만 자바스크립트는 독특하게 하나의 숫자 타입만 존재한다.

ECMAScript 사양에 따르면 숫자 타입의 값은 배정밀도 64비트 부동소수점 형식을 따른다. 따라서 **모든 수를 실수로 처리**하며, 정수만을 표현하는 데이터 타입은 없다.

```javascript
// 모두 숫타 타입이다.
var integer = 10;
var double = 10.12;
var negative =-20;

// 표기법만 다를 뿐 모두 같은 값.
var binary = 0b01000001;
var octal = 0o101;
var hex = 0x41;

// 주의
console.log(1 === 1.0); // true
console.log(3 / 2); // 1.5
```

여기서 주의할 점은 앞서 말했듯이 자바스크립트에서는 정수 타입이 없어 정수끼리 나눠도 실수가 나올 수 있다. 또한 숫자 타입은 세 가지의 특정한 값을 표현하는데 다음과 같다.

* Infinity
* -Infinity
* NaN

`Infinity/-Infinity`는 각각 양의 무한대, 음의 무대한를 나타내며 읽기 전용의 전용 스코프 변수이다. 무한대를 나타내는 이 상태는 다음과 같은 성질을 가지고 있다.

* `POSITIVIE_INFINITY`를 포함한 아무 양의 수와 `POSITIVE_INFINITY`를 곱한 결과는 `POSITIVE_INFINITY`입니다.
* [`NEGATIVE_INFINITY`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Number/NEGATIVE_INFINITY)를 포함한 아무 음의 수와 `POSITIVE_INFINITY`를 곱한 결과는 [`NEGATIVE_INFINITY`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Number/NEGATIVE_INFINITY)입니다.
* 아무 양의 수를 `POSITIVE_INFINITY`로 나눈 결과는 0입니다.
* 아무 음의 수를 `POSITIVE_INFINITY`로 나눈 결과는 음의 부호를 가진 0입니다.
* 0을 `POSITIVE_INFINITY`로 나눈 결과는 [`NaN`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/NaN)입니다.
* [`NaN`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/NaN)에 `POSITIVE_INFINITY`를 곱한 결과는 [`NaN`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/NaN)입니다.
* `POSITIVE_INFINITY`를, [`NEGATIVE_INFINITY`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Number/NEGATIVE_INFINITY)를 제외한 아무 음의 수로 나눈 결과는 [`NEGATIVE_INFINITY`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Number/NEGATIVE_INFINITY)입니다.
* `POSITIVE_INFINITY`를, `POSITIVE_INFINITY`를 제외한 아무 양의 수로 나눈 결과는 `NEGATIVE_INFINITY`입니다.
* `POSITIVE_INFINITY`를 [`NEGATIVE_INFINITY`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Number/NEGATIVE_INFINITY) 또는 `POSITIVE_INFINITY`로 나눈 결과는 [`NaN`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/NaN)입니다.

다음으로 `NaN`은 Not-A-Number로 숫자가 아님을 의미한다. NaN이 반환하는 연산에는 다섯 가지 종류가 있다.

* 숫자로서 읽을 수 없음 \(`parseInt("어쩌구")`, `Number(undefined)`\)
* 결과가 허수인 수학 계산식 \(`Math.sqrt(-1)`\)
* 피연산자가 `NaN` \(`7 ** NaN`\)
* 정의할 수 없는 계산식 \(`0 * Infinity`\)
* 문자열을 포함하면서 덧셈이 아닌 계산식 \(`"가" / 3`\)

여기서 주의할 점은 NaN은 다른 모든 값과 비교\(`==`, `!=`, `===`, `!==`\)했을 때 같지 않으며 NaN의 판별은 [`Number.isNaN()`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Number/isNaN) 또는 [`isNaN()`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/isNaN)을 사용하면 제일 분명하게 수행할 수 있다. 또 하나 `isNaN`은 현재 값이 `NaN`이거나, 숫자로 변환했을 때 NaN이면 반환하지만, `Number.isNaN`은 현재 값이 `NaN`이어야 참이 반환된다.

```javascript
NaN === NaN;        // false
Number.NaN === NaN; // false
isNaN(NaN);         // true
isNaN(Number.NaN);  // true

isNaN('hello world'); // true
Number.isNaN('hello world'); // false

let arr = [2, 4, NaN, 12];
arr.indexOf(NaN);                      // -1 (false)
arr.includes(NaN);                     // true
arr.findIndex(n => Number.isNaN(n));   // 2
```

### 문자열 타입

문자열 타입은 0개 이상의 16비트 유니코드 문자의 집합으로 문자열 데이터를 나타내는 데 사용한다. 문자열 타입은 ```작은따옴표(''), 큰따옴표(""), 백틱(``)```으로 감싼 텍스트를 의미하며, 자바스크립트에서는 작은따옴표가 가장 일반적으로 사용된다.

```javascript
var string;
string = 'string';
string = "string";
string = `string`; // ES6

string = 'string "type"';
string = "string 'type'";

"string 'type'" // "string 'type'"
'string "type"' // "string \"type\""
`string 'type'` // "string 'type'"
`string "type"` // "string \"type\""
```

#### 템플릿 리터럴

ES6부터 템플릿 리터럴이란 문자욜 표기법이 도입되었고, 다음과 같은 편리한 문자열 처리 기능이 제공된다.

**멀티라인 문자열**

일반 문자열에서는 줄바꿈이 허용되지 않는다. 하지만 백슬래시로 시작하는 이스케이프 시퀀스를 사용하면 가능하게 된다.

```javascript
"123
123" // VM278:1 Uncaught SyntaxError: Invalid or unexpected token

"123 \
123" // "123 123"
```

| 코드 | 출력 |
| :--- | :--- |
| `\XXX` | 8진수 Latin-1 문자 |
| `\'` | 작은따옴표 |
| `\"` | 큰따옴표 |
| `\\` | 역슬래시 |
| `\n` | 개행 |
| `\r` | 캐리지 리턴 |
| `\v` | 세로 탭 |
| `\t` | 탭 |
| `\b` | 백 스페이스 |
| `\f` | 폼 피드 |
| `\uXXXX` | 유니코드 코드포인트 |
| `\u{X}` ... `\u{XXXXXX}` | 유니코드 코드포인트 |
| `\xXX` | Latin-1 문자 |

**표현식 삽입**

자바스크립트에서는 `+`연산자를 통해서 문자열을 연결할 수 있다. 따라서 다음과 같이 문자열 안에 변수값을 넣기 위해서 다음과 같은 방식을 이용할 수 있다.

```javascript
var first = 'Ung-mo';
var last = 'Lee';

console.log('My name is ' + first + ' ' + last + '.');
```

하지만 이런 방식은 가독성이 낮고 불필요한 타이밍을 초래한다. 하지만 표현식 삽입을 사용하면 다음과 같이 깔끔한 코드를 작성할 수 있다.

```javascript
var first = 'Ung-mo';
var last = 'Lee';

console.log('My name is ${first} ${last}');
```

표현식 삽입은 ${}으로 표현하려는 변수를 감싸 표현할 수 있다. 문자열이 아닌 타입은 문자열로 타입이 강제로 변환되어 삽입된다.

**태그드 템플릿**

템플릿 리터럴을 발전된 형태로 태그를 사용하여 템플릿 리터럴을 함수로 파싱할 수 있다. 태그 함수의 첫 번째 인수는 문자열 값의 배열이고 나머지는 인수 표현식과 관련된다.

```javascript
var person = 'Mike';
var age = 28;

function myTag(strings, personExp, ageExp) {

  var str0 = strings[0]; // "that "
  var str1 = strings[1]; // " is a "

  // 사실 이 예제의 string에서 표현식이 두 개 삽입되었으므로
  // ${age} 뒤에는 ''인 string이 존재하여
  // 기술적으로 strings 배열의 크기는 3이 됩니다.
  // 하지만 빈 string이므로 무시하겠습니다.
  // var str2 = strings[2];

  var ageStr;
  if (ageExp > 99){
    ageStr = 'centenarian';
  } else {
    ageStr = 'youngster';
  }

  // 심지어 이 함수내에서도 template literal을 반환할 수 있습니다.
  return str0 + personExp + str1 + ageStr;

}

var output = myTag`that ${ person } is a ${ age }`;

console.log(output);
// that Mike is a youngster
```

### 불리언 타입

불리언 타입의 값은 논리적 참, 거짓을 나타내는 타입으로 프로그램의 흐름을 제어하는 조건문에서 자주 사용된다. 하지만 불리언 값을 감싸고 있는 Boolean 객체는 몇가지 규칙이 있어 사용함에 있어 주의가 필요하다.

Boolean 객체의 첫번째 매개변수는 전달된 값을 불리언 값으로 변환된다. `값이 없거나, 0, -1, null, false, NaN, undefined, 빈 문자열("")`이라면 객체의 초기값은 **false**이다. 그 **외의 초기값은 true**로 설정한다.

하지만 Boolean 객체의 true/false는 원시 불리언의 true/false와 **혼동해선 안된다**. 값이 undefined, null이 아닌 **모든** 객체는 조건문에서 true로 계산된다. 이는 false인 Boolean 객체도 포함된다.

```javascript
var x = new Boolean(false);
if (x) {
  // 이 코드는 실행됨
}
```

따라서 Boolean 원시 값의 자리에서 Boolean 객체를 이용해선 안된다.

### undefined 타입

undefined 타입의 값은 undefined가 유일하다. 선언한 변수는 암묵적으로 undefined로 초기화된다. 변수 선언에 의해 확보된 메모리 공간을 빈 상태가 아닌 자바스크립트 엔진이 undefined로 초기화한다. 따라서 선언만 하고 할당하지 않은 변수를 참조하면 undefined가 반환된다. 따라 undefined가 의미하는 바는 `개발자가 선언하였지만 아직 아무것도 할당하진 않았다.`이며 개발자가 임의로 undefined를 초기화 목적으로 사용하면 혼란을 줄 수 있으므로 권장하지 않는다. 따라서 개발자가 빈 값을 초기화하기 위한 목적이라면 `null`을 사용하자.

#### typeof와 undefined

undefined는 앞서 설명한 바와 같이 초기화가 이뤄지지 않은 변수에 자바스크립트 엔진이 할당해준다. 따라서 개발자는 다음과 같은 조건문을 사용할 수 있다.

```javascript
var x;
if (x === undefined) {
  // 이 문이 실행됨
}
```

하지만 undefined에는 두 가지의 의미를 가지고 있다. 하나는 변수가 선언 후 초기화 되었는가?이고, 또 하나는 변수 자체가 선언되었는가?이다. 하지만 후자의 경우 다음 코드에선 오류가 발생하게 된다.

```javascript
if(x === undefined) {
  // ReferenceError 
}  
if (typeof x === 'undefined') {
  // 오류 없이 true로 평가
  // 이 문이 실행됨
}
```

따라서 typeof를 사용하는 것을 권장한다.

### null 타입

null 타입의 값은 null이 유일하다. 자바스크립트는 대소문자를 구별하므로 null은 Null, NULL 등과 다르다. 프로그래밍 언어에서 null은 의도적으로 값이 없다는 것을 명시한다. 자바스크립트 엔진은 누구도 참조하지 않는 메모리 공간에 대해 가비지 콜렉션을 수행할 것이다.

자바스크립트에서는 undefined와 null의 의미가 언뜻보면 비슷하기에 사용 여부를 구분하기 위해 비교를 하는 경우가 많다. 따라서 다음과 같은 연산 결과에 주의하자.

```javascript
typeof null          // "object" (하위호환 유지를 위해 "null"이 아님)
typeof undefined     // "undefined"
null === undefined   // false
null  == undefined   // true
null === null        // true
null == null         // true
!null                // true
isNaN(1 + null)      // false
isNaN(1 + undefined) // true
```

### 심벌 타입

심벌은 ES6에서 추가된 7번째 타입으로, 변경 불가능한 원시 타입의 값이다. 심벌 값은 다른 값과 중복되지 않는 유일무이한 값이다. 따라서 이름이 충돌한 위험이 없는, 객체의 유일한 프로퍼티 키를 만들기 위해 사용한다.

심벌 이외의 원시 값은 리터럴을 통해 생성하지만 심벌은 Symbol 함수를 호출해 생성한다. 이때 생성된 심벌값은 외부에 노출되지 않으며 중복되지 않는 유일무이한 값이다.

```javascript
var sym1 = Symbol();
var sym2 = Symbol("foo");
var sym3 = Symbol("foo");
Symbol("foo") === Symbol("foo"); // false
var sym = new Symbol(); // TypeError
```

### 객체 타입

### 데이터 타입의 필요성

#### 데이터 타입에 의한 메모리 공간의 확보와 참조

* _저장_ 메모리 공간의 **크기** 결정
* _읽어 들여야 할_ 메모리 공간의 **크기**
* 2진수의 **해석**

값은 메모리에 저장하고 참조할 수 있어야 한다. 이런 일을 하기에 앞서 해당하는 값의 크기에 적절한 공간을 메모리에 확보해야 한다. 자바스크립트 엔진은 데이터 타입에 따라 필요한 메모리 공간의 크기를 결정한다. 참조의 경우는 값이 저장된 메모리의 선두 메모리셀의 주소를 찾아가며 읽어들일 크기를 알고 있어야 한다. 여기서 읽어들일 크기는 데이터 타입을 알고 있으면 문제가 없기 때문에 데이터 타입은 메모리의 크기를 표현하고 개발자가 해당 데이터를 어떤 데이터로 다룰지 결정하는 키워드이다.

### 동적 타이핑

우리가 흔히 알고 있는 C언어는 정적 타이핑을 지원한다. 즉, 변수를 선언할 때 값의 종류\(데이터 타입\)을 명시해야 한다. 이런 행위를 명시적 타입 선언이라고 한다. 이렇게 선언한 변수는 이후 해당하는 데이터 타입에 맞는 값만 가질 수 있게 된다. 이렇게 타입을 강제하게 되면 타입의 일괄성을 유지하고 개발자는 선언된 변수에 저장된 데이터를 의미에 맞게 사용할 수 있다.

하지만 자바스크립트에서는 var, let, const 키워드를 사용해 변수를 선언할 뿐, 미리 선언된 데이터만 할당할 수 있는 것이 아니다.

```javascript
var foo;
console.log(typeof foo); // undefined
foo = 3;
console.log(typeof foo); // number
foo = 'abcd';
console.log(typeof foo); // string
```

다시말해 자바스크립트의 변수는 선언이 아니라 할당에 의해 타입이 추론된다. 그리고 재할당에 의해 변수의 타입은 언제든지 동적으로 변할 수 있다. 이런 특징을 **동적 타이핑**이라고 하며 이런 특징을 가진 자바스크립트를 **동적 타입 언어**라고 한다.

이렇게 변수를 유연하게 동적 타이핑할 수 있는건 복잡한 프로그램에서는 변화하는 변수 값을 추적하기 어려울 수 있다. 더욱이 자바스크립트는 개발자의 의도와는 상관없이 자바스크립트 엔진에 의해 암묵적으로 타입이 자동으로 변환되기도 한다. 이렇듯 언어의 _유연성은 높지만 신뢰성이 떨어지는 트레이드-오프_가 존재한다.

