# Chapter 3. 함수 정의와 호출

## 3.1 코틀린에서 컬렉션 만들기

- 코틀린은 자체 컬렉션 기능을 제공하지 않아, 기존의 자바 컬렉션을 그대로 사용할 수 있다.
- 코틀린 컬렉션은 자바 컬렉션과 동일한 기능이지만, 더 많은 기능을 쓸 수 있다.

```kotlin
val set = hashSetOf(1, 7, 53)
val list = arrayListOf(1, 7, 53)
val map = hashMapOf(1 to "one", 7 to "seven", 53 to "fifty-three")

// javaClass를 이용해 객체가 어느 클래스에 속하는지 확인하기
>>> println(set.javaClass)
>>> class java.util.HashSet
```

<br>

## 3.2 함수를 호출하기 쉽게 만들기

> Java의 컬렉션 toString은 커스터마이징이 어렵지만 코틀린은 서드파티 없이 간단하게 구현 가능하다.

```kotlin
fun<T> joinToString(
    collection: Collection<T>,
    separator: String,
    prefix: String,
    postfix: String
) : String {
    val result = StringBuilder(prefix)
    for ((index, element) in collection.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }
    result.append(postfix)
    return result.toString()
}
```

- 위 함수는 제네릭하지만, 인자가 4개라서 호출이 번잡스럽다.
- 코틀린스럽게 개선해보자.

<br>

### 📌 이름 붙인 인자

> 함수 호출부의 가독성 개선하기

- 함수 호출을 외우거나 IDE에 의존하더라도, 호출부의 가독성은 여전히 부족
- 코틀린은 아래와 같이 함수 호출시에 인자의 이름을 명시할 수 있다
  ```kotlin
    joinToString(collection, separator = "", prefix = "", postfix = "")
  ``` 
- 단, 어느 하나라도 이름을 명시하면 혼동을 피하기 위해 나머지 인자도 꼭 이름을 명시해야 한다.
  
<br>

### 📌 디폴트 파라미터 값

> 함수를 오버로딩 할 경우, 그리고 인자가 일부 생략된 오버로드 함수를 호출할 경우의 모호성 개선하기

```kotlin
fun<T> joinToString(
    collection: Collection<T>,
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
) : String
```

- 코틀린은 함수 선언에서 파라미터의 디폴트 값을 지정할 수 있다
- 이름 붙인 인자를 사용할 경우, 메서드 시그니처의 인자 순서와 관계 없이 편하게 호출할 수 있다.
  ```kotlin
    >>> joinToStriing(list, postfix = ";", prefix = "$ ")
  ```
- 자바에는 디폴트 파라미터 개념이 없기에 코틀린을 자바에서 사용한다면 모든 인자를 명시해주어야 한다.
  - 단, `@JvmOverloads` 어노테이션을 이용하면 자바에서도 편하게 코틀린 함수를 호출할 수 있다.

<br>

### 📌 정적인 유틸리티 클래스 없애기: 최상위 함수와 프로퍼티

> Java의 모든 코드들은 클래스의 메서드로 작성되어야 하므로, 클래스가 단순한 정적 메서드를 모아두는 역할만 담당하게 되는 경우가 빈번하다.

- 코틀린은 함수를 소스 파일 최상단, 즉 다른 클래스의 밖에 위치시켜 클래스명을 생략하는 방식으로 import를 단순화할 수 있다.
  - 코틀린 컴파일러는 최상위 함수가 들어있는 소스 파일의 이름을 이용해 클래스를 생성한다.
  - 만약 최상위 함수가 포함되는 클래스 이름을 바꾸고 싶다면 `@JvmName` 어노테이션을 이용할 수 있다.

> 최상위 프로퍼티를 이용해 데이터를 클래스 밖에 위치시킬 수도 있다.

```kotlin
// 최상위 프로퍼티 선언
var opCount = 0
// 최상위 프로퍼티의 값 변경
fun performOperation() {
    opCount++
}
```

- 최상위 프로퍼티의 값은 정적 필드에 저장된다.
  - 이를 이용해 코드에 상수를 추가할 수도 있다!
- 최상위 프로퍼티도 접근자 메서드를 통해 자바 코드에 노출되지만, 겉으론 상수처럼 보이기에 자연스럽지 않다.
  - 따라서 `const` 변경자를 추가해, `public static fina`l 필드로 컴파일할 수 있다.
  ```kotlin
  /* 코틀린 */
  const val UNIX_LINE_SEPARATOR = "\n"
  /* 자바 */
  public static final String UNIX_LINE_SEPARATOR = "\n" 
  ```

<br>

## 메서드를 다른 클래스에 추가: 확장 함수와 확장 프로퍼티

