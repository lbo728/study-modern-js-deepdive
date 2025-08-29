## 46.1 제너레이터란?
- 코드 블록의 실행을 일시 중지 했다가 필요한 시점에 재개할 수 있는 `특수한 함수`

*[제너레이터와 일반 함수의 차이]*
1. 제너레이터 함수는 함수 호출자에게 함수 실행의 제어권을 양도할 수 있다.
  → 함수의 제어권을 독점하는 것이 아님
2. 제너레이터 함수는 함수 호출자와 함수의 상태를 주고받을 수 있다.
  → 함수의 상태를 전달할 수도, 전달 받을 수도 있음
3. 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.
  → 이터러블이면서 동시에 이터레이터인 제너레이터 객체를 반환

## 46.2 제너레이터 함수의 정의
- 화살표 함수로 정의 불가
- new 연산자와 함께 생성자 함수로 호출 불가

```js
// 제너레이터 함수 선언문
function* genDecFunc() {
  yield 1;
}

// 제너레이터 함수 표현식
const genExpFunc = function* () {
  yield 1;
};

// 제너레이터 메서드
const obj = {
  * genObjMethod() {
    yield 1;
  }
};

// 제너레이터 클래스 메서드
class MyClass {
  * genClsMethod() {
    yield 1;
  }
};
```
## 46.3 제너레이터 객체
- 일반 함수처럼 함수 코드 블록 실행하는 것이 아님
  → 제너레이터 객체 생성하여 반환
- 제너레이터 객체는 이터러블이면서 동시에 이터레이터

```js
// 제너레이터 함수
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}

// 제너레이터 함수를 호출 → 제너레이터 객체 반환
const generator = genFunc();

// 이터러블은 Symbol.iterator 메서드를 직접 구현 or 프로토타입 체인을 통해 상속받은 객체
console.log(Symbol.iterator in generator); // true

// 이터레이터는 next 메서드를 갖는다.
console.log('next' in generator); // true
```

## 46.4 제너레이터의 일시 중지와 재개
- 제너레이터는 yield 키워드와 next 메서드를 통해 실행을 일시 중지했다가 필요한 시점에 다시 재개 가능
- 단, 일반 함수처럼 한 번에 코드 블록의 모든 코드를 일괄 실행하는 것이 아니라 yield 표현식까지만 실행
- yield 키워드는 제너레이터 함수의 실행을 중지 or yield 키워드 뒤에 오는 표현식의 평가 결과를 제너레이터 함수 호출자에게 반환
- 이터레이터의 next 메서드와 달리 제너레이터 객체의 next 메서드에는 인수를 전달 가능

```js
// 제너레이터 함수
function* getFunc(){
  yield 1;
  yield 2;
  yield 3;
}

// 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.
// 이터러블이면서 동시에 이터레이터인 제너레이터 객체는 next 메서드를 갖는다.
const generator = getFunc();

// 처음 next 메서드를 호출하면 첫 번째 yield 표현식까지 실행되고 일시 중지된다.
// next 메서드는 이터레이터 리절트 객체({value, done})를 반환한다.
// value 프로퍼티에는 첫 번째 yield 표현식에서 yield된 값 1이 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당된다.
console.log(generator.next()); // {value: 1, done: false}

// 다시 next 메서드를 호출하면 두 번째 yield 표현식까지 실행되고 일시 중지된다.
// next 메서드는 이터레이터 리절트 객체({value, done})를 반환한다.
// value 프로퍼티에는 두 번째 yield 표현식에서 yield된 값 2이 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당된다.
console.log(generator.next()); // {value: 2, done: false}

// 다시 next 메서드를 호출하면 세 번째 yield 표현식까지 실행되고 일시 중지된다.
// next 메서드는 이터레이터 리절트 객체({value, done})를 반환한다.
// value 프로퍼티에는 세 번째 yield 표현식에서 yield된 값 3이 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당된다.
console.log(generator.next()); // {value: 3, done: false}

// 다시 next 메서드를 호출하면 남은 yield 표현식이 없으므로 제너레이터 함수의 마지막까지 실행한다.
// next 메서드는 이터레이터 리절트 객체({value, done})를 반환한다.
// value 프로퍼티에는 제너레이터 함수의 반환값 undefined가 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었음을 나타내는 true가 할당된다.
console.log(generator.next()); // {value: undefined, done:true}
```

