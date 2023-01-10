---
layout: default
title: Migrate from LiveData to Flow
parent: Flow
grand_parent: Android
---

# LiveData를 Flow로 마이그레이션 (with DataBinding)

## LiveData vs Flow

LiveData와 StateFlow은 유사하다.
둘 다 데이터 홀더 클래스이며, 데이터를 observe하여 데이터의 값이 바뀔 때마다 데이터가 변경됨을 알리는 점에서 유사성을 띤다.

**LiveData**는 **android 종속성**(android/androidx package!)을 의존하고 있다. 또한, View의 Lifecycle의 정보를 가지고 있기 때문에 view가 **STOPPED 상태에 들어가면 자동으로 옵저빙을 중단**한다.
반면에 **StateFlow**는 Coroutine에 종속되어 있어, view의 lifecycle를 모르기 때문에 **직접적으로 제어**해주어야 한다.

### 일단 Coroutine이라는 장점

### 아키텍쳐적 관점에서의 LiveData

LiveData는 android 종속성을 가지고 있고 UI와 밀접하게 연결되어 있다.
이는 클린 아키텍쳐 설계 시, 순수 Java/Kotlin 코드만을 사용해야하는 domain layer나 비동기 처리가 활발한 data layer에서 사용하기에는 적합하지 않다.

<!-- https://cesarmorigaki.medium.com/replace-singleliveevent-with-kotlin-channel-flow-b983f095a47a

https://mashup-android.vercel.app/mashup-10th/sehee/coroutines-stateflow-channel/ -->

<br/>

## 마이그레이션

### 기본 (LiveData to StateFlow)

StateFlow를 통해 LiveData를 대체하는 방법은 아주 간단하다.
MutableLiveData를 MutableStateFlow로 대체하면 끝이다.

```kotlin
class MainViewModel : ViewModel() {
	private val _count = MutableLiveData<Int>()
	val count: LiveData<Int> = _count

	// to do something . . .
}
```

```kotlin
class MainViewModel : ViewModel() {
	// StateFlow는 LiveData와 달리 initial value를 지정해야 함
	private val _count = MutableStateFlow<Int>(0)
	val count = _count.asStateFlow()

	// to do something . . .
}
```

StateFlow는 lifecycle을 감지하지 못하기 때문에 직접적으로 스코프를 지정해주어 observe 해야한다.

```java
class MainFragment() : Fragment() {
	private val viewModel by viewModels<MainViewModel>()

	override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
	    super.onViewCreated(view, savedInstanceState)

	    // 대충 Lifecycle이 STARTED일 때 observe를 반복한다는 뜻
	    viewLifecyclerOwner.lifecyclerScope.launch {
		    viewLifecyclerOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
			    viewModel.count.collect {
				    log.d("TAG", "count: $it")
			    }
		    }
	    }
	}
}
```

<!-- ### repeatOnLifecycle / launchWhenStarted -->

### SingleLiveEvent to Channel

SingleLiveEvent의 경우 Channel로 마이그레이션 할 수 있다.
<!-- 간단하게 Channel의 개념에 대해 설명하자면, stateFlow와 달리 기본 버퍼 사이즈가 지정되어 있어 그 사이즈 이상의 데이터가 발행되면 ... 무시된다? -->

```kotlin
class MainViewModel : ViewModel() {
	private val _action = Channel<Action>()
	val action = _action.receiveFlow()
	...
}
```

<!-- ### Channel과 sealed class를 이용해 action event 처리하기

## References -->
