---
layout: default
title: RecyclerView
parent: ../Android
---

# RecyclerView

## ListView vs RecyclerView

기존 ListView는 아래와 같은 단점이 있었음.
- 스크롤 시 버벅거림: 재활용 없이 매번 아이템을 생성할 때마다 findViewById() 메서드를 호출하는 것은 매우 큰 비용.
- 기본 애니메이션 지원 없음
- 수직 스크롤만 가능: RecyclerView는 Linear뿐만아니라 Grid, Staggered 등 다양한 스크롤 기능을 지원함.

이러한 단점을 개선하고자 나온 것이 RecyclerView!

기존의 ListView는 화면에서 벗어난 뷰를 삭제하고 새로 생성하는 반면, RecyclerView는 처음 생성한 뷰를 계속해서 재사용함.  
뷰를 재사용함으로써 앱의 응답 및 전력 소모를 감소하며, 성능이 개선됨.


## RecyclerView 구성요소
RecyclerView는 Adapter, LayoutManager, ItemAnimator, ViewHolder로 구성됨
### Adapter


### LayoutManager



LayoutManager의 종류는 다음과 같음.

- LinearLayoutManager
- GridLayoutManager
- StaggeredLayoutManager

### ItemAnimator

### ViewHolder


</br>

## RecyclerView Adapter의 주요 메서드들

### 구현할 때 상속받아야하는 메서드

RecyclerView Adapter를 생성할 때는 아래와 같은 세 메서드를 상속받아야 함.

- `onCreateViewHolder()`  
: ViewHolder를 새로 생성해서 반환함. 각 아이템의 껍데기인 ViewHolder를 만들어 줌.

```kotlin
override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): MyViewHolder {
    val inflater = LayoutInflater.from(parent.context);
    val binding = ItemMyBinding.inflate(inflater);
    return MyViewHolder(binding);
}
```

- `onBindViewHolder()` : ViewHolder에 데이터를 할당해줌.
```kotlin
override fun onBindViewHolder(holder: MyViewHolder, position: Int) {
    holder.bind(list[position]);
}
```

- `getItemCount()` : 리사이클러뷰 아이템의 총 개수를 반환해줌.
```kotlin
override fun getItemCount(): Int {
    return list.size;
}
```


### 아이템 변경을 알리는 메서드

- `notifyItemChange()`
- `notifyItemInserted()`
- `notifyItemRemoved()`
- `notifyItemMoved()`


### 등등~ 알아놓아야할 메서드들
- `adapter.setHasFixedSize(boolean)`   
: RecyclerView의 크기가 일정하다는 것을 명시함.  
<!-- 아이템 항목을 새로 추가할 때마다 RecyclerView의 크기가 변경되는데, 그때마다 크기 측정 및 그리기가 반복되기 때문에 --> 

- `adapter.setHasStableIds(boolean)`  
: 어댑터의 아이템들이 각 고유한 ID 값을 가지게 설정함.  
바인드 시 onBindViewHolder()를 최적화를 통해 성능을 향상시킬 수 있음.  
`setHasStatableIds(boolean)`를 설정해줄 때는 반드시 어댑터에서 `getItemId(int)`를 구현해야 작동함.  


</br>

## 아이템 재활용 원리
(출처ㅣ Microsoft)
![image](https://miro.medium.com/max/1400/0*9boKedNTnDTpNEsd.webp)

RecyclerView는 한 번에 모든 데이터를 아이템 뷰에 할당하지 않음.  
화면에 보여지는 아이템 뷰의 수만큼만 데이터를 할당하고, 사용자가 스크롤할 때마다 해당 아이템 레이아웃을 다시 재활용함.

해당 아이템 뷰가 화면에 더 이상 표시되지 않을 때, 이 아이템 뷰는 `Scrap View`가 됨.

#### ScrapView?
- RecyclerView는 ScrapView 전용 **Scrap Heap 캐싱 시스템**이 존재함.
  - Scrap Heap: 뷰를 어댑터로 다시 전달하지 않고도, 뷰를 LayoutManager를 통해 직접 반환할 수 있는 경량화된 컬렉션!
  - 데이터가 계속해서 ViewHolder에 연결되어 있기 때문에 이와 같은 시스템 구축이 가능함.
  - 배치된 뷰는 데이터와 뷰가 일시적으로 분리되지만, 동일한 레이아웃을 그릴 때 재사용됨.

Scrap Heap과 더불어 `Recycle Pool`이라는 또 다른 뷰의 캐싱 시스템이 있음.

> **Recycle Pool** : 데이터를 ViewHolder에 다시 연결하거나 바인딩한 다음 LayoutManager에 반환할 수 있도록 항상 Adapter에 전달됨..........



- Adapter가 item layout을 inflate할 때마다 해당 ViewHolder도 함께 생성됨.
- ViewHolders는 findViewById를 사용해 inflate된 item layout 파일 내부의 뷰에 대한 참조를 가져옴.
- 이 참조는 레이아웃을 재활용해 새 데이터를 표시하고 로드하는데 사용함.

## 성능 향상하기



## References
- https://medium.com/1mgofficial/how-recyclerview-works-internally-71290de5d2c4
- https://n1lesh.medium.com/understanding-recyclerview-part-1-the-basics-a7bd07cfae93