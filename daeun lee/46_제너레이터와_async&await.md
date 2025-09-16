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
- async/await를 사용하면 프로미스의 후속 처리 메서드를 사용하지 않고 동기 처럼 프로미스를 사용할 수 있음.

```js
const fetch = require('node-fetch');

async function fetchTodo() {
  const url = 'https://jsonplaceholder.typicode.com/todos/1';
  
  const response = await fetch(url);
  const todo = await response.json();
  console.log(todo); // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
}

fetchTodo();
```

  ### 46.6.1 async 함수
  - await 키워드는 반드시 async 함수 내부에서 사용해야 함.
  - async 함수는 async 키워드를 사용해 정의할 수 있으며 언제나 프로미스를 반환.
  - async 함수가 명시적으로 프로미스를 반환하지 않더라도 async 함수는 암묵적으로 반환값을 resolve하는 프로미스를 반환.

  ```js
  // async 함수 선언문
  async function foo(n) { return n }
  foo(1).then(v => console.log(v)); // 1

  // async 함수 표현식
  const bar = async function(n) { return n };
  bar(2).then(v => console.log(v)); // 2

  // async 화살표 함수
  const baz = async n => n;
  baz(n).then(v => console.log(v)); // 3

  // async 메서드
  const obj = {
    async foo(n) { return n; }
  };
  obj.foo(4).then(v => console.log(v)); // 4

  // async 클래스 메서드
  class MyClass {
    async bar(n) { return n; }
  }
  const myClass = new MyClass();
  myClass.bar(5).then(v => console.log(v)); // 5
  ```

  - 클래스의 constructor 메서드는 async 메서드가 될 수 없음.
  - 클래스의 constructor 메서드는 인스턴스를 반환해야 하지만 async 함수는 언제나 프로미스를 반환해야 함.

  ```js
  class MyClass {
    async constructor() { }
    // SyntaxError: class constructor may not be an async method
  }
  const myClass = new MyClass();
  ```

  ### 46.6.2 await 키워드
  - await 키워드는 프로미스가 settled 상태(비동기 처리가 수행된 상태)가 될 때까지 대기하다가 settled 상태가 되면 프로미스가 resolve한 처리 결과를 반환.
  - awiat 키워드는 반드시 프로미스 앞에서 사용.

  ```js
  const fetch = require('node-fetch');

  const getGithubUserName = async id => {
    const res = await fetch(`https://api.github.com/users/${id}`); // ①
    const { name } = await res.json(); // ②
    console.log(name); // daeun Lee
  };

  getGithubUserName('daeun Lee')  
  ```

  - await 키워드는 프로미스가 settled 상태가 될 때까지 대기하다 프로미스가 settled 상태가 되면 프로미스가 resolve한 결과를 res 변수에 할당.
  - 이처럼 await 키워드는 다음 실행을 일시 중지시켰다가 프로미스가 settled 상태가 되면 다시 재개.

  ```js
  async function foo() {
    const a = await new Promise(resolve => setTimeout(() => resolve(1), 3000));
    const b = await new Promise(resolve => setTimeout(() => resolve(2), 2000));
    const c = await new Promise(resolve => setTimeout(() => resolve(3), 1000));

    console.log([a, b, c]); // [1, 2, 3]
  }

  foo(); // 약 6초 소요.
  ```

  - 위 예제의 foo 함수는 종료될 때까지 6초가 소요됩니다. 그 이유는 await 키워드는 프로미스가 settled 상태가 될 때까지 대기하기 때문.
  - setTimeout 함수가 종료되면 그때 프로미스는 settled 상태가 되어 다음 코드가 실행.
  - 이렇게 서로 연관이 없는 비동기 처리는 개별적으로 수행되는 비동기 이므로 처리가 완료될 때까지 대기하여 순차적으로 처리할 필요 없음.
  - 이때 Promise.all()을 활용할 수 있습니다.

  ```js
  async function foo() {
    const res = Promise.all([
      new Promise(resolve => setTimeout(() => resolve(1), 3000));
      new Promise(resolve => setTimeout(() => resolve(2), 2000));
      new Promise(resolve => setTimeout(() => resolve(3), 1000));
    ])

    console.log(res); // [1, 2, 3]
  }

  foo(); // 약 3초 소요.
  ```

  - Promise.all()을 활용하면 프로미스가 병럴 처리됨.
  - 다음 예제의 경우,
      첫 번째 프로미스는 3초 후에 1을 resolve 함.
      두 번째 프로미스는 2초 후에 2을 resolve 함.
      세 번째 프로미스는 1초 후에 3을 resolve 함.
    그렇게 약 3초 후에 모든 프로미스가 fullfilled 상태가 되면 모든 프로미스를 배열에 저장하고 새로운 프로미스를 반환.

  ### 46.6.3 에러 처리
  - 비동기 처리를 위한 콜백 패턴의 단점 중 가장 심각한 것은 에러 처리가 곤란하다는 것.
  - 에러는 호출자 방향으로 전파. 즉, 콜 스택의 아래 방향(실행중인 실행 컨텍스트가 푸시되기 직전에 푸시된 실행 컨텍스트 방향)으로 전파.
  - 하지만 콜백 함수를 호출한 것은 비동기 함수가 아니기 때문에 try...catch 문을 사용해 에러를 캐치할 수 없음.

  ```js
  try {
    setTimeout(() => {
      throw new Error('Error!');
    }, 1000);
  } catch (error) {
      // 에러를 캐치하지 못한다.
      console.error('캐치한 에러 : ', error);
  }
  ```

  - async/await에서 에러 처리는 try...catch문을 사용할 수 있음.
  - 콜백 함수를 인수로 전달받는 비동기 함수와는 달리 프로미스를 반환하는 비동기 함수는 명시적으로 호출할 수 있기 때문에 호출자가 명확함.

  ```js
  const fetch = require('node-fetch');

  const foo = async () => {
    try {
      const wrongUrl = 'https://wrong.url';

      const response = await fetch(wrongUrl);
      const data = await response.json();
      console.log(data);
    } catch (error) {
      console.error(error); // TypeError : Failed to fetch
    }
  }

  foo();
  ```

  - 위 예제의 foo 함수의 catch 문은 HTTP 통신에서 발생한 네트워크 에러뿐 아니라 try 코드 블록 내의 모든문에서 발생한 일반적인 에러까지 모두 캐치가 가능.
  - async 함수 내에서 catch 문을 사용해서 에러 처리를 하지 않으면 async 함수는 발생한 에러를 reject하는 프로미스를 반환.
  - Promise.prototype.catch 후속 처리 메서드를 사용해 에러를 캐치할 수도 있음.

  ```js
  const fetch = require('node-fetch');

  const foo = async () => {
    const wrongUrl = 'https://wrong.url';

    const response = await fetch(wrongUrl);
    const data = await response.json();
    return data;
  }

  foo().then(console.log).catch(console.error); // TypeError : Failed to fetch
  ```
