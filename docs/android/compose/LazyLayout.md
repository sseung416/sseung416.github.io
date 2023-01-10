---
layout: default
title: LazyLayout
parent: Compose
grand_parent: Android
---

# Compose의 Lazy Layout

부제: Compose로 RecyclerView 구현하기

---

## Compose에서 RecyclerView 구현하기

Lazy 컴포넌트는 RecyclerView와 같이, 화면상에만 보이는 요소만 출력하는 컴포저블이다. 기존의 Column과 같은 레이아웃을 사용하면 화면 표시 여부와 관계 없이, 모든 아이템이 출력되므로 성능상으로 문제가 생길 수 있다. Lazy 레이아웃을 사용하면 화면에 보여지는 컴포저블만 출력하기 때문에 화면 로딩 시간 및 성능을 개선시킬 수 있다.

Lazy 컴포넌트는 화면에 나타나는 뷰만 출력한다는 점에서 RecyclerView와 유사성을 띠지만, 실제로 동일하지는 않다. RecyclerView는 화면에서 가려진 뷰들을 Recycler Pool에 저장하여 나중에 재사용하지만, Lazy 컴포넌트는 재사용의 과정 없이 그냥 다시 그려진다.  

<!-- 
재사용하는 것보다 다시 그리는 것이 성능상으로 더 좋기 때문이다. 
어떠한 면에서 좋은지 찾아보기!!
 -->

Compose에서 RecyclerView를 구현하는 방법은 매우 간단하다! Adapter 및 ViewHolder의 구현 없이 아래의 로직만 정의해주면 끝이다. 
```kotlin
fun UserList(list: List<User>) {
    LazyColumn {
        items(list) { item ->
            UserCard(item)
        }
    }
}
```
Lazy 컴포넌트 안에 itemIndexed와 같은 LazyListScope DSL의 함수를 삽입해주면 된다. LazyListScope DSL은 아래에서 자세히 설명한다.


### LazyListScope DSL

LazyListScope DSL은 Lazy 레이아웃 안에서 아이템 컴포저블을 구성할 수 있게하는 여러 함수들을 제공한다. 아이템뷰를 정의하고 값을 세팅해주는 RecyclerView의 ViewHolder와 비슷한 역할을 한다고 생각하면 된다!  
<!-- Lazy Composable은 일반적인 Composable과 다른데, 이 내부에 일반 Composable를 사용할 수 있도록 하는게 LazyListScope의 역할인 것 같다(...) -->

아무튼 LazyListScope DSL의 종류는 다음과 같다.

- **item**
<br/> 단일 항목을 추가한다. 내부에 Composable 요소를 추가하면 그만큼 출력해준다.
    ```kotlin
    LazyColumn {
        item {
            UserCard(name = "John")
            UserCard(name = "Jina")
        }
    }
    ```

- **items**
<br/> 여러 항목을 추가한다. 파라미터로 추가할 아이템의 개수를 입력하면 그 만큼의 아이템이 출력된다.
    ```kotlin
    LazyColumn {
        items(10) { index ->
            UserCard(name = "John - $index")
        }
    }
    ```
    List와 같은 컬렉션을 파라미터로 넘길 수도 있다. 아마 실제로 코드를 구현할 때는 이 방식을 가장 많이 사용하지 않을까 싶다.
    ```kotlin
    @Composable
    fun UserList(list: List<User>) {
        LazyColumn {
            items(list) { user ->
                UserCard(user)
            }
        }
    }
    ```

- **itemIndexed**
<br/> item과 index 값을 둘 다 제공한다. index 값이 필요한 경우 이 scope를 사용하면 된다.
    ```kotlin
    @Composable
    fun ContentList(list: List<Content>) {
        LazyColumn {
            itemIndexed(list) { index, content
                ContentCard(index, content)
            }
        }
    }
    ```

<br/>

이외에도 Lazy 레이아웃 내부에는 여러 개의 scope를 선언하여 레이아웃을 구성할 수도 있다.

```kotlin
LazyColumn {
    item {
        Header()
    }

    items(5) { index ->
        Content(content = "content $index")
    }
}
```



## LazyColumn, LazyRow, LazyGrid




### 알아두면 좋은 옵션들!

## Lazy Layout을 사용할 때 유의해야 할 점




---

## References

- https://developer.android.com/jetpack/compose/lists