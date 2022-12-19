---
layout: default
title: Exception Handling
parent: Java
---

# try catch를 사용한 예외 처리

---

## Exception, 예외 처리

자바에서는 잘못된 코드 작성이나 조작을 발생하는 프로그램 오류를 예외(`Exception`)라고 한다.  
예외는 발생 즉시 프로그램을 종료 시키지만, `try-catch` 등의 예외 처리를 통해 프로그램을 종료시키지 않고 정상적으로 동작하게 할 수 있다.  
자바 프로그램을 개발할 때 자주 맞딱뜨리는 `NullPointerException`, `Illegalargumentexception` 등과 같은 자바의 모든 예외는 `java.lang.Exception`을 부모로 한다.  

예외의 종류는 `check exception`, `runtime/uncheck exception`으로 나뉜다.

- **check exception**
<br/>runtime/uncheck exception이 아닌 모든 Exception을 의미한다.
- **runtime/uncheck exception**
<br/>실행 시 실패할 가능성이 있는 Exception을 RuntimeException이라고 한다.
그리고 이러한 `RuntimeException`을 상속한 모든 `Exception`을 `runtime/uncheck exception`이라고 한다.
또한, 컴파일 시에 검사를 하지 않기 때문에 `uncheck exception`이라고도 불린다.



### 비슷해보이는 Exception과 Error

`Exception`과 `Error` 둘 다 프로그램 오류에 속하여, 발생 시 프로그램이 비정상적으로 종료한다는 점에서 유사하다고 느낄 수 있지만 큰 차이가 있다.

- **Error**
<br/> 자바 프로그램 밖에서 발생한 예외를 의미한다.  
시스템 레벨에서 발생하여 개발자가 어떠한 조치를 할 수 없는 수준이 여기에 해당된다. (ex: 메인보드 문제와 같이 외부 장치 문제, 시스템 과부화 등...)  
`Error`는 예외 처리가 불가능하고 발생 시 반드시 프로그램이 종료하게 된다.  
이러한 `Error`를 미리 방지하기 위해 `Exception`의 상황을 만들어 handling을 통해 처리하도록 한다.

- **Exception**
<br/> 프로그램 동작 중 예기치 않은 이상이 발생했을 때 수행 중인 프로그램이 영향을 받는 것을 의미한다.  
이 경우, 예외가 발생하더라도 handling을 통해 적절히 대처하면 비정상적인 종료를 사전에 방지할 수 있다.

> **Exception**은 해당 쓰레드에만 영향을 주지만, **Error**는 그 프로그램 전체인 프로세스에 영향을 준다.

<br/>

## try-catch를 통해 예외 처리하기

예외 처리는 아래와 같이 `try-catch` 구문을 통해 처리할 수 있다.  
try 구문의 특정 부분에서 오류가 발생하면, 그 아래 코드는 실행되지 않고 catch 구문의 코드를 실행한다.

```java
public void nullPointerException() {
    int count;
    
    try {
        count ++; // NPE 발생!
        Systme.out.println("count 값 증가");
    } catch (NullPointerException e) { // 발생되는 예외의 해당되는 Exception 클래스를 표기해주면 된다
        // 오류가 발생하는 부분에서 출력이 필요하다면, System.err를 사용하도록 하자
        // 실제 콘솔화면에서는 별 차이 없지만, IDE에서는 출력 결과를 다른 색으로 표시한다
        System.err.println("값이 초기화되지 않았어요!");
    }

    Systme.out.println("함수 종료");
}
```

> [ 출력결과 ]
>
> 값이 초기화되지 않았어요!  
> 함수 종료

### 다중/멀티 catch

복잡한 자바 프로그램을 개발할 땐, 단순히 하나의 예외 뿐만 아니라 다양한 예외가 발생할 수 있다.

다음과 같이 여러 개의 catch 블럭을 선언하여 다양한 예외에 맞게 처리할 수 있으며,  
자바7부터는 하나의 catch 블럭에 여러 개의 예외를 처리할 수 있게 되었다.  
하나의 catch 블록에 여러 개의 `Exception`을 처리할 때는 `|`를 통해 `Exception`을 연결해주면 된다.

