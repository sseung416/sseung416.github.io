---
layout: default
title: Compose
parent: Android
has_children: true
permalink: /docs/android/compose
---

# Compose

- 개념
- Lifecycle (+ keys)
- State와 상태 호이스팅, remember

---

## [개념](https://developer.android.com/jetpack/compose/mental-model#any-order)

### 선언형 UI

기존 XML과 같은 명령형 객체 지향 UI 도구들은 레이아웃을 트리 형태로 인스턴스화하여 UI를 초기화하여, 앱 로직과 위젯이 상호작용 할 수 있도록 getter/setter 메서드를 노출하는 형태로 구성되어 있다.

Compose는 위젯이 대부분 Stateless 상태로, setter/getter 함수 뿐만 아니라 위젯 자체가 객체로 노출되지 않는다.



### Composable 함수의 특징
- **순서와 관계없이 실행될 수 있음**
- **동시에 실행될 수 있음**<br/>UI의 변경은 반드시 메인 스레드에서 작업했어야 했지만, Composable 함수의 경우 Background thread pool 내에서도 문제없이 실행 가능함.
- **UI는 최소한으로 변경됨**
<br/>뷰의 전체가 바뀌는 것이 아니라, 데이터가 바뀐 부분만 확인해서 그 부분만 자동으로 변화함.
- **매우 자주 실행될 수 있음**
<br/>경우에 따라 UI 애니메이션의 모든 프레임에서 실행될 수 있음. 따라서 비용이 많이 드는 작업을 실행할 때는 mutableStateof나 LiveData를 사용해 다른 스레드에서 작업하도록 해야 함.


### Compose의 장단점

컴포즈를 직접 사용하면서 느낀 장단점

장점
- 확실히 재사용성이 높아진다
- XML과 코틀린 코드 상 이동이 없어 번거로움이 줄었다
- 빌드 속도가 빨라진다고 하네요 (체감하진 못함)

단점
- 익숙하지 않은 것의 러닝커브: 기존 ConstraintLayout에 절여진 내 뇌가 Row, Column에 익숙해지기에는 많은 시간이 걸렸다.. 
- LiveEdit 기능.. 라이브가 맞나요? : 느리고 반영하려면 계속해서 리빌드 해줘야한다는 점이 불편하다
- Compose를 지원하지 않는 라이브러리들
- drawable resource의 부재: selector 등과 같은 경우, 클릭 여부에 따라 직접적으로 drawable을 계속해서 바꿔줘야 한다 (여기서 신선한 충격을 맛봄)

---



## [Composable의 Lifecycle](https://developer.android.com/jetpack/compose/lifecycle)

Composable의 생명주기는 `Initial Composition`, `Recomposition`, `Decomposition` 3가지 상태로 구성되어 있다.
- **Initial Composition(초기 컴포지션)**
<br/>Composition이 처음 생성될 때 호출됨.
- **Recomposition(리컴포지션/재구성)**
<br/>`State<T>` 객체가 변경되면 트리거됨. UI의 데이터가 변경될 때 이 상태가 실행되면서 업데이트됨!
- **Decomposition**
<br/>Composition이 삭제될 때 호출됨.
  
<br/>

> **🤚 잠깐! 헷갈리는 Composition과 Composable 용어 정리**
> - `Composition` : Composable를 실행할 때 빌드한 UI로, 여러 Composable은 하나의 Composition에 포함되어 있음.
> - `Composable` : 각각의 뷰를 구현한 컴포즈의 (구성가능한) 함수!

<br/>

아래의 이미지를 참고하면서 Composable의 lifecycle을 이해해보자.