> 기존 자바 API를 재작성하지 않고 코틀린으로 확장 가능하도록, 확장 함수를 사용할 수 있다.

```kotlin
package strings
// 수신 객체 타입 (String) = 수신 객체 (this)
fun String.lastChar(): Char = this.get(this.length - 1)

>>> println("Kotlin".lastChar())
>>> n
```
- `확장 함수`
  - 어떤 클래스의 멤버 메서드인 것처럼 호출할 수 있지만, 그 클래스의 밖에 선언된 함수.
  - 추가하려는 함수 이름 앞에 그 함수가 확장할 클래스의 이름을 덧붙여서 생성한다.
  - 확장 함수 내부에선 수시니 객체의 메서드나 프로퍼티를 바로 사용할 수 있지만, 캡슐화를 깨는 것은 아니다.
    - 확장 함수 안에서는 private이나 protected를 사용할 수 없다.
- `수신 객체 타입` : 확장이 정의될 클래스의 타입
- `수신 객체` : 클래스에 속한 인스턴스 객체
  - 확장 함수 본문에서 this를 생략할 수 있다.

<br>

### 📌 확장 함수를 사용하기 위해선 임포트가 필요하다.

```kotlin
import strings.lastChar
// 또는
import strings.*
// 별칭도 추가 가능
import strings.lastChar as last
```

- 항 클래스에 동일 이름의 확장 함수가 둘 이상 있을 때, 임포트를 이용해 이름 충돌을 막을 수 있다.
- 확장 함수는 반드시 짧은 이름을 쓰는 것이 좋다.

<br>

### 📌 자바에서 코틀린의 확장 함수를 호출하기

```java
// StringUtil.kt파일에 정의된 lastChar 확장 함수를 사용하는 예시
char c = StringUtilKt.lastChar("Java")
```

- 내부적으로 확장 함수는 수신 객체를 첫 번째 인자로 받는 정적 메서드이다.
  - 호출할 때 다른 어댑터나 부가적인 비용이 필요하지 않다.
  - 따라서 확장 함수가 들어있는 파일 이름을 이용해 접근할 수 있다.

<br>

### 📌 확장 함수로 유틸리티 함수 정의하기
```kotlin
// Collection<T>에 대한 확장 함수 선언
fun <T> Collection<T>.joinToString(
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
) : String {
    val result = StringBuilder(prefix)
    for ((index, element) in this.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }
    result.append(postfix)
    return result.toString()
}

>> val list = listOf(1, 2, 3)
>> println(list.joinToString(separator = "; ", prefix = "(", postfix = ")"))
>>> (1; 2; 3)
```

- 확장 함수는 정적 메소드 호출에 대한 문법적인 편의(syntaic sugar)이다.
- 클래스가 아닌 더 구체적인 타입을 수신 객체 타입으로 지정할 수도 있다.

```kotlin
// String의 컬렉션에 대해서만 호출할 수 있는 join 함수 정의
fun Collection<String>.join(
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
) = joinToString(separator, prefix, postfix)

>>> println(listOf("one", "two", "three").join(" "))
>>> one two three
}

>> val list = listOf(1, 2, 3)
>> println(list.joinToString(separator = "; ", prefix = "(", postfix = ")"))
>>> (1; 2; 3)
```

<br>

### 📌 확장 함수는 오버라이드 할 수 없다

> - 동적 디스패치 (dynamic dispatch) : 실행 시점에 객체 타입에 따라 동적으로 호출될 대상 메서드 결정 <br>
> - 정적 디스패치 (static dispatch) : 컴파일 시점에 알려진 변수 타입에 따라 정해진 메서드를 호출

- 확장 함수를 클래스의 밖에 선언된다. 즉 클래스의 일부가 아님!
  - 따라서 확장 함수를 호출할 때 수신 객체로 지정한 **변수 정적 타입에 의해 호출될 확장 함수가 결정**된다.
- 어떤 클래스를 확장한 함수와 그 클래스의 멤버 함수의 이름과 시그니처가 동일하다면, **항상 멤버 함수의 우선 순위가 높다**.

<br>

### 📌 확장 프로퍼티

> 프로퍼티라고 불리긴 하지만 상태를 저장할 방법이 없어, 확장 프로퍼티는 아무 상태를 가질 수 없다. <br>
> : 단 코드를 더 짧게 작성하는 데에는 도움이 된다.

```kotlin
val String.lastChar: Char
  get() = get(length - 1)
```

- 수신 객체 클래스를 추가해서 확장 프로퍼티를 만들 수 있다.
- 뒷받침 필드가 없기 때문에 최소한 getter는 꼭 정의해주어야 한다. 동시에 초기화 코드는 쓸 수 없다.

