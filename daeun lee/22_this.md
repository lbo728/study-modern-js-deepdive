## 22.1 this 키워드
- 메서드가 자신이 속한 객체의 프로퍼티를 참조하려면,
  자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 함!

- this : 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 **자기 참조 변수**
→ this를 통해 자신이 속한 객체 또는 자신이 생성할 인스턴스의 프로퍼티나 메서드를 참조할 수 있다.

```js
// [예제 22-01]
const circle = {
  // 프로퍼티: 객체 고유의 상태 데이터
  radius = 5,
  // 메서드: 상태 데이터를 참조하고 조작하는 동작
  getDiameter() {
    // 자신이 속한 circle 객체를 참조할 수 있어야 한다.
    return 2 * circle.radius;
    (= return 2 * this.radius;)
  }
};

console.log(circle.getDiameter); // 10
```

```js
// [예제 22-04]
// 생성자 함수
function Circle(radius) {
  // this는 생성자 함수가 생성할 인스턴스
  this.radius = radius;
}

Circle.prototype.getDiameter = function () {
  // this는 생성자 함수가 생성할 인스턴스
  return 2 * this.radius;
}

// 인스턴스 생성
const circle = new Circle(5);
console.log(circle.getDiameter()); // 10
```
- 위의 두 this는 가리키는 대상이 다르다!
- this 바인딩은 함수 호출 방식에 의해 동적으로 결정
- 'use strict' mode (엄격 모드) 역시 this 바인딩에 영향을 줌
- this는 코드 어디에서든 참조 가능
- 전역에서도, 함수 내부에서도 가능

## 22.2 함수 호출 방식과 this 바인딩
**this 바인딩은 함수 호출 방식, 즉 함수가 어떻게 호출되었는지에 따라 동적으로 결정**
- 렉시컬 스코프와 this 바인딩은 결정 시기가 다르다
  - 렉시컬 스코프 : 함수 정의 평가 → 함수 객체 생성 → 상위 스코프를 결정
  - this 바인딩 :  함수 호출 시점에 결정

- 함수 호출 방식
  1. 일반 함수 호출 → 기본적으로 this에는 전역 객체(global object)가 바인딩
  2. 메서드 호출 → 메서드를 소유한 객체가 아닌 호출한 객체에 바인딩
  3. 생성자 함수 호출 → 생성자 함수가 (미래에) 생성할 인스턴스가 바인딩
  4. Function.prototype.(apply/call/bind) 메서드에 의한 간접 호출
  → apply/call/bind 메서드는 Function.prototype의 메서드
  → 이들 메서드는 모든 함수가 상속받아 사용할 수 있음

  ```js
  // [예제 22-06]
  const foo = function () {
    console.dir(this);
  }

  // 1. 일반 함수 호출
  foo(); // window → 전역 객체

  // 2. 메서드 호출
  const object = { foo };
  object.foo(); // object →메서드 호출 객체

  // 3. 생성자 함수 호출
  new foo(); // foo {} → 생성한 인스턴스

  // 4. Function.prototype.(apply/call/bind) 메서드에 의한 간접 호출
  // foo 함수 내부의 this는 인수에 의해 결정
  const bar = { name: 'bar'};

  foo.call(bar); // bar
  foo.apply(bar); // bar
  foo.bind(bar); // bar
  ```

  ```js
  // [예제 22-19] → 4번의 예제
  function getThisBinding() {
    return this;
  }

  // this로 사용할 객체
  const thisArgument = { a: 1 };

  console.log(getThisBinding()); // window

  // getThisBinding 함수를 호출하면서 인수로 전달한 객체를
  // getThisBinding 함수에 this에 바인딩
  console.log(getThisBinding.apply(thisArgument)); //{ a: 1 }
  console.log(getThisBinding.call(thisArgument)); //{ a: 1 }
  ```
  ⇒ apply와 call 메서드의 본질적인 기능 : 함수 호출