| ![Image](https://developer.android.com/static/images/jetpack/compose/lifecycle-composition.png) |
|:--:|
| *Reference - Android Developer* |

위와 같이 처음 컴포지션을 시작하고 0회 이상 `recomposition` 후 컴포지션이 종료된다.  
데이터의 변경이 없다면 `recomposition`이 발생하지 않을 수도 있고 여러 번의 변경이 있다면 여러 번 `recomposition`이 발생한다  

또, 한 번 호출된 `Composable`은 데이터가 변경되지 않은 경우 `recomposition` 하지 않는다.
```kotlin
@Composable
fun LoginScreen(showError: Boolean) {
    if (showError) {
        LoginError()
    }
    LoginInput()
}

@Composable
fun LoginInput() { /* ... */ }
```

| ![Image](https://developer.android.com/static/images/jetpack/compose/lifecycle-showerror.png) | 
|:--:|
| *Reference - Android Developer* |

`showError` 값이 false 였다가 true가 된 경우, `LoginInput` 인스턴스는 두 번 호출되었지만,  
데이터의 변경 없으니 `recomposition`되지 않으며 `LoginError` 인스턴스만 `recomposition`되어 화면에 뷰를 추가한다.


### 예제와 함께 key를 활용해 야무지게 재활용하기

```kotlin
@Composable
fun MoviesScreen(movies: List<Movie>) {
    Column {
        movies.foreach { movie ->
            key(movie.id) { // Unique한 값
                MovieOverview(movie)
            }
        }
    }
}
```

| ![Image](https://developer.android.com/static/images/jetpack/compose/lifecycle-newelement-top-keys.png) |
|:--:|
| *Reference - Android Developer* |

아이템이 추가될 때마다 리스트의 가장 윗 부분에 recomposition된다.  

> 🔑 **Key**  
> 각 항목의 **키의 defualt 값**은 그 위치를 기준으로 **position 값**이 지정됨.  
> 하지만 **데이터가 추가/삭제됨으로써 위치 값이 바뀌면 문제**가 생길 수 있음.  
>   
> 이러한 문제점을 방지하기 위해서는 key 매개변수를 통해 각 아이템마다 **unique한 값을 할당**해주어야 함.  
> key를 할당해주면 **각 항목에 대한 일관성**을 유지할 수 있고, Composition에서 **해당 인스턴스를 인식**할 수 있음!  
> 
> RecyclerView의 setHasStableIds(ture) 설정한 것처럼 값이 변경되면 그 아이디 값에 맞는 애만 알아서 변환해주지 않을까...? (어림짐작)


---

## State

XML UI를 사용할 때는 뷰에 LiveData를 연결해주어서 데이터가 변경될때마다 즉각적으로 뷰에 반영해주었다면,  
Composable은 선언적 UI라 한 번 코드를 실행 후에는 자동으로 데이터를 업데이트 하지 못한다!  
수정하면 Exception이 뜨면서 앱이 종료된다!  
따라서 Composable를 업데이트 하려면 동일한 Composable를 한 번 더 출력해야 한다!

### Stateful vs Stateless

> 상태를 갖는(remember를 사용하는) Composable를 Stateful, 갖지 않는 Composable을 Stateless라고 함.

### 상태 호이스팅
Composable을 stateless로 만들기 위해 컴포저블의 호출자를 옮기는 패턴(...)을 뜻함.

### remember... me..

> 📝 Remember  
> remember는 Recomposition될 때 initial composition할 때 저장한 값을 보존함!  
> 즉, 객체를 새로 생성하지 않고, 기존에 저장된 데이터를 반환!
> remember은 객체를 해당 composition에 저장하고, remember를 호출한 composable이 composition에서 삭제되면 그 객체를 잊음.

**언제 사용해야 할까?**  
시간이 오래 걸리는 비용이 큰 작업을 수행해야 할 때나 recomposition 상태가 되어도 데이터를 보존해야하는 경우(버튼을 클릭했을 때 ripple 변경이나 다이얼로그 표시/숨김 등..), remember 변수를 사용에 처리하면 좋음.

```kotlin
var count by remember { mutableStateOf(0) }
```

하지만 remember 변수는 화면 회전이나 다시 그려지는 configure change의 상황이 발생하면 값이 유지되지 않기 때문에 실제 코드에서 사용할 때는 remeberSaveable 변수를 사용해 구현해야한다.

<!-- ### MutableStateOf<T> -->

```kotlin
@Composable
fun RememberTest() {
    var count by remeberSaveable { mutableStateOf(0) }

    Box(modifier = Modifier.fillMaxSize()) {
        Button(onClick = { count++ }, modifier = Modifier.align(Alignment.Center)) {
            Text(text = "Clicked $count")
        }
    }
}
```


---


## [Icon vs Image](https://stackoverflow.com/questions/69349400/what-is-the-difference-between-an-icon-and-an-image-in-android-jetpack-compose)

> 👉 지원하는 Material 아이콘을 사용할 것이면 `Icon`, 내 Drawable을 사용할 것이면 `Image`

### Icon
- Material에서 제공하는 아이콘을 사용할 수 있음. (기본 사이즈 24dp)

### Image
- 사용자의 Drawable를 사용하려면 Image를 이용해야 함.


---

## ToggleButton 만들기

checked에 따라 비교하여 직접 Drawable를 넣어줘야 한다.

```kotlin
@Composable
private fun PauseAndStartToggleButton(
    checked: Boolean,
    onCheckedListener: ((Boolean) -> Unit)
) {
    IconToggleButton(
        checked = checked,
        onCheckedChange = { onCheckedListener?.invoke(it) },
        modifier = Modifier.size(FlagSize)
    ) {
        val drawableRes =
            if (checked) R.drawable.episode_flag_pause else R.drawable.episode_flag_play

        Image(
            painter = painterResource(id = drawableRes),
            contentDescription = "pause and start",
        )
    }
}
```

---

## Dialog 구현하기

### 기본

간단하게 베이스 다이얼로그 선언하고...
```kotlin
@Composable
fun AppAlertDialog(
    onDismissRequest: () -> Unit = {},
    content: @Composable () -> Unit
) {
    Dialog(onDismissRequest = onDismissRequest) {
        content.invoke()
    }
}
```

아래처럼 커스텀한다.

```kotlin
@Composable
fun 
```


### Dialog

### Dialog 숨기기