```kotlin
val StringBuilder.lastChar: Char
  // 프로퍼티 게터
  get() = get(length - 1)
  // 프로퍼티 세터
  set(value: Char) {
      this.setCharAt(length - 1, value)
  }
```

- 단, 자바에서 확장 프로퍼티를 사용하고 싶다면 getter나 setter를 명시적으로 호출해야 한다.

<br>

## 3.4 컬렉션 처리 : 가변 길이 인자, 중위 함수 호출, 라이브러리 지원

- varag 키워드를 사용하면 호출 시 인자 개수가 달라질 수 있는 함수를 정의할 수 있다.
- 중위 (infix) 함수 호출 구문을 사용하면 인자가 하나뿐인 메서드를 간편하게 호출할 수 있다.
- 구조 분해 선언 (destructuring declaration)을 이용해 복합적인 값을 분해해해서 여러 변수에 담을 수 있다.

<br>

### 📌 자바 컬렉션 API 확장

> 어떻게 자바 라이브러리 클래스의 인스턴스에 대해 코틀린이 새로운 기능을 추가할 수 있을까?

- 확장 함수를 이용했다.

<br>

### 📌 가변 인자 함수 : 인자의 개수가 달라질 수 있는 함수 정의

> 가변 길이 인자 varag

- 메소드 호출 시 원하는 만큼 값을 인자로 넘길 수 있다
  ```kotlin
  fun listOf<T>(varag values: T): List<T> { ... }
  ```
- `스프레드 연산자 (*)` : 배열에 들은 원소를 넘길 때에 배열의 각 원소가 인자로 전달되게 만든다.
  ```kotlin
  fun main(args: Array<String>) {
    val list = listOf("args : ", *args)
    println(list)
  }
  ```

<br>

### 📌 값의 쌍 다루기 : 중위 호출과 구조 분해 선언

  ```kotlin
  // to 메서드를 일반적인 방식으로 호출한 경우
  1.to("one")
  // to 메서드를 중위 호출 방식으로 호출한 경우
  1 to "one"
  ```
- 중위 호출 (infix call)
  - 수신 객체와 유일한 메서드 인자 사이에 메서드 이름을 넣어 함수를 호출하는 방식
  - 인자가 하나뿐인 일반 메서드, 또는 인자가 하나뿐인 확장 함수에 적용 가능
  - 함수가 중위 호출을 허용하게 만들기 위해 `infix` 변경자를 함수 선언 앞에 추가해야 함

```kotlin
// to 함수의 정의
infix fun Any.to(other: Any) = Pair(this, other)
// 두 원소로 이루어진 순서쌍을 리턴하는 Pair를 이용해 두 변수를 바로 초기화할 수 있다
val (number, name) = 1 to "one"

// 루프 안에서 구조 분해 선언을 활용한 예시
for ((index, element) in collection.withIndex()) {
    println("$index: $element")
}
```

- 구조 분해 선언 (destructuring declaration)
  - 객체가 가진 값을 분해해서 여러 변수를 동시에 초기화하는 방식
  - 루프 안에서 사용하거나, 맵의 원소를 이용하는 등 다양한 방식으로 수행 가능

<br>

## 3.5 문자열과 정규식 다루기

> 자바의 문자열 클래스를 확장 함수를 이용해 더 편하게 활용할 수 있다.


### 📌 문자열 나누기
```kotlin
// to.Regex()를 이용해 정규식 명시 가능
>>> println("12.345-6.A".split("\\.|-".toRegex()))
>>> [12, 345, 6, A]
// 여러개의 구분 문자열을 직접 사용할 수도 있다. 문자열 대신 문자도 사용 가능
>>> println("12.345-6.A".split(".", "-"))
>>> [12, 345, 6, A]
```

- 코틀린은 split 확장 함수를 제공해서 여러 조합의 파라미터를 받을 수 있다.
- 자바의 단 하나의 문자만 받을 수 있는 메서드를 대신한다.

<br>

### 📌 정규식과 3중 따옴표로 묶은 문자열

> 파일 전체 경로명을 디렉터리, 파일 이름, 확장자로 구분해보자.

```kotlin
/* 경로 구분자와 String 확장 함수를 이용해 파싱 */
fun parsePath(path: String) { 
    val directory = path.substringBeforeLast("/")
    val fullName = path.substringAfterLast("/")
    val fileName = fullName.substringBeforeLast(".")
    val extension = fullName.substringAfterLast(".")
    println("Dir: $directory, name: $fileName, ext: $extension")
}

>>> parsePath("/Users/yole/kotlin-book/Chapter.adoc")
>>> Dir: /Users/yole/kotlin-book, name: Chapter, ext: adoc
```
- 코틀린은 문자열에서 구분 문자열이 맨 나중에(또는 처음) 나타난 곳 뒤(또는 앞)의 부분 문자열을 반환할 수 있다.

