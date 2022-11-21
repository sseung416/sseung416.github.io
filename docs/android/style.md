---
layout: default
title: style
parent: Android
nav_order: 2
---

# Style

이것 저것
{: .fs-6 .fw-300 }

## Table of contents

{: .no_toc .text-delta }

1. TOC
   {:toc}

---

## 총총

### 암튼 대충 어쩌구

---

## TextView

### Drawable 추가하기 (programmatic)

각각 파라미터 값음 left, top, right, bottom임  
원하는 위치에 설정하면 됨

```kotlin
textView.setCompoundDrawablesWithIntrinsicBounds(img, 0, 0, 0)
```

### underline 넣기

```

```

---

## ViewPager

### 현재 페이지 변경하기 (다른 페이지로 슬라이드하기)

```kotlin
viewPager.setCurrentItem(index)
```

### 사용자의 스와이프 막기

```kotlin
viewPager.setUserInputEnabled(false);
```

### indicator 설정하기

### ViewPager1과 ViewPager2의 차이점

---

## TextInputLayout / TextInputEditText

### underline 없애기

```xml
<TextInputLayout
   ...
   app:boxStrokeWidth="0dp" />
```

### hint floating animation 없애기

```xml
<TextInputLayout
   ...
   app:hintEnabled="false" />

```

### radius 변경하기

```xml
<TextInputLayout
   ...
   app:boxCornerRadiusBottomEnd="10dp"
   app:boxCornerRadiusTopEnd="10dp"
   app:boxCornerRadiusBottomStart="10dp"
   app:boxCornerRadiusTopStart="10dp" />

```

### password hide/show icon 표시하기

```xml
<TextInputLayout
   ...
   app:passworddToggleEnabled="true" />
```

`app:passwordToggleDrawable` - toggle icon drawable 변경 (selector로 설정)
