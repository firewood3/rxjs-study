## RxJS 간단 정리

### 1부
1. RxJS가 해결하려고 했던 문제1 - 입력 데이터 구조적 문제
2. RxJS가 해결하려고 했던 문제2 - 상태 전파 문제
3. RxJS가 해결하려고 했던 문제3 - 로직 오류
4. 부록1. 1부를 마치며
5. 부록2. 함수형 프로그래밍

### 2부
1. RxJS란 무엇인가?
2. Observable 만들기
3. <U>RxJS의 핵심 - Observable</U>
4. <U>RxJS 오퍼레이터를 살펴보기 전에</U>
5. <U>자동완성 UI 만들기</U>
6. 자동완성 UI 사용성 개선하기
7. 자동완성 UI와 Subject
11. 부록1. RxJS의 Subjects
12. 부록2. 자바스크립트 비동기 처리 과정과 RxJS 스케줄러


### 3부
1. 버스 노선 조회 서비스 살펴보기

***

## 정리: 1부
### 웹 어플리케이션은 상태머신
```code
      상태
       |
입력값->로직->결과값
```

### 웹 어플리케이션을 개발하면서 만나는 세가지 문제
- 입력 오류: 데이터 입력 시점이 동기와 비동기로 나누어져있어서 오류 발생 가능성이 증가된다.
- 상태 오류: B->A라는 의존성 있는 두 머신간에서 A 머신의 상태가 바뀌었는데 B 머신은 그 사실을 몰라 오류 발생 가능성이 증가 된다.
- 로직 오류: 반목분 분기문 변수의 사용은 프로그램의 오류 가능성을 증가 시킨다.


### 해결책
1. Observable을 사용하면 동기 입력이든 비동기 입력이든 같은 형식으로 데이터 처리 가능하다.
2. Observable은 옵저버 패턴을 응용한 것으로 머신간 의존성이 분리되고 Push방식의 상태전파가 된다.
3. Operator와 순수함수로 프로그래밍 하면 반복문, 분기문, 변수의 사용을 줄일 수 있다.

