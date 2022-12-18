---
layout: default
title: 단일 이벤트 처리 - SingleLiveData & EventWrapper
parent: LiveData
---

# 단일 이벤트 처리: SingleLiveData & EventWrapper

---

- 사용 이유

**View의 재활성화로 인해 불필요한 Observer Evnet를 막기 위함.**

화면 회전과 같은 동작이 발생하면, Activity는 파괴되고 다시 생성됨. 하지만 viewModel은 파괴되지 않고 그대로 살아있기 때문에 viewModel 안의 데이터도 유지됨.

따라서 재생성된 Activity가 viewModel의 liveDate를 한번 더 observe 하게 되면서 불필요한 Observer Event가 호출됨.

이를 개선하기 위해 나온 것이 단일 이벤트인 SingleLiveData와 EventWrapper임.

- 화면 회전 생명 주기
    
    세로로 되어있던 액티비티가 onPause() -> onStop() -> onDestroy()  되고
    가로로 된 액티비티가 다시 onCreate() -> onStart() -> onResume() 호출을 한다.
    

- SingleLiveData

**한 개의 observer만 구독**이 가능하며, 여러개를 구독했을 때는 어떤 것이 실행될지 모름

(따라서 EventWrapper를 더 권장함)

```kotlin
class SingleLiveEvent<T> : MutableLiveData<T?>() {
		// 멀티쓰레딩 환경에서 동시성을 보장하는 AtomicBoolean
    private val pending = AtomicBoolean(false)

    override fun observe(owner: LifecycleOwner, observer: Observer<in T?>) {
				// hasActiveObservers(): 현재 활성화된 observer가 있는지 boolean으로 return
        if (hasActiveObservers()) {
            Log.w(TAG, "Multiple observers registered but only one will be notified of changes.")
        }

        super.observe(owner, Observer { t ->
						// 아래는 pending 변수가 true면 if문의 로직을 처리하고 false로 바꾼다는 뜻임
						// setValue를 통해서만 pending 값을 true를 바꿀 수 있기 때문에
						// activity가 재시작되더라도 불필요한 Observer event가 나타나지 않음
            if (pending.compareAndSet(true, false)) {
                observer.onChanged(t)
            }
        })
    }

    @MainThread
    override fun setValue(@Nullable t: T?) {
        pending.set(true)
        super.setValue(t)
    }

		// 데이터 속성을 지정하지 않더라도 setValue()를 호출할 수 있음
    @MainThread
    fun call() {
        value = null
    }

    companion object {
        private const val TAG = "SingleLiveEvent"
    }
}
```

[멀티쓰레드에서 동시성 보장하기 (AtomicBoolean)](https://www.notion.so/AtomicBoolean-f3e26507c3d646508c9ce7598aa4fb6f)

- EventWrapper (권장)

여러 개를 구독해야 할 때 사용

```kotlin
open class Event<out T>(private val content: T) { 
		// 단일 이벤트가 실행됐는지 판단하는 변수
		var hasBeenHandled = false
		private set 
	
		// 이벤트가 실행 되지 않았으면 이벤트 실행
		// 기존의 단일 이벤트가 실행되지 않았을 때만 아래 메서드를 실행해야 함
		// 하나의 observer에서만 사용 가능해서 대부분 peekContent()를 더 많이 씀
		fun getContentIfNotHandled(): T? { 
				return if (hasBeenHandled) { 
						null 
				} else {
						hasBeenHandled = true 
						content 
				} 
		} 
	
		// 이벤트 처리 유무와 관계 없이 값 반환
		fun peekContent(): T = content 
	} 
	
	// observe 할 때 일반 Observer 대신 EventObserver를 사용해야 함
class EventObserver<T>(private val onEventUnhandledContent: (T) -> Unit) : Observer<Event<T>> { 
		override fun onChanged(event: Event<T>?) {
				event?.getContentIfNotHandled()?.let { value -> 
						onEventUnhandledContent(value) 
				} 
		} 
}
```

+ Event에 Nullable한 값이 들어가야할 경우

[ViewModel에서 View(Activity, Fragment)로 이벤트를 전달하는 방법](https://seunghyun.in/android/6/)

```kotlin
class EventObserver<T>(private val onEventUnhandledContent: (T?) -> Unit) : Observer<Event<T>> {
    override fun onChanged(event: Event<T>?) {
        event ?: return

        if (event.content == null && !event.hasBeenHandled) {
            event.getContentIfNotHandled()
            onEventUnhandledContent(null)
            return
        }

        event.getContentIfNotHandled()?.let { value ->
            onEventUnhandledContent(value)
        }
    }
}
```