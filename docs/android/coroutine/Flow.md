---
layout: default
title: Flow
parent: Android
has_children: true
permalink: /docs/android/flow
---

# Coroutine Flow

{: .fs-6 .fw-300 }

---

## 코루틴 내부적으로 살펴보기

https://mashup-android.vercel.app/mashup-11th/dahyun/coroutineinternal/coroutineinternal/

## Flow란?

## Flow의 Hot Observable: StateFlow, SharedFlow

### Flow의 Hot Observable

**Flow**는 RxJava의 Observable과 같이 **cold stream**의 형태를 띤다.
즉, Flow는 구독 시점부터 스트림을 생성해 데이터를 처음부터 수신한다.
이러한 Flow를 Hot Observable 형태로 지원해주는 것이 StateFlow와 SharedFlow이다.
StateFlow/SharedFlow는 LiveData처럼 데이터 홀더의 역할도 함
Flow는 말 그대로 데이터의 흐름으로. . .

### StateFlow vs SharedFlow

StateFlow는 SharedFlow의 자식 클래스로(Flow <- SharedFlow <- StateFlow), 두 클래스의 차이는 생성자로 정의할 수 있는 옵션의 차이라고 생각하면 됨

MutableStateFlow를 정의하는 부분을 보면

```kotlin
class MainViewModel(
	private val repository: UserRepository
) : ViewModel {

	private val _userList = MutableStateFlow<UiState<List<User>>>(UiState.Loading)
	val userList = _userList.asStateFlow()

	private val _

	// to do something . . .
}
```

StateFlow는 SharedFlow의 자식 클래스로 ..

https://myungpyo.medium.com/stateflow-%EC%99%80-sharedflow-32fdb49f9a32

### Flow를 StateFlow/SharedFlow로 변환하기

#### 총총 SharingStarted의 종류

## CallbackFlow

## Channel

### Channel

### BroadcastChannel

## Future

## repeatOnLifecycleLaunchWhen

~~

## References
