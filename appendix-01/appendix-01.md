# 부록 1 RxJS의 Subject
### 구독하기 전에 생성된 데이터를 전달 할 수 있는 Subject들
- behabiorSubject: 구독하기 전 데이터 1개를 전송
- replaySubject: 구독하기 전 데이터 n개 전송
- asyncSubject: 일반적인 subject와는 달리 데이터를 전달하지 않다가 Complete시에 마지막 데이터를 전달

### Multicasted Observable에서 구독하기 전에 생성된 데이터 사용 오퍼레이터들
- publishBehavior(value)
- publishReplay(bufferSize, windowTime)
- publishLast()
- shareReplay(bufferSize, windowTime)


### 예제
* Observable 구독하기 전 데이터 생성
* subject 구독하기 전 데이터 생성 불가
* behaviorSubject 구독하기 전 데이터 1개 읽어들임
* replaySubject 구독하기 전 데이터 n 개 읽어들임 (시간이지나면 구독하기전 데이터 전송 하지 않게 설정 가능)
* asyncSubject 데이터를 전달하고 있지 않다가 complete시에 마지막 데이터를 전달

* publishBehavior
* publishReplay 
* publishLast
* shareReplay 

==> subject.next를 호출할 수 없으니.. 클릭 이벤트로? 해야할듯?
hot 옵저버블인데 어떻게 외부에서 데이터를 줄 것인가?

click 이벤트로 multicasted Ovservable에게 데이터를 전송하고


Observer의 생성시점을 setTimeOut으로 미룬다.

last의 경우는 setTimeout을 2번 사용하여 ovserver의 생성시점을 미루고(1s) subject의 complete(2s)를 각각 따로 해준다.