## 46.5 제너레이터의 활용
  ### 46.5.1 이터러블의 구현
  → 제너레이터 함수를 사용하면 간단히 이터러블을 구현

  - 기존의 무한 이터러블을 생성하는 함수
  ```js
  // 무한 이터러블을 생성하는 함수
  const infiniteFibonacci = (function () {
    let [pre, cur] = [0,1];
    
    return {
      [Symbol.iterator]() { return this; },
      next() {
        [pre, cur] = [cur, pre+cur];
        // 무한 이터러블이므로 done 프로퍼티를 생략
        return { value: cur };
      };
    };
  }()};
  
  // infiniteFibonacci는 무한 이터러블이다.
  for (const num of infiniteFibonacci) {
    if (num > 10000) break;
    console.log(num); // 1 2 3 5 8 ... 2584 4181 6765
  }
  ```
  - 제너레이터 함수를 사용하여 무한 이터러블을 생성하는 함수
  ```js
  // 무한 이터러블을 생성하는 제너레이터 함수
  const infiniteFibonacci = (function* () {
    let [pre, cur] = [0, 1];
    
    while(true) {
      [pre, cur] = [cur, pre+cur];
      yield cur;
    }
  }()};
  
  // infiniteFibonacci는 무한 이터러블이다.
  for (const num of infiniteFibonacci){
    if (num > 10000) break;
    console.log(num); // 1 2 3 5 8 ... 2584 4181 6765
  }
  ```

  ### 46.5.2 비동기 처리
  - 제네레이터 함수는 next 메서드와 yield 표현식을 통해 함수 호출자와 함수의 상태를 주고받을 수 있음
  - 이러한 특성을 활용하면 프로미스를 사용한 비동기 처리를 동기 처리처럼 구현할 수 있음
  - 다시 말해, 프로미스의 후속 처리 메서드 then/catch/finally 없이 비동기 처리 결과를 반환하도록 구현할 수 있음

  ```js
  // 제네레이터 실행기
  const async = generatorFunc => {
    const generator = generatorFunc(); // (2)
    
    const onResolved = arg => {
      const result = generator.next(arg); // (5)
      
      return result.done
        ? result.value // (9)
        : result.value.then(res => onResolved(res)); // (7)
    };
    
    return onResolved; // (3)
  };
  
  (async(function* fetchTodo(){ // (1)
    const url = 'https://jsonplaceholder.typicode.com/todos/1';
    
    const response = yield fetch(url); // (6)
    const todo = yield response.json(); // (8)
    console.log(todo);
    // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
  })()); // (4)
  ```
  1.  async 함수가 호출(1)되면 인수로 전달받은 제네레이터 함수 fetchTodo를 호출하여 제너레이터 객체를 생성(2)하고 onResolved 함수를 반환(3)한다. onResolved 함수는 상위 스코프의 generator 변수를 기억하는 클로저다.        async 함수가 반환한 onResolved 함수를 즉시 호출(4)하여 (2)에서 생성한 제너레이터 객체의 next 메서드를 처음 호출(5)한다.
  2.  next 메서드가 처음 호출(5)되면 제너레이터 함수 fetchTodo의 첫 번째 yield 문(6)까지 실행된다. 이때 next 메서드가 반환한 이터레이터 리절트 객체의 done 프로퍼티 값 false,
      즉 아직 제너레이터 함수가 끝까지 실행되지 않았다면 이터레이터 리절트 객체의 value 프로퍼티 값,
      즉 첫 번째 yield된 fetch 함수가 반환한 프로미스가 resolve한 Response 객체를 onResolved 함수에 인수로 전달하면서 재귀 호출(7)한다.
  3.  onResolved 함수에 인수로 전달된 Response 객체를 next 메서드에 인수로 전달하면서 next 메서드를 두 번째로 호출(5)한다.
      이때 next 메서드에 인수로 전달한 Response 객체는 제너레이터 함수 fetchTodo의 response 변수(6)에 할당되고 제너레이터 함수 fetchTodo의 두 번째 yield 문(8)까지 실행된다.
  4.  next 메서드가 반환한 이터레이터 리절트 객체의 done 프로퍼티 값이 false, 즉 아직 제너레이터 함수 fetchTodo가 끝까지 실행되지 않았다면 이터레이터 리절트 객체의 value 프토퍼티 값,
      즉 두 번째 yield된 response.json 메서드가 반환한 프로미스가 resolve한 todo 객체를 onResolved 함수에 인수로 전달하면서 재귀 호출(7)한다.
  5.  onResolved 함수에 인수로 전달된 todo 객체를 next 메서드에 인수로 전달하면서 next 메서드를 세 번째로 호출(5)한다.
      이때 next 메서드가 인수로 전달한 todo 객체는 제너레이터 함수 fetchTodo의 todo 변수(8)에 할당되고 제너레이터 함수 fetchTodo가 끝까지 실행된다.
  6.  next 메서드가 반환한 이터레이터 리절트 객체의 done 프로퍼티 값이 true, 즉 제너레이터 함수 fetchTodo가 끝까지 실행되었다면 이터레이터 리절트 객체의 value 프로퍼티 값,
      즉 제너레이터 함수 fetchTodo의 반환값인 undefined를 그대로 반환(9)하고 처리를 종료한다.
  
## 46.6 async/await


  ### 46.6.1 async 함수

  ### 46.6.2 await 키워드

  ### 46.6.3 에러 처리
