# Compose의 Layout

- Row
- Column
- Box

---

## Row

### Row Alignment

수직 정렬(horizontalAlignmet)를 지원한다.
 
- Alignment.Top : 위쪽 정렬
- Alignment.CenterVertical : 중앙 정렬
- Alignment.Bottom : 아래쪽 정렬


https://kotlinworld.com/183

## Column

## Box

## [Compose로 ConstraintLayout 구현하기](https://developer.android.com/jetpack/compose/layouts/constraintlayout?hl=ko)

XML 방식으로 레이아웃을 구성할 때와 다르게, Compose는 여러 개의 뷰를 중첩하더라도 성능상 문제 없다.  
하지만 코드 가독성을 위해서나 가이드라인, 체인 등 ConstraintLayout만의 기능이 필요할 때 사용한다.  


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


<!-- 대충 정렬도. 적으라고. -->