```java
public void mutipleCatch() {
    int count;
    
    try {
        count ++; 
        Systme.out.println("count 값 증가");
    } catch (NullPointerException e) {
        System.err.println("값이 초기화되지 않았어요!");
    } catch (ArrayIndexOutOfBoundsException | NumberFormatException e) {
        System.err.println("또 다른 오류가 발생했어요.");
    }

    Systme.out.println("함수 종료");
}
```

<br/>

## 항상 실행되는 finally

finally는 try 구문에서의 오류 발생 여부와 관계 없이 항상 실행하는 구문을 의미한다.

```java
public void nullPointerExceptionWithFinally() {
    int count;
    
    try {
        count ++; // NPE 발생!
        Systme.out.println("count 값 증가");
    } catch (NullPointerException e) {
        System.err.println("값이 초기화되지 않았어요!");
    } finally {
        Systme.out.println("finally는 항상 실행됨");
    }

    Systme.out.println("함수 종료");
}
```

> [ 출력결과 ]
> 
> 값이 초기화되지 않았어요!  
> finally는 항상 실행됨  
> 함수 종료

<br/>

## 자동으로 자원 해제하기: try-catch-resources

파일 입출력 등 예외 발생 여부와 상관없이 finally를 통해 리소스를 닫아주었다면, 자바 7부터는 finally 구문에 명시하지 않더라도 다음과 같은 방법으로 자동으로 리소스를 닫아준다.  

```java
// before java 7
public void withoutTryCatchResources() {
    FileInputStream fis;
    try {
        fis = new FileInputStream("file.txt");

        // 파일을 읽는 어떠한 작업 중..
    } catch(IOException e) {
        // 오류 처리 중...
    }
}
```

```java
// before java 7!
public void tryCatchResources() {
    try (FileInputStream fis = new FileInputStream("file.txt")) {
        // 파일을 읽는 어떠한 작업 중..
    } catch(IOException e) {
        // 오류 처리 중...
    }
}
```

try(...) 안에 선언된 변수는 try 구문에서 사용할 수 있으며, try 구문을 벗어나면 자동으로 해당 객체의 close() 메서드를 자동으로 호출하여 자원을 해제해준다.

하지만 모든 객체를 자동으로 close()시켜 주진 않고, AutoClosable를 구현한 객체만 close() 메소드가 호출된다.


### 직접 AutoClosable 구현하기

아래와 같이 직접 AutoClosable을 구현하여 사용할 수도 있다.

```java
public class CustomResource implements AutoClosable {

    public void doSomething() {
        // 어떠한 작업을 하는 중 . . .
    }
    
    @Override
    public void close() throws Exception {
        // 어떠한 리소스를 닫는 작업 . . .
    }
}
```

```java
public static void main(String[] args) {
    try (CustomResource resource = new CustomResource()) {
        resource.doSomething();
    } catch (Exception e) {
        // . . .
    }
}
```


## 예외 던지기: throws

throws 키워드를 통해 해당 메소드를 호출한 곳으로 예외를 넘길 수 있다.

```java
public void test() {
    try {
        throwNullPointerException();
    } catch (NullPointerException e) {
        System.err.println("NPE 발생!");
    }
}

public void throwNullPointerException() throw NullPointerException {
    int count;
    count ++;
    System.out.println("count 값 증가");
}
```

> [ 출력결과 ]
>
> NPE 발생!

<br/>

## Exception Custom하기

사용자가 직접 Exception을 정의하여 사용할 수 있다.

```java
public class VerifyException extends RuntimeException {

    public VerifyException(String message) {
        super(message);
    }
}
```

```java
public static void main(String[] args) {
    try {
        verifyEmail("asdfasd1");
    } catch (VerifyException e) {
        Systme.err.println(e.getMessage)
    }
}

public static void verifyEmail(String value) throw VerifyException {
    if (value == null || value.trim().isEmpty()) {
        throw new VerifyException("이메일을 입력해주세요.");
    } else if (value.contains('@')) {
        throw new VerifyException("이메일이 아닙니다.");
    }
}
```

> [ 출력결과 ]
> 
> 이메일이 아닙니다.

<br/>

## Reference
- 자바의 신
- https://gyoogle.dev/blog/computer-language/Java/Error%20&%20Exception.html
- http://blog.eomdev.com/java/2016/03/31/exception.html
- https://codechacha.com/ko/java-try-with-resources/