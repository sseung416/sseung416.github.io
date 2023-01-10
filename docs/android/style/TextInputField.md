---
layout: default
title: TextInputField
nav_order: 1
parent: style
grand_parent: Android
---

# TextInputField 커스텀하기 (TextInputLayout, TextInputEditText)

---

## TextInputLayout Underline Custom

| 속성          | 태그                      | default                                           |
| ------------- | ------------------------- | ------------------------------------------------- |
| Color         | app:boxStrokeColor        | ?attr/colorOnSurface, ?attr/colorPrimary(focused) |
| ErrorColor    | app:boxStrokeErrorColor   | ?attr/colorError                                  |
| Width         | app:boxStrokWidth         | 1dp                                               |
| Focused width | app:boxStrokeWidthFocused | 2dp                                               |

### underline 삭제

```xml
<TextInputLayout
   ...
   app:boxStrokeWidth="0dp" />
```

---

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
