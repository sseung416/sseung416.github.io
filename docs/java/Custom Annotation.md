# 커스텀 어노테이션 만들긔~

---

## 어노테이션이 뭐예요

Annotation은 주석처럼 코드에 달아 클래스에 특별한 의미를 부여하거나 기능을 주입할 수 있다.

- 자바5부터 등장.
- 비즈니스 로직에는 영향을 주지 않지만 소스코드의 구조 변경이 가능하다.

### 미리 정의되어 있는 어노테이션

- @Override
- @...
- @



## 어노테이션 직접 만들기

### 메타 어노테이션

메타 어노테이션은 어노테이션을 선언할 때 사용하는 어노테이션을 의미한다.
메타 어노테이션은 `@Target`, `@Retention`, `@Documented`, `@Inherited`의 4가지로 구성된다.

- **@Target**
<br/>어노테이션을 적용할 대상을 정한다. 대상의 종류는 아래와 같다.
(대충 표)

- **@Retention**
<br/>언제까지 어노테이션의 정보가 유지되는지 정한다.


- **@Documented**
<br/>해당 어노테이션의 정보가 Javadocs(API) 문서에 포함된다는 것을 명시한다.

- **@Inherited**


```java

```


## Reference
- 자바의 신