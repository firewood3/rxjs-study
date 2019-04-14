### 1부
1. RxJS가 해결하려고 했던 문제1 - 입력 데이터 구조적 문제
2. RxJS가 해결하려고 했던 문제2 - 상태 전파 문제
3. RxJS가 해결하려고 했던 문제3 - 로직 오류
4. 부록1. 1부를 마치며
5. 부록2. 함수형 프로그래밍

### 2부
1. RxJS란 무엇인가?
2. Observable 만들기
3. RxJS의 핵심 - Observable
4. RxJS 오퍼레이터를 살펴보기 전에
5. 자동완성 UI 만들기
6. 자동완성 UI 사용성 개선하기
7. 자동완성 UI와 Subject
8. 캐러셀 UI 만들기
9. 캐러셀 UI 상태 관리하기
10. 캐러셀 UI 애니메이션 만들기
11. 부록1. RxJS의 Subjects
12. 부록2. 자바스크립트 비동기 처리 과정과 RxJS 스케줄러

## RxJS를 시작하기 전에
웹 어플리케이션은 상태머신
```code
      상태
       |
입력값->로직->결과값
```

RxJS는 입력오류, 상태오류, 로직오류로 인해 발생하는 문제를 효과적으로 처리하기 위한 고민의 산출물이다.
궁극적으로 RxJS는 오류가 발생하지 않는 애플리케이션을 만들기 위해 일관된 방식으로 안전하게 데이터 흐름을 처리하도록 도와주는 라이브러리이다.

*RxJS는 범용 데이터 흐름 제어 솔루션*
- 상태전파를 위한 리액티브 프로그래밍 패러다임 포함
- 로직 오류를 방지하기 위한 함수형 프로그래밍 패러다임 포함

## 1부

### 1-1 RxJS가 해결하려고 했던 문제1 - 입력 데이터의 오류
동기와 비동기는 시간의 축으로 봤을 때 같은 형태이다. 그리고 이런 형태는 시간을 인덱스로 둔 컬렉션으로 생각할 수도 있다. RxJS는 이를 스트림(stream)이라 표현한다. RxJS에서는 이런 스트림을 표현하는 Observable 클래스를 제공한다.

Observable은 시간을 인덱스로 둔 컬렉션을 추상화한 클래스이다. 이 클래스는 동기나 비동기의 동작방식으로 전달된 데이터를 하나의 컬렉션으로 바라볼 수 있게 해준다. 이렇게 함으로써 개발자는 데이터가 어떤 형태로 전달되는에 대해 더이상 고민할 필요가 없어진다. 단지, Observable을 통해 데이터를 전달받기만 하면 된다.

모든 데이터는 Observable 인스턴스로 만들 수 있다.
- 키보드를 눌러서 입력된 데이터
- 마우스를 이동하거나 클래해서 입력된 데이터
- Ajax/fetch 요청을 통해 얻은 데이터
- Web socket을 통해 전달된 데이터
- Messages를 통해 전달된 데이터

=> 동기든 비동기든 모든 데이터 타입을 동일한 형태로 사용할 수 있다.

rxjs 네임스페이스
```js
// 이벤트를 Observalbe로 만들기
fromEvent(HTMLElement, "이벤트 타입");

const { fromEvent } = rxjs;
const key$ = fromEvent(document, "keydown");
const click$ = fromEvent(document, "click");
```

```js
// iterable, array-like, promise 데이터를 Observable로 만들기
from(Iterable|Array-like|Promise);

const { form } = rxjs;
const arrayFrom$ = from([10, 20, 30]);
const iterableFrom$ = from(new Map([[1,2],[3,4],[5,6]]));
const ajaxPromiseFrom$ = from(fetch("./api/some.json"));
```

```js
// 연속적으로 전달되는 단일 데이터를 Observable로 만들기
of(...items);

const { of } = rxjs;
const numberOf$ = of(10, 20, 30);
const stringOf$ = of("a", "b", "c");
```

입력데이터의 오류 문제는 입력 시점이 동기와 비동기로 다양하다는 것 이었다.

RxJs는 이 문제를 시간을 인덱스로 둔 컬랙션으로 생각하였고 이를 스트림으로 부르고 그 구현체가 Observable 클래스이다. Observable은 모든 데이터를 표현할 수 있는 일원화된 데이터 구조이다. Observable을 사용하면 개발자는 더이상 데이터가 어떤 형태로 전달되는지에 대해 고민할 필요가 없다.

### RxJS가 해결하려고 했던 문제2 - 상태 전파 문제
B 머신이 A 머신을 의존하고 있을 때, B->A
<br>A 머신의 상태가 바뀌었는데 B 머신은 그 사실을 모르는 문제

