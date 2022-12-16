---
layout: default
title: Compose
parent: Android
---

# Compose

## [개념](https://developer.android.com/jetpack/compose/mental-model#any-order)

### 선언형 UI

기존 XML과 같은 명령형 객체 지향 UI 도구들은 레이아웃을 트리 형태로 인스턴스화하여 UI를 초기화하여, 앱 로직과 위젯이 상호작용 할 수 있도록 getter/setter 메서드를 노출하는 형태로 구성되어 있음.

Compose는 위젯이 대부분 Stateless 상태로, setter/getter 함수 뿐만 아니라 위젯 자체가 객체로 노출되지 않음.



### Composable 함수의 특징
- 순서와 관계없이 실행될 수 있음
- 동시에 실행될 수 있음  
: UI의 변경은 반드시 메인 스레드에서 작업했어야 했지만, Composable 함수의 경우 Background thread pool 내에서도 문제없이 실행 가능함.
- UI는 최소한으로 변경함  
: 데이터가 바뀐 부분만 확인해서 그 부분만 자동으로 변화함.
- 매우 자주 실행될 수 있음  
: 경우에 따라 UI 애니메이션의 모든 프레임에서 실행될 수 있음. 따라서 비용이 많이 드는 작업을 실행할 때는 mutableStateof나 LiveData를 사용해 다른 스레드에서 작업하도록 해야 함.


---

## State

XML UI를 사용할 때는 뷰에 LiveData를 연결해주어서 데이터가 변경될때마다 즉각적으로 뷰에 반영해주었다면,  
Composable은 선언적 UI라 한 번 코드를 실행 후에는 자동으로 데이터를 업데이트 하지 못함!  
업데이트 하려면 동일한 Composable를 한 번 더 출력해야 함.

### Stateful vs Stateless

> 상태를 갖는(remember를 사용하는) Composable를 Stateful, 갖지 않는 Composable을 Stateless라고 함.

### 상태 호이스팅
Composable을 stateless로 만들기 위해 컴포저블의 호출자를 옮기는 패턴(...)을 뜻함.


---

## 눈물의 remember 학습

### [Composable의 Lifecycle](https://developer.android.com/jetpack/compose/lifecycle)

Composable의 생명주기는 `Initial Composition`, `Recomposition`, `Decomposition` 3가지 상태로 구성되어 있음.
- Initial Composition(초기 컴포지션): Composition이 처음 생성될 때.
- Recomposition(리컴포지션/재구성): `State<T>` 객체가 변경되면 트리거됨. UI의 데이터가 변경될 때 이 상태가 실행되면서 업데이트됨!
- Decomposition: Composition이 삭제될 때.

> 잠깐! 헷갈리는 Composition과 Composable 용어 정리
> - `Composition` : Composable를 실행할 때 빌드한 UI. 여러 Composable은 하나의 Composition에 포함되어 있음.
> - `Composable` : 각각의 뷰를 구현한 컴포즈의 구성가느한 함수!

![Image](https://developer.android.com/static/images/jetpack/compose/lifecycle-composition.png)

위와 같이 처음 컴포지션을 시작하고 0회 이상 recomposition 후 컴포지션이 종료됨.  
데이터의 변경이 없다면 recomposition이 발생하지 않을 수도 있고 여러 번의 변경이 있다면 여러 번 recomposition됨.  

또! 한 번 호출된 Composable은 데이터가 변경되지 않은 경우 recomposition 하지 않음!
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
![Image](https://developer.android.com/static/images/jetpack/compose/lifecycle-showerror.png)

위의 경우, LoginInput 인스턴스는 두 번 호출되었지만 데이터의 변경 없으니 recomposition되지 않으며, LoginError만 recomposition되어 화면에 뷰를 추가함.

</br>

#### 예제와 함께 key를 활용해 야무지게 재활용하기

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

![Image](https://developer.android.com/static/images/jetpack/compose/lifecycle-newelement-top-keys.png)

아이템이 추가될 때마다 리스트의 가장 윗 부분에 recomposition됨.  

> 🔑 Key  
> 기본적으로 각 항목의 키는 그 위치를 기준으로 position 값이 지정됨.  
> 하지만 데이터가 추가/삭제됨으로써 위치 값이 바뀌면 문제가 생길 수 있음.  
> 이러한 문제점을 방지하기 위해서는 key 매개변수를 통해 각 아이템마다 unique한 값을 할당해주어야 함.  
> key를 할당해주면 각 항목에 대한 일관성을 유지할 수 있고, Composition에서 해당 인스턴스를 인식할 수 있음!  
> 
> RecyclerView의 setHasStableIds(ture) 설정한 것처럼 값이 변경되면 그 아이디 값에 맞는 애만 알아서 변환해주지 않을까...? (어림짐작)

<br/>

### remember... me..

> 📝 Remember  
> remember는 Recomposition될 때 initial composition할 때 저장한 값을 보존함!  
> 즉, 객체를 새로 생성하지 않고, 기존에 저장된 데이터를 반환!
> remember은 객체를 해당 composition에 저장하고, remember를 호출한 composable이 composition에서 삭제되면 그 객체를 잊음.

**언제 사용해야 할까?**  
시간이 오래 걸리는 비용이 큰 작업을 수행해야 할 때나 recomposition 상태가 되어도 데이터를 보존해야할 때, remember를 활용하면 좋음.


---


## [Icon vs Image](https://stackoverflow.com/questions/69349400/what-is-the-difference-between-an-icon-and-an-image-in-android-jetpack-compose)

> 정리: 지원하는 Material 아이콘을 사용할 것이면 Icon, 내 Drawable을 사용할 것이면 Image

#### Icon
- Material에서 제공하는 아이콘을 사용할 수 있음. (기본 사이즈 24dp)

#### Image
- 사용자의 Drawable를 사용하려면 Image를 이용해야 함.


---
## ToggleButton 만들기

checked에 따라 직접 비교해줘야 함.

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

## [Compose로 ConstraintLayout 구현하기](https://developer.android.com/jetpack/compose/layouts/constraintlayout?hl=ko)

XML 방식으로 레이아웃을 구성할 때와 다르게, Compose는 여러 개의 뷰를 중첩하더라도 성능상 문제 없음.  
하지만 코드 가독성을 위해서나, 가이드라인, 체인 등 기능이 필요할 때가 아래와 같이 ConstraintLayout을 사용함.

> 기본 compose library와 version이 다르니 주의하기~
```gradle
implementation "androidx.constraintlayout:constraintlayout-compose:1.0.1"
```

```kotlin
fun MyConstraintLayout() {
    ConstraintLayout {
        val (button, text) = createRefs()

        Button(
            onClick = {}, 
            modifier = Modifier.constrainAs(button) {
                top.linkTo(parent.top)
                start.linkTo(parent.start)
            }
        ) {
            Text(text = "button")
        }

        Text(
            text = "text"
            modifier = Modifier.constrainAs(text) {
                top.linkTo(button.top)
                start.linkTo(button.start)
            }
        ) 
    }
}
```