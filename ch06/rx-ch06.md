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
3. RxJS의 핵심 - Observable
4. RxJS 오퍼레이터를 살펴보기 전에
5. 자동완성 UI 만들기
6. <U>자동완성 UI 사용성 개선하기</U>
7. <U>자동완성 UI와 Subject</U>
11. 부록1. RxJS의 Subjects
12. 부록2. 자바스크립트 비동기 처리 과정과 RxJS 스케줄러


### 3부
1. 버스 노선 조회 서비스 살펴보기

***
## 2-6 자동완성 UI 사용성 개선하기
***

## 추가 오퍼레이터
1. partition
2. switchAll
3. switchMap
4. catchError
5. retry
6. finalize

### partition
- partition은 조건이 참인경우와 거짓인 경우에 대해 Observable을 각각 분리하여 두개의 크기를 가진 배열로 반환한다.
```js
const [odd$, even$] = of(1,2,3,4,5,6,7,8,9)
                    .pipe(
                        // 조건을 만족하면 odd$, 그렇지 않으면 even$
                        partition(n=>n%2==1)
                    );
odd$.subscribe(n=>console.log(`odd: ${n}`)); // 1, 3, 5, 7, 9
even$.subscribe(n=>console.log(`even: ${n}`)); //  2, 4, 6, 8
```

### switchAll과 swithMap
- mergeAll 처럼 Observable을 입력으로 받고 입력받은 Observable을 평탄화 하는 오퍼레이터
- 가장 최근에 들어온 Observable을 구독하고 이전의 구독 대상인 Observable을 구독 취소 하는 오퍼레이터
- 네트워크 통신에서 유용하게 쓰임(같은 유형의 요청을 서버에 여러번 하지만 가장 최근에 요청한 응답만 받고 싶을 때)
- switchMap = map + switchAll

```js
fromEvent(document, 'click').pipe(
    tap(()=> console.log('click')),
    map(event=>interval(1000)), // 비동기의 반복적인 호출로 바꾸어버림
    // mergeAll(),
    switchMap(),
)
.subscribe(
    x=>console.log(x)
);

/*
-mergeAll-
3번 클릭 했을 때, 이전의 Observable(Interval)이 그대로 남아있음
map => Observable(Observable(Interval), Observable(Interval), Observable(Interval))
mergeAll => Observable(0 ... 1 ... 2 ... 0 ... 3 ... 1 ... 4 ... 2 ... 0 ... 5 ... 3 ... 1 ...)
sibscribe => click ... 0 ... 1 ... click ... 2 ... 0 ... 3 ... 1 ... click 4 ... 2 ... 0 ... 5 ... 3 ... 1 ...
*/
/*
-switchAll-
3번 클릭 했을 때, 이전의 Observable(Interval)은 남아있지 않고 현재의 Observable(Interval)만 남아있다.
map => Observable(Observable(Interval))
switchAll => Observable(1 ... 2 ...)
subscribe => click ... 1 ... 2 ... click ... 1 ... 2 ... click ... 1 ... 2 ...
*/
```

### catchError
- Observable에서 발생하는 error를 catch 하는 오퍼레이터 
- 반환 값을 선택할 수 있다.
    1. throwError 반환(Ovserver.error 호출)
    2. 현재 옵저버블 반환(재구독(현재 옵저버블의 미러) - 에러가 발생할때마다 재구독 되므로 무한루프 조심..)
    3. 새로운 옵저버블 반환(재구독은 아님)
```js
of(1,2,3,4,5,6,7,8,9)
.pipe(
    mergeMap(val=>{
        if(val>5) {return throwError('Error!');}
        return of(val);
    }),
    catchError((error, currentObservable)=>{
        console.log(error);
        return throwError(error);
        // return currentObservable;
        // return of(3,3,3,3,6);
    })
)
.subscribe({
    next: val => console.log(val),
    error: val => console.log(`${val}: 에러 발생 후 종료(unsubscribe)`)
});
```

### retry
- retry(n)
- 에러가 발생한 경우 소스 옵저버블을 n 번동안 재 구독한다.(소스 옵저버블의 미러를 반환..!?)
- n+1 번째 에러가 발생한 경우 Ovserver.error를 호출한다.
- 현재 옵저버블의 미러를 반환한다.
```js
of(1,2,3,4,5,6,7,8,9)
.pipe(
    mergeMap(val=>{
        if(val>5) {return throwError('Error!');}
        return of(val);
    }),
    retry(2)
)
.subscribe({
    next: val => console.log(val),
    error: val => console.log(`${val}: 재 구독 2회 후 종료`)
});
```

### finalize
- finalize(callback함수)
- 옵저버블이 error나 complete되었을 때 콜백함수를 실행시키는 오퍼레이터
- error나 complete가 발생하였을 때 후처리를 위해 사용하면 유용하다.
- 현재 옵저버블의 미러를 반환한다.
```js
 of(1,2,3,4,5,6,7,8,9)
.pipe(
    mergeMap(val=>{
        if(val>5) {return throwError('Error!');}
        return of(val);
    }),
    catchError((error, currentObservable)=>{
        return throwError(error);
    }),
    tap(value=>console.log(`${value}: tap 오퍼레이터`)), // 에러가 발생 시 잡을 수 없음
    finalize(value=>console.log(`${value}: finalize 오퍼레이터`)) // 에러(error) or 완료(complete) 시 잡을 수 있음
)
.subscribe({
    next: val => console.log(val),
    error: val => console.log(`${val}: 에러 발생`),
    complete: val => console.log(`${val}: 완료`)
});
```

***
## 자동완성 UI에 적용 부분
- partitioin: 입력값이 0 보다 클때는 ajax 호출하고 입력값이 0 일때는 reset 호출 하는 로직
- switchMap: 이미 요청한 ajaxGET 요청을 취소하고 새롭게 요청한 ajaxGET을 받기 위해 사용
- retry, finalize: 서버 요청 에러를 잡기 위해 사용