*해결책: 옵저버 패턴을 사용하여 결합도를 낮춘다.*

[위키백과 : 옵저버 패턴](https://ko.wikipedia.org/wiki/%EC%98%B5%EC%84%9C%EB%B2%84_%ED%8C%A8%ED%84%B4)

**옵저버 패턴**
- Observer는 특정 머신의 변화를 감지해야 하는 머신
- Subject는 옵저버들에게 특정 머신의 변화를 알려주는 머신

**옵저버 패턴 사용법**
- B 머신은 A 머신의 상태값을 전달 받는 인터페이스(Observer)를 구현한다.
- Subject는 B 머신을 구성 하고(복수 구성 가능) B 머신에게 상태변화를 알리는 인터페이스를 구현한다.
- A 머신은 자신의 상태변화를 Subject를 통해 B 머신에게 알린다.

**예**

setAstate 함수로 A 머신의 a_state 값을 변경해도 B 머신의 b_state는 변경되지 않음
```ts
class Bmechine {
    b_state = 0;
    construct(a_machine: Amachine) {
        this.b_state = a_machine + 1;
    }
}

class Amechine {
    a_state = 0;
    // setAstate 함수로 a_state 값을 변경해도 b_state는 변경되지 않음
    setAstate(a_state) {
        this.a_state = a_state;
    }
}
```

setAstate 함수로 A 머신의 a_state 값을 변경하면 B 머신의 b_state도 변경됨
```ts
class Bmachine implements Observer {
    b_state = 0;
    update(a_state) {
        this.b_state = a_state + 1;            
    }
}

class Subject {
    oservers: Observer[];
    addMachine(machine: Observer) {
        this.observers.push(machine);
    }
    notify(state) {
        this.observers.forEach(machine => {
            machine.update(state);
        });
    }
}

class Amachine {
    a_state = 0;
    construct(subject: Subject) {
        this.subject = subject;
    }
    // setAstate 함수로 a_state 값을 변경하면 b_state도 변경됨
    setAstate(a_state) {
        this.a_state = a_state;
        this.subject.notify(this.a_state);
    }
}
```

**RxJS의 상태전파 해결**
<br>RxJS의 Subject는 다수의 Observer에게 공통의 데이터를 전달하고 Observerble은 하나의 Observer에게 독립적인 데이터를 전달한다.

**RxJS는 옵저버패턴을 개선한다**
1. 인터페이스의 확장
<br> 옵저버 패턴의 update()메소드에서 next(), error(), complete() 메소드로 확장
2. readonly
<br> 옵저버 패턴은 데이터의 흐름을 양방향으로 코딩 가능, RxJS는 데이터의 흐름을 양뱡향으로 코딩 불가.(Observable은 read-only)
<br> 옵저버 패턴에서 옵저버는 서브젝트로부터 데이터를 제공받는데, 반대로 옵저버에서 서브젝트로 데이터를 주게끔 프로그래밍 할 수도 있다. 이것은 데이터가 양방향으로 흐는 것을 의미하고 프로그래밍을 복잡하게 만든다. 그래서 Observable은 Subscribe를 통해 옵저버에게 데이터를 전달할 수는 있지만, 반대로 Observable은 옵저버로부터 데이터를 전달 받을 수는 없다.

리엑티브 프로그래밍 : 데이터흐름이 자동적으로 전파되는것. 옵저버패턴의 push방식

Pull 방식은 데이터를 얻고자 하는 대상이 데이터를 직접 가져오는 방식
Push 방식은 데이터를 얻고자 하는 대상(Observer)이 의존관계의 대상(Subject)으로부터 데이터를 제공받는 형식


### RxJS가 해결하려고 했던 문제3 - 로직 오류
반목분과 분기문 그리고 변수는 코드의 복잡도를 높이고 오류 발생빈도를 높인다.
<br>=> ES5에서 제공하는 Array의 filter.map.reduce와 같은 고차함수를 사용하면 반복문과 분기문, 변수를 사용하지 않고 프로그래밍 할 수 있다. 

**고차함수(high-order function)**
- 다른 함수를 인자로 받거나 그 결과로 함수를 반환하는 함수

RxJS에서는 ES5 Array의 고차함수와 같은 오퍼레이터(operator)를 제공하므로써 로직에 존재하는 분기문과 반복문 그리고 변수를 제거하려 하였다.



***
참고도서  
제목: RxJS 퀵스타트  
저자: 손찬욱  
출판사: 루비페이퍼  
