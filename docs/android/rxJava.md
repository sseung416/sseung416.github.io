# RxJava

## 기본 자료형, Observable

### Observable

- onNext() : 하나씩 순차적으로 데이터를 발행한다~
- onComplete() : 데이터 발행 끝남, onNext() 더 이상 호출 안 함
- onError() : 오류 발생


## Observable의 확장
### Single
Observable의 또 다른 형태로, **한 개**의 데이터나 에러만을 발행함
 -  **onSuccess**, **onError** 2가지 알림이 있음
- 데이터 발행의 완료를 알리지 않음 (**onComplete 메서드 없음**) 
- 결과를 단일값으로 가져오는 **네트워크 통신, 로컬 데이터베이스 조회** 등에 유용

### Maybe
한 개의 데이터만 발행한다는 점에서 Single과 유사하지만, **onComplete()** 메서드가 존재함
**데이터를 발행하지 않고도 컴플리트 시키기 가능!!**

- **onSuccess**, **onError**, **onComplete** 3가지 알림이 있음
- 데이터를 1개도 통지하지 않고 종료했을 때 onComplete 메서드를 호출함

### Completable
데이터 발행의 완료 및 에러 **신호만 보내는** 형태  

 -  **onSuccess**, **onError** 2가지 알림이 있음

## Backpressure and Flowable!
https://4z7l.github.io/2020/12/23/rxjava-7.html
https://proandroiddev.com/rxjava-backpressure-and-why-you-should-care-369c5242c9e6


### 배압(Backpressure)
데이터의 **생산과 소비가 불균형**적일 때 발생

- 0.1초마다 데이터 발행, 10초마다 데이터 소비
	-> 데이터 스트림에 데이터가 점차 쌓임
	=> overflow! OutOfMemory 오류 발생 가능성

### Flowable
Observable과 다르게 **스스로 배압 현상을 제어**함


### Observable vs Flowable
#### Observable를 권장해요!
- 1000개 미만의 데이터 발행
- GUI 이벤트 시
- Observable은 Flowable를 사용할 때보다 오버헤드가 낮아요!
#### Flowable를 권장해요!


## Disposable
### Disposable
코루틴의 **Job**과 비슷한 개념
**아이템의 발행을 중단**함으로써, 메모리릭을 방지

- 데이터가 무한정 발생하는 등 오랜시간 데이터를 발행하는 상황에서 제대로 종료하지 않으면 메모리릭의 가능성이 있음
	=> Disaposable를 사용해 이를 차단
```java
disposable.dispose()
```


### CopositeDisposable
 **여러 개의 Disposable 객체를 가지고 있어 한 번에 종료/관리**하는 것을 도움
- 안드로이드 개발할 땐 대부분 CompositeDisposable를 통해 Disposable를 관리함 (한 개씩 하면 매-우 귀찮기 때문...)
- 사용방법은 Disaposable과 동일
- 아래와 같이 Disaposable HashSet 인스턴스를 가지고 있어 한 번에 모든 Disposable의 종료가 가능함
```java
public final class CompositeDisposable implements Disposable, DisposableContainer {

    OpenHashSet<Disposable> resources;
    ....
}
```

### dispose() vs clear()

CompositeDisposable 클래스에는 Disposable를 중단하기 위한 메서드로 dispose()와 clear() 메서드가 있음
두 메서드의 차이점은 아이템 발행을 중단하고나서 **다시 Disposable를 추가할 수 있는가에 따라 달라짐**
CompositeDisposable 클래스의 add() 함수를 살펴보면, 내부적으로 disposed 변수가 false일 때 값을 추가하게 되어있음
ㅇㅇ


```java
// in CompositeDisposable . . .

// Disposable를 저장하는 변수
OpenHashSet<Disposable> resources;
// 한 번 dispose 되었는지 확인하는 변수
volate boolean disposed; // false
```
```java
public void dispose() {  
    if (disposed) {  
        return;  
	}  
    OpenHashSet<Disposable> set;  
	synchronized (this) {  
        if (disposed) {  
            return;  
		}  
        disposed = true;  
		set = resources;  
		resources = null;  
	}  
  
    dispose(set);  
}
```
```java
public void clear() {  
    if (disposed) {  
        return;  
    }  
    OpenHashSet<Disposable> set;  
    synchronized (this) {  
        if (disposed) {  
            return;  
	    }  
	    
        set = resources;  
	    resources = null;  
	} 
	
    dispose(set);  
}
```

## Hot Observable vs Cold Observable

### Hot Observable
**구독의 관계없이 데이터를 발행**하는 형태를 의미함
**가장 최근의 데이터만**을 처리한다고 생각하면 됨
Observer는 구독한 시점부터 발행된 데이터를 받을 수 있음

### Cold Observable
일반적으로 사용하는 형태로, 구독 전까지 데이터를 발행하지 않음
https://luukgruijs.medium.com/understanding-hot-vs-cold-observables-62d04cf92e03



## Subject, PublishedSubject. . . . . .

## subscribeOn vs subscribeWith
https://javaexpert.tistory.com/826
https://4z7l.github.io/2020/12/18/rxjava-6.html
