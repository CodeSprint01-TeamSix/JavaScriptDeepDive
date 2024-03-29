> # 📖 모듈

### 모듈이란

- 애플리케이션을 구성하는 개별적 요소로서 재사용 가능한 코드 조각을 말한다
- 모듈이 성립하려면 자신만의 `파일 스코프(모듈 스코프)`를 가질 수 있어야 한다.

1. 모듈은 공개가 필요한 자산에 한정하여 명시적으로 선택적 공개가 가능하다. 이를 `export`라 한다.
2. 모듈 사용자는 모듈이 공개한 자산 중 일부 또는 전체를 선택해 자신의 스코프 내로 불러들여 재사용할 수 있다. 이를 `import`라 한다.

## ES6 모듈(ESM)

- ES6에서 부터 모듈 기능이 추가했다
- script 태그에 type='module' 어트리뷰트를 추가하면 로드된 자바스크립트 파일은 모듈로서 동작한다.
- ESM임을 명확이 하기 위해 파일 확장명을 mjs를 사용하는 것을 권장한다.
- ESM은 클래스와 마찬가지로 기본적으로 strict mode가 적용된다.

```html
<script type="module" src="app.mjs"></script>
```

### 모듈 스코프

ESM이 아닌 일반적인 자바 스크립트 파일은 script 태그로 분리해서 로드해도 독자적인 모듈 스코프를 갖지 않는다

```javascript
// foo.js
// x 변수는 전역 변수다.
var x = 'foo';
console.log(window.x); // foo

// bar.js
// x 변수는 전역 변수다. foo.js에서 선언한 전역 변수 x와 중복된 선언이다.
var x = 'bar';

// foo.js에서 선언한 전역 변수 x의 값이 재할당되었다.
console.log(window.x); // bar

// html 파일
<!DOCTYPE html>
<html>
<body>
  <script src="foo.js"></script>
  <script src="bar.js"></script>
</body>
</html>
```

타입을 지정해주지 않아서 script가 `하나의 전역으로 공유한다`(각 파일에 선언한 변수가 전역변수가 된다)

### 독자적인 모듈 스코프를 가지려면 타입지정 해줄것!!!!

```javascript
// foo.mjs
// x 변수는 전역 변수가 아니며 window 객체의 프로퍼티도 아니다.
var x = 'foo';
console.log(x); // foo
console.log(window.x); // undefined

// bar.mjs
// x 변수는 전역 변수가 아니며 window 객체의 프로퍼티도 아니다.
// foo.mjs에서 선언한 x 변수와 스코프가 다른 변수다.
var x = 'bar';
console.log(x); // bar
console.log(window.x); // undefined

// html 파일
<!DOCTYPE html>
<html>
<body>
  <script type="module" src="foo.mjs"></script>
  <script type="module" src="bar.mjs"></script>
</body>
</html>
```

## export 키워드

모듈 내부에서 선언한 식별자를 외부에 공개하여 다른 모듈들이 재사용할 수 있게 하려면 `export` 키워드를 사용한다

```javascript
// lib.mjs
// 변수의 공개
export const pi = Math.PI;

// 함수의 공개
export function square(x) {
  return x * x;
}

// 클래스의 공개
export class Person {
  constructor(name) {
    this.name = name;
  }
}
```

#### 한번에 export 하는 방법

```javascript
// lib.mjs
const pi = Math.PI;

function square(x) {
  return x * x;
}

class Person {
  constructor(name) {
    this.name = name;
  }
}

// 변수, 함수 클래스를 하나의 객체로 구성하여 공개
export { pi, square, Person };
```

## import 키워드

다른 모듈에서 공개한 식별자를 자신의 모듈 스코프 내부로 로드하려면 `import` 키워드를 사용한다.(파일 확장자명은 생략할 수 없음)

```javascript
// app.mjs
// 같은 폴더 내의 lib.mjs 모듈이 export한 식별자 이름으로 import한다.
// ESM의 경우 파일 확장자를 생략할 수 없다.
import { pi, square, Person } from './lib.mjs';

console.log(pi);         // 3.141592653589793
console.log(square(10)); // 100
console.log(new Person('Lee')); // Person { name: 'Lee' }

//html 파일
<!DOCTYPE html>
<html>
<body>
  <script type="module" src="app.mjs"></script>
</body>
</html>
```

### 하나의 이름으로 import 하는 방법

import 되는 식별자는 뒤에 as 뒤에 지정한 이름의 객체 프로퍼티로 할당하면 된다.

```javascript
// app.mjs
// lib.mjs 모듈이 export한 모든 식별자를 lib 객체의 프로퍼티로 모아 import한다.
import * as lib from "./lib.mjs";

console.log(lib.pi); // 3.141592653589793
console.log(lib.square(10)); // 100
console.log(new lib.Person("Lee")); // Person { name: 'Lee' }
```

```javascript
// app.mjs
// lib.mjs 모듈이 export한 식별자 이름을 변경하여 import한다.
import { pi as PI, square as sq, Person as P } from "./lib.mjs";

console.log(PI); // 3.141592653589793
console.log(sq(2)); // 4
console.log(new P("Kim")); // Person { name: 'Kim' }
```

### 하나의 값만 export 한다면 default 키워드를 사용할 수 있다

1. default 키워드를 사용하면 기본적으로 이름 없이 하나의 값을 export 한다
2. 선언자 키워드를 사용할 수 없다(var, let, const)
3. {} 없이 임의의 이름으로 import 한다.

```javascript
// lib.mjs   ------ 1
export default x => x * x;

// lib.mjs  ------------ 2
export default const foo = () => {};
// => SyntaxError: Unexpected token 'const'
// export default () => {};

// app.mjs ---------- 3
import square from './lib.mjs';

console.log(square(3)); // 9
```
