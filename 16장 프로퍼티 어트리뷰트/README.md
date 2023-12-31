## 😀 내부 슬롯과 내부 메서드

: 자바스크립트 엔진의 구현 알고리즘을 사용하기 위한 의사 프로퍼티와 의사 메서드 (**자바스크립트 엔진의 내부 로직**)
-> [[]] 이중 괄호로 감싼 것들
-> 자바스크립트 엔진에서 동작하지만, **개발자가 직접 접근 불가**

```js
const o = {};
o.__proto__; // 이렇게 접근 가능
```

-> 모든 객체는 [[Prototype]]이라는 내부 슬롯 가짐
-> **일부** 내부 슬롯/내부 메서드에 한하여 간접적으로 접근 가능

## 😀 프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체

### ❤️ 프로퍼티 어트리뷰트

: 프로퍼티 생성 시, 프로퍼티 상태 나타내는 프로퍼티 어트리뷰트 기본값으로 자동 정의
-> 즉, 객체의 프로퍼티 생성 시, 자바스크립트 엔진이 관리하는 **프로퍼티의 내부 상태** 값인 **내부 슬롯**

- 프로퍼티 값 (value), 값의 갱신 가능 여부(writable), 열거 가능 여부 (enumerable), 재정의 가능 여부 (configurable)
  -> 직접 접근할 수 없지만, '**Object.getOwnPropertyDescriptor**' 메서드로 간접적으로 확인 가능

```js
const person={
  name="Lee"
};

console.log(Object.getOwnPropertyDescriptor(person, 'name'));
```

-> 첫번째 매개변수는 **객체의 참조**, 두번째 매개변수는 프로퍼티 **키**
-> **프로퍼티 디스크립터 객체 반환**
-> 존재하지 않는 프로퍼티 디스크립터 요구: undefined 반환
**Object.getOwnPropertyDescriptor**
-> 모든 프로퍼티의 프로퍼티 디스크립터 객체 반환

## 😀 데이터 프로퍼티와 접근자 프로퍼티

### ❤️ 데이터 프로퍼티

: 키와 값으로 구성된 일반적인 프로퍼티

<데이터 프로퍼티 어트리뷰트>

#### 1. [[Value]]

: 프로퍼티 키를 통해 프로퍼티 값에 접근하면 반환되는 값

#### 2. [[Writable]]

: 프로퍼티 값의 변경 가능 여부 (불리언 값)

#### 3. [[Enumerable]]

: 프로퍼티의 열거 가능 여부 (불리언) (for..in이나 keys 메서드로 열거 불가)

#### 4. [[Configurable]]

: 프로퍼티 재정의 가능 여부
-> false: 삭제, 값의 변경 불가

---> 초반엔 모두 true로 초기화

### ❤️ 접근자 프로퍼티

: 자체적으로 값을 갖고 있지 않고, 다른 데이터 프로퍼티의 **값을 읽거나 저장**할 때 호출되는 접근자 함수

<프로퍼티 어트리뷰트>

#### 1. [[Get]]

: 프로퍼티 값 읽을 때 호출되는 접근자 함수

#### 2. [[Set]]

: 데이터 값 저장할 때 호출되는 접근자 함수

#### 3. [[Enumerable]]

#### 4. [[Configurable]]

## 😀 프로퍼티 정의

: 새로운 프로퍼티 추가하면서, 프로퍼티 어트리뷰트 명시적으로 정의하거나, 기존의 것을 재정의하는 것 (갱신 가능한 지, 열거 가능한지, 재정의 가능한지 정의!)
**Object.defineProperty**
-> 인수로 객체의 참조, 데이터 프로퍼티의 키인 문자열, 프로퍼티 디스크립터 객체 전달
-> 정의하지 않으면 기본 값 적용됨
**Object.defineProperties**
-> 한 번에 여러 개의 프로퍼티 정의

## 😀 객체 변경 방지

: 객체는 변경 가능한 값이므로 프로퍼티 추가, 삭제, 갱신, 재정의 가능한데,
이러한 객체 변경을 방지하는 메서드 존재 (메서드마다 금지 강도 다름)
-> 방지했는데 작성한 코드는 모두 무시

### ❤️ 객체 확장 금지

Object.preventExtensions
: 프로퍼티 추가 불가

### ❤️ 객체 밀봉

Object.Seal
: 프로퍼티 추가, 삭제, 재정의 불가

### ❤️ 객체 동결

Object.freeze
: 읽기만 가능

```js
const userInfo = {
  name: "Kang",
  age: 30,
};

Object.freeze(userInfo);

delete userInfo.age;

console.log(userInfo);
```

---> 여기까지는 얕은 변경 방지로 직속 프로퍼티만 변경이 방지되고, 중첩 객체까지는 영향을 주지 못함

### ❤️ 불변 객체

: 읽기 전용의 불변 객체
-> 객체를 값으로 갖는 모든 프로퍼티에 재귀적으로 Object.freeze 호출
