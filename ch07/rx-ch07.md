# Chapter07 자동 완성 UI와 Subject

## 개요
1. Cold Observable and Hot Observable
2. Subject 
3. ConnectableObservable
    - multicast 오퍼레이터
    - refCount 오퍼레이터
    - share 오퍼레이터
***
## Cold Observable and Hot Observable
[참고: angularfirebase](https://angularfirebase.com/lessons/rxjs-quickstart-with-20-examples/#Hot-Observable-Example)   
[참고: hot vs cold observable](https://medium.com/@benlesh/hot-vs-cold-observables-f8094ed53339)

### Cold Observable
- Observable의 내부에서 데이터를 전달하는 주체(Producer)를 생성함.
- Cold Observable은 하나의 Producer를 하나의 Observer에게 전달한다.
### Hot Observable
- Observable의 외부에서 데이터를 전달하는 주체(Producer)가 생성되고 Observable은 이 외부에서 생성된 Producer를 공유함.
- Hot Observable은 하나의 Producer를 여러 Observer에게 전달한다.

Cold Ovservable
```js
const cold = Rx.Observable.create( (observer) => {
    observer.next( Math.random() )
});

cold.subscribe(a => console.log(`Subscriber A: ${a}`)) // 다른 값을 전달 받음
cold.subscribe(b => console.log(`Subscriber B: ${b}`)) // 다른 값을 전달 받음
```

Hot Observable
```js
const x = Math.random()

const hot = Rx.Observable.create( observer => {
  observer.next( x )
});

hot.subscribe(a => console.log(`Subscriber A: ${a}`)) // 같은 값을 전달 받음
hot.subscribe(b => console.log(`Subscriber B: ${b}`)) // 같은 값을 전달 받음
```

| 구분 | Cold Ovservable | Hot Observable | 
| --- | --- | --- |
| 데이터 주체 생성 시기 | Observable 내부 | Observable 외부 | 
| Observer와의 관계 | 1:1<br>=>Observer마다 독립된 Producer를 가짐 | 1:N<br>=> 하나의 Producer를 여러 Observer가 공유 |
| 데이터 영역 | Observer마다 독립적 | N개의 Observer와 공유 |
| 데이터 전달 시점 | 구독하는 순간부터 데이터를 전달하기 시작<br>=>Ovserver가 구독하면 독립적인<br>=> Producer가 생성되고 Producer의 데이터를 전달받음 | 구독과 상관 없이 데이터를 중간부터 전달<br>=> Ovserver가 구독하면 외부의 Producer로부터 데이터를 전달받음  |
| RxJS 객체 | Observable<br>=> of, from, 생성자로 Observable을<br>=>생성할때, Producer를 정의부에 추가하는 경우 | fromEvent에 의해 생성된 Observable, ConnectableObsevable, Subject <br>=> Dom객체의 이벤트는 Ovservable의 외부에서 생성된 것이다. | 

***
## Subject
[참고: reactivex](http://reactivex.io/rxjs/manual/overview.html)

Subject는 특별한 타입의 Observable

Subject는 읽기와 쓰기가 가능한 Observable이다.
- 읽고 쓰는 대상
    - Observer의 상태를 업데이트 하는 next() 함수
- 읽기 가능
    - Observer는 subscribe 함수를 통해 Subject의 next()들을 읽을 수 있다.
    - Subject는 Observable을 상속받아 구현되었다.
    - Observable은 next()함수들을 subscribe() 함수에서 읽기만 한다.
- 쓰기 가능
    - Subject는 next()함수를 직접 사용하여 Observer에게 데이터를 전달 할 수 있다.
    - Subject에 Ovserver가 구현되어 있으므로 Subject에서 next()함수를 호출할 수 있다.
    - Subject는 Observer가 구현되어 있으므로 다른 Observable을 subscribe할 수 있다.
    
Subject는 HotObservable이다.
- Subject는 생성할때, Subscribe() 함수를 정의할 수 없으므로 ColdObserver이 될수 없다.
- => Observable은 Subscribe()함수를 정의하고 내부에 Producer를 생성할 수 있으므로 ColdObservable을 구현할 수 있는것.


```js
var subject = new Subject();
subject.subscribe({
  next: (v) => console.log('observerA: ' + v)
});
subject.subscribe({
  next: (v) => console.log('observerB: ' + v)
})
subject.next(1);
subject.next(2);
```

Subject를 사용하여 Observable을 공유하기

```code
      ---event---
      |         |
     num$      num$ // num$ 옵저버블은 동일하지만 subscribe()함수 부분은
      |         |   // 공유 되지 않기 때문에 event가 발생하면 두개의 num$으로 각각 전파된다.(Unicast)
     odd$      even$
      |         |
   subscribe   subscribe
   (ovserver)  (observer)
```

```code
      ---event---
           |
          num$         //  Ovservable과 Observer사이에 subject를 사용하여 연결하면 
           |           // 하나의 subscribe 정의부를 여러 Observer가 공유할 수 있다.(Multicast)
         subject      
      |          |   
     odd$       even$
      |          |
   subscribe    subscribe
   (ovserver)   (observer)    
```


```js
let num$ = fromEvent(document, "click")
        .pipe(
            pluck('offsetX'),
            tap(value=>console.log(`click : ${value}`)),
        );

const subject = new Subject();
num$.subscribe(subject)

// let odd$ = num$
let odd$ = subject
.pipe(
    tap(value=>console.log(`odd input : ${value}`)),
    filter(value=>value%2==1)
);
odd$.subscribe(value=>console.log(`odd : ${value}`))

// let even$ = num$
let even$ = subject
.pipe(
    tap(value=>console.log(`even input : ${value}`)),
    filter(value=>value%2==0)
);
even$.subscribe(value=>console.log(`even : ${value}`));
```

subject는 쓰기 가능하기 때문에 Side-effect를 발생시킬 수 있다. 그러므로 Observable을 공유하기 위해 Subject를 사용하여 Observable이나 Observer를 연결하는것은 권고되지 않는다.

***가급적이면 Subject를 Observable 내부에서 사용하라.***

***
## ConnectableObservable
[참고: reactivex](http://reactivex.io/rxjs/manual/overview.html)

- multicast 오퍼레이터와 Subject를 사용하면 Unicast인 Observable을 Multicast Observable로 만들 수 있다.
- Multicasted Observable은 데이터의 전달을 위해 connect() 함수를 호출하고 connect()의 반환값을 이용해 데이터 전달을 중지할 수 있다.
- 데이터 전달 : connect()
- 데이터 전달 중지: un = multi$.connect(); multi$.unsubscribe();
- publish 오퍼레이터: publish() == multicast(new Subject)
- reCount 오퍼레이터: mulicated Observable이 첫번째로 구독될때, connect()연결해줌. 마지막 Obserable이 구독해제할때 multicasted Observable도 구독 해지해줌.
- share 오퍼레이터: publish() + reCount()

```code
      ---event---
      |         |
     num$      num$ // num$ 옵저버블은 동일하지만 subscribe()함수 부분은
      |         |   // 공유 되지 않기 때문에 event가 발생하면 두개의 num$으로 각각 전파된다.(unicast)
     odd$      even$
      |         |
   subscribe   subscribe
   (ovserver)  (observer)
```

```code
      ---event---
           |
          num$(share)     // multicast와 subject를 사용하여 num$을 공유하였다.(multicast)
      |         |   
     odd$      even$
      |         |
   subscribe   subscribe
   (ovserver)  (observer)
```

```js
var subject = new Subject();
let num$ = fromEvent(document, "click")
        .pipe(
            pluck('offsetX'),
            tap(value=>console.log(`click : ${value}`)),
            multicast(subject),
            // publish() // publish() == multicast(new Subject)
        );
num$.connect();

let odd$ = num$
.pipe(
    tap(value=>console.log(`odd input : ${value}`)),
    filter(value=>value%2==1)
);
odd$.subscribe(value=>console.log(`odd : ${value}`))

let even$ = num$
.pipe(
    tap(value=>console.log(`even input : ${value}`)),
    filter(value=>value%2==0)
);
even$.subscribe(value=>console.log(`even : ${value}`));
```




*** 
## 자동 완성 UI에 적용
키보드 입력시 두 keyup 옵저버블로 전달되는 문제를 share() 오퍼레이터로 해결

```code
      ---event---
      |         |
     keyup$    keyup$ 
      |         |   
     user$      reset$
      |         |
   subscribe   subscribe
   (ovserver)  (observer)
```

```code
      ---event---
           |
        keyup$(share)     // share 오퍼레이터를 사용하여 keyup$을 공유하였다.(multicast)
      |         |   
     user$      reset$
      |         |
   subscribe   subscribe
   (ovserver)  (observer)
```