```kotlin
/* 정규식을 이용해 파싱 */
fun parsePath(path: String) { 
    // 3중 따옴표 문자열에선 어떤 문자도 이스케이프 할 필요가 없다!
    /// """ 디렉터리 (마지막 슬래시) 파일 이름 (마지막 점) 확장자 """
    val regex = """(.+)/(.+)\.().+""".toRegex()
    val matchResult = regex.matchEntire(path)
    if (matchResult != null) { 
        val (directory, filename, extension) = matchResult.destructured
      println("Dir: $directory, name: $fileName, ext: $extension")
    }
}

>>> parsePath("/Users/yole/kotlin-book/Chapter.adoc")
>>> Dir: /Users/yole/kotlin-book, name: Chapter, ext: adoc
```

- `3중 따옴표 문자열` : 이스케이프 문자 생략 가능
  - 마침표를 이스케이프하는 경우
    - 일반 정슈식 : `\\.`
    - 3중 따옴표 문자열 : `\.`

<br>

### 📌 여러 줄 3중 따옴표 문자열

```kotlin
val kotlinLogo = """| //
                    ./ //
                    ./\"""
>>> println(kotlinLogo.trimMargin("."))
| //
| //
|/\
```

- 3중 따옴표 문자열은 문자열 이스케이프를 피하기 위해서도 사용되지만, 줄 바꿈이 들어있는 텍스트를 자를 때도 사용된다.
- 3중 따옴표 문자열 안에서 문자열 템플릿 기능을 이용할 수 있다
  - 단, 3따문 안에선 이스케이프를 쓸 수 없어 '$' 문자를 직접 넣어야 한다
  ```kotlin
  val price = """${'$'}99.9"""
  ```
  
> 라이브러리 알선 (Pimp My Library) 패턴 <br>
> : 기존 라이브러리를 새 언어에서 활용하는 패턴. like 자바와 코틀린

<br>

## 3.6 코드 다듬기 : 로컬 함수와 확장

> DRY 원칙을 지키면서도 문법적인 부가 비용 없이 깔끔하게 코드를 조직하는 방법

- before
```kotlin
/* 코드 중복 예제 */
class User(val id: Int, val name: String, val address: String)
fun saveUser(user: User) {
    // 코드 중복
    if (user.name.isEmpty()) {
        throw IllegalArgumentException("Can't save user ${user.id} : empty Name")
    }
    // 코드 중복
    if (user.address.isEmpty()) {
      throw IllegalArgumentException("Can't save user ${user.id} : empty Address")
    }
}
```

- refactor (1) : 로컬 함수로 코드 중복 줄이기
```kotlin
/* 검증 로직 로컬 함수로 분리하기 */
class User(val id: Int, val name: String, val address: String)
fun saveUser(user: User) {
    fun validate(user: User, value: String, fileldName: String) {
      if (value.isEmpty()) {
        throw IllegalArgumentException("Can't save user ${user.id} : empty $fileldName")
      }
    }
    validate(user, user.name, "Name")
    validate(user, user.address, "Address")
}
```

- refactor (2) : 로컬 함수에서 바깥 함수의 파라미터에 접근하기
```kotlin
/* 로컬 함수는 자신이 속한 바깥 함수의 파라미터와 변수를 그대로 쓸 수 있다는 특성 이용 */
class User(val id: Int, val name: String, val address: String)
fun saveUser(user: User) {
    fun validate(value: String, fileldName: String) {
      if (value.isEmpty()) {
        throw IllegalArgumentException("Can't save user ${user.id} : empty $fileldName")
      }
    }
    validate(user.name, "Name")
    validate(user.address, "Address")
}
```

- refactor (3) : 로컬 함수를 확장 함수로 추출하기
```kotlin
/* 검증 로직을 User 클래스를 확장한 함수로 만들기 */
class User(val id: Int, val name: String, val address: String)
fun User.validateBeforeSave() {
  fun validate(value: String, fileldName: String) {
    if (value.isEmpty()) {
      throw IllegalArgumentException("Can't save user ${user.id} : empty $fileldName")
    }
  }
  validate(name, "Name")
  validate(address, "Address")
}

fun saveUser(user: User) {
    user.validateBeforeSave()
}
```

> 확장 함수를 로컬 함수로 정의할 수도 있지만, 중첩 단계가 깊어지면 가독성이 떨어지기에 패스한다.