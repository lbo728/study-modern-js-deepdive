# 7번째 데이터 타입 Symbol
## 33.1 심벌이란?
`심벌`(Symbol)은 ES6에서 도입된 7번째 데이터 타입으로 변경 불가능한 원시 타입의 값이다.
- 심벌 값은 다른 값과 중복되지 않은 유일무이한 값이다.
- 따라서 주로 이름의 충돌 위험이 없는 유일한 프로퍼티 키를 만들기 위해 사용한다.
> #### _과거에는?_
> _1997년 자바스크립트가 ECMAScript로 표준화된 이래로 자바스크립트에는 6개의 타입 즉, 
> `문자열`, `숫자`, `불리언`, `undefiend`, `null`, `객체` 타입이 있었다._


<br>

## 33.2 심벌 값의 생성

## 33.3 심벌과 상수

## 33.4 심벌과 프로퍼티 키

## 33.5 심벌과 프로퍼티 은닉

## 33.6 심벌과 표준 빌트인 객체 확장

## 33.7 Well-known Symbol
**자바스크립트가 기본 제공하는 빌트인 심벌 값**
- ECMAScript 사양에서는 `Well-known Symbol`이라 부른다.
- 빌트인 심벌 값은 `Symbol 함수`의 프로퍼티에 할당되어 있다.
- 심벌은 중복되지 않는 상수 값을 생성하는 것은 물론 기존에 작성된 코드에 영향을 주지 않고 새로운 프로퍼티를 추가하기 위해, **즉 하위 호환성을 보장하기 위해 도입되었다.**
<img width="472" height="448" alt="스크린샷 2025-08-26 오후 1 23 02" src="https://github.com/user-attachments/assets/88daa1ac-624c-43bd-aa58-9183a97fdeb3" />

### 대표 사례: Symbol.iterator
```js
const iterable = {
  [Symbol.iterator]() {
    let cur = 1;
    const max = 5;
    return {
      next() {
        return { value: cur++, done: cur > max + 1 };
      }
    }
  } 
}

for (const num of iterable) {
  console.log(num); // 1 2 3 4 5
}

```