### RxJS 이론
- RxJS는 범용 데이터 흐름 제어 라이브러리이다.
- RxJS를 사용하면 입력오류, 상태오류, 로직오류를 해결할 수 있다.
    - 입력 오류의 해결: 동기로 입력된 데이터든 비동기로 입력된 데이터든 같은 형식으로 데이터를 처리할 수 있도록 하였다. (Observable / fromEvent / of)
    - 상태 오류의 해결: 개선된 옵저버 패턴을 도입하므로써 두 머신간의 의존성을 낮추고 상태 전파를 push 방식(Reactive Programming 도입)으로 바꾸었다. (Observable / subscribe / operator)
        - [옵저버 패턴](https://firewood3.github.io/design-pattern/observer_pattern/observer-pattern/V)
        - Observer: 특정 머신의 변화를 감지해야 하는 머신
        - Subject: 옵저버들에게 특정 머신의 변화를 알려주는 머신(Observable)
        - Pull 방식: 데이터를 얻고자 하는 대상이 데이터를 직접 가져오는 방식
        - Push 방식: 데이터를 얻고자 하는 대상(Observer)이 의존관계의 대상(Subject)으로부터 데이터를 제공받는 형식
        - Reactive Programming: 머신간 데이터의 흐름이 자동으로 전파되는 프로그래밍 패러다임
    - 로직 오류의 해결: 함수형 프로그래밍을 도입하므로써 로직 오류의 발생 가능성을 줄였다. (Operator 사용, 람다와 순수함수로 코딩)
        - 함수형 프로그래밍: 자료 처리를 수학적 함수의 계산으로 취급하고 상태변경과 가변데이터를 피하려는 프로그래밍 패러다임
        <br>=> 로직을 최대한 쪼갤수 있는 함수까지 쪼개고 그 함수도 순수함수로 만들어서 프로그래밍 하는 것
        - 순수함수: 함수의 실행이 외부에 영향을 받지도 않고 외부에 영향을 끼치지 않는 함수
        - Operator: collections과 함께 사용되는 함수를 다루는 함수로써 프로그래밍을 도아준다. (map, filter)




***

## 정리: 2부-1,2 

#### RxJS
- Observable: 시간을 축으로 연속적인 데이터를 저장하는 컬렉션을 표현한 객체
- Observer: Observable이 전달하는 데이터를 소비하는 주체
- Operators: Observable을 생성 및 조작하는 함수들
- Subscription: Observable.prototype.subscribe의 반환값. Observable에게 데이터를 전달 받고 싶지 않을 경우 unsubscribe 메소드를 사용함

#### 플러스
- Pipe: 오퍼레이터들을 연결해주는 함수
- pluck: 스트림에서 입력으로 들어온 객체의 특정 프로퍼티를 추출하는 오퍼레이터
- filter: 스트림에서 입력으로 들어온 객체들 중 특정 조건에 맞는 객체만 추출하는 오퍼레이터

#### Observable 생성자를 통해 Observable을 만들 수 있다.
- new Observable
- Observable.create

#### rxjs 네임스페이스에 있는 생성함수로 Observable을 만들기
of / range / fromEvent / from / interval

#### empty / throwError / never 함수로 Observable 만들기
- empty : complete된 Observable을 반환
- throwError: error된 Observable을 반환
- never: 종료되지 않은 Observable을 반환

***옵저버블을 만들때 생성자를 통해 만들지 말고 생성함수로 만들어라!***

***
## 불변객체 Observable
ES5 Array의 고차함수들이 바환값으로 새로운 Array객체를 반환하여 각각에 영향을 미치지 않도록 하는것과 같이 RxJS의 오퍼레이터는 항상 새로운 Observable을 반환함으로써 Array의 고차함수과 같이 불변 객체(immutable object)를 반환한다.

고차함수의 불변객체
```js
var arr = [1,2,3];
var mappedArr = arr.map(v=>v);

console.log(arr === mappedArr); // false
```

Operator Map은 입력으로 들어온 Obserable을 사용해 또 다른 Observable을 생성함
```js
map = function(transfromationFx) {
    const source =this;
    const result = new rxjs.Observable(observer => {
        // 새로운 Observable은 현재의 Observable을 subscribe 한다.
        source.subscribe(
            // 현재의 Observable에서 전달된 데이터를 변경하여 전달한다.
            function(x) {observer.next(transformationFn(x))},
            function(err) {observer.error(err);},
            function() {observer.complete();}
        )
    })
}
```

***
## 정리: 2부-3,4

### Observable의 특징
- 모든 데이터는 Observable 인스턴스로 만들 수 있다.
- Observable은 읽기 전용(read-only)이다. (Observable이 Observer로부터 데이터를 받는 것을 허용하지 않게 하기 위해)
 <br>=>옵저버 패턴에서 옵저버는 서브젝트로부터 데이터를 제공받는데, 반대로 옵저버에서 서브젝트로 데이터를 주게끔 프로그래밍 할 수도 있다. 이것은 데이터가 양방향으로 흐는 것을 의미하고 프로그래밍을 복잡하게 만든다. 그래서 Observable은 Subscribe를 통해 옵저버에게 데이터를 전달할 수는 있지만, 반대로 Observable은 옵저버로부터 데이터를 전달 받을 수는 없다.
- Observable은 리엑티브하다.
<br>=>Operator 사용과 옵저버 패턴의 PUSH 방식
- Observable은 불변 객체(immutable object)다.
<br>=>외부에서 조작 불가능하도록 만들어놓음.

함수 VS Observable VS Promise

| 구분  |  함수 | Observable  | Promise | 
| ---- | ---- | ---- | ---- | 
| 정의 | 함수 선언 | Observable 객체 생성 | Promise 객체 생성 | 
| 호출 | 함수 호출 | Observable.subscribe | Promise.then<br>=>new Promise([logic])시 로직 바로 호출됨<br>=>then은 Promise의 resolve() 결과만 호출하는 것임. | 
| 호출 시 정의부 실행 여부 | 매번 정의부 실행  | 매번 정의부 실행<br>=>Observable은 subscribe에 로직을 정의한다.<br>=>호출 시 매번 subscribe 함수의 정의부가 실행된다.  | 생성 시 단 한번 호출<br>=>Promise 생성 시 정의한 로직이 바로 실행된다.<br>=>then() 호출시 정의부가 실행되지 않는다. | 
| 지연(Lazy) 가능 여부<br>=>객체 생성 후<br>=>정의부 지연 가능여부 | O <br>=>콜백함수의 경우 함수 선언 후<br>정의부 실행이 지연된다. | O<br>=>Subscribe의 실행을 지연 시킬 수 있음.  | X(Promise는 정해진 상태값만 호출된다.)<br>=>Promise 생성 로직에서 비동기 호출을 할 수는 있겠으나,<br>=>Promise 생성 후 다른방법(then)으로 실행로직을 지연시킬 수 없다. | 
| 데이터 | 한 개<br>=>함수 리턴값은 한번만 리턴함 | 여러 개<br>=>subscribe함수 내에서 next()를 여러번 호출하여<br>=>데이터 스트림을 반환할 수 있음 | 한 개<br>=>Promise 정의부에서는 resolve()함수를 한번만 호출 가능 | 
| 에러 처리 지원 | 별도로 없음 | error 상태<br>=>subscribe에서 throwError를 던지고<br>=>Ovserver에서 error로 잡음 | reject 상태<br>=>Promise 로직 선언부에서 reject()호출하고<br>=>catch()로 reject()결과를 받을 수 있음 | 
| 취소 지원 | X | O<br>=>Observable의 반환값인 subscription의<br>=>unsubscribe() 함수로 취소 가능 | X |
| 전달 방식 | Pull | Push<br>=>subscribe 로직부분에서 next()로<br>=>전달한 상태가 Observer로 전달됨. | Push<br>=>Promise선언부의 resolve()로<br>=>전달한 상태가 then()으로 전달됨. | 


Observable
```js
const click$ = fromEvent(document, "click")
            .pipe(
                tap(x=> console.log(x)),
                pluck("clientX")
            );

const subscription = click$.subscribe({
    next: v=> {console.log("click 이벤트 발생"); console.log(v) },
    error: e=>console.log(e),
    complete: ()=>console.log("완료")
});
setTimeout(function(){
    subscription.unsubscribe();
},10000);
```

Promise
```js
let myFirstPromise = new Promise((resolve, reject) => {
    setTimeout(function(){
        // 비동기 성공시 resolve() 함수 호출
        resolve("Success!");
        // 비동기 실패시 reject() 함수 호출
        reject("reject!");
    }, 1000);
});
myFirstPromise
    .then(value => {console.log(value)})
    .catch(error => {console.log(error)})
    ;
```
***
참고도서  
제목: RxJS 퀵스타트  
저자: 손찬욱  
출판사: 루비페이퍼  

