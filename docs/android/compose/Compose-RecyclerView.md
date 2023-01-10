
## Compose LazyColumn으로 RecyclerView 구현하기

- LazeColumn, LazyRow, LazyGrid
- item, items, itemsIndexed

---

compose에는 RecyclerView가 없다!  
뷰를 재활용하는 비용보다 새로 그리는 비용이 더 낫다!  
그래서 재활용따위 안 하는 개머싯는 Compose, 

### LazyColumn, LazyRow, LazyGrid

> 화면에 보여지는 Composable만 표시하는 스크롤이 가능한 Column, Row, Grid를 뜻한다.
> 즉, `RecyclerView`를 구현하기 적합한 layout이라 할 수 있다!

### item, items, itemsIndexed

- **item** : LazyColumn 내부에 Composable를 아이템을 직접적으로 넣을 때 사용.
- **items** : Composable를 반복해서 나타내고 싶을때 사용.
- **itemsIndexed** : 해당 리스트를 반복하여 출력. 현업에서는 얘가 가장 많이 쓰임.


```kotlin
@Composable
fun UserRecyclerView(messageList: List<String>) {
    LazyColumn {
        itemsIndexed(messageList) { index, item ->
            MessageCard(message = item)
        }
    }
}

// recyclerView Item
@Composable
fun MessageCard(message: String) {
    Box(Modifier.fillMaxSize().padding(16.dp)) {
        Text(
            text = message,
            modifier = Modifier.align(Alignment.Center)
        )
    }
}
```



---