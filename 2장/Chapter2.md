# Chapter 2. 코틀린 기초

## 2.1 기본 요소 : 함수와 변수

> 코틀린은 문장을 맺음할 때 세미콜론을 생략할 수 있다!

### 📌 함수 선언 방법

```kotlin
// fun 함수 이름(파라미터 목록): 반환 타입
fun max(a: Int, b: Int): Int {
    // 함수 본문
    return if (a > b) a else b
}
```
- 함수를 선언할 때는 fun 키워드를 사용한다.
- 파라미터의 이름 뒤에 파라미터의 타입을 쓴다.
- 함수는 클래스 없이도 선언될 수 있다.

> 👉 문 (statement) vs 식 (expression) <br>
> : 문 = 자신을 둘러싼 가장 안쪽 블록의 최상위 요소. 아무 값도 만들지 않는다 <br>
> : 식 = 다른 식의 하위 요소로 계산에 참여 가능. 값을 만든다.

<br>

### 📌 함수를 간결하게 표현하는 방법
```kotlin
fun max(a: Int, b: Int) = if (a > b) a else b
```
- 함수 본문이 단일 표현식으로 이루어진 경우, 중괄호와 return, 반환 타입을 생략할 수 있다.

> 👉 반환 타입을 생략할 수 있는 이유 <br>
> : 코틀린은 정적 타입 지정 언어이지만, 식이 본문인 경우 **컴파일러가 자동으로 식 결과 타입을 반환 타입으로 배정**한다.

<br>

### 📌 변수
```kotlin
val question = "삶, 우주, 그리고 모든 것에 대한 궁극적인 질문"
// 타입 명시
val answer: Int = 42
// 타입 표기 생략
val answer = 42
```

- 변수의 타입을 지정하지 않는 경우, 컴파일러가 초기화 식을 분석해 타입을 지정한다.
- 따라서 초기화 없이 변수 선언만 할 경우, 변수 타입의 명시가 필수적이다.

#### ⚡️ 변경 가능한 변수 vs 변경 불가능한 변수

- val (기본적으로 권장되는 키워드)
    - 변경 불가능한 참조를 저장하는 변수 (Immutable)
    - 한 번 초기화하면 이후 재대입이 불가능
    - 자바의 final
- var
    - 변경 가능한 참조 (Mutable)
    - 이전 변수의 값은 바뀔 수 있다
    - 자바의 일반 변수

> 👉 왜 val 키워드를 사용해야 할까? <br>
> : *변경 불가능한 참조*와 *변경 불가능한 객체*를 *부수 효과가 없는 함수*와 조합하면, **함수형 프로그래밍**에 가까워진다.

> 👉 val 변수를 여러 값으로 초기화할 수 있는 경우 <br>
> : 블록이 실행될 때, 단 하나의 초기화 문장만 실행됨을 컴파일러가 확인할 수 있는 경우
> ```kotlin
> val message: String
> if (canPerformOperation()) {
>   message = "Success"
> } else {
>   message = "Failed"
> }
> ```

#### ⚡️ val, var 더 알아보기

- val은 참조 자체가 불변인거지, 참조가 가리키는 객체 내부 값은 불변이 아니다.
  ```kotlin
    val language = arrayListOf("Java") // 불변 참조 선언
    language.add("Kotlin")             // 참조가 가리키는 객체 내부를 변경
  ```
- var 키워드를 쓰면 변수 값은 변경할 수 있지만, 변수 타입은 변경할 수 없다.
  ```kotlin
    var answer = 42
    answer = "no anwwer"  // type mismatch 컴파일 오류 발생
  ```  
    - 만약 특정 타입 변수에 다른 타입의 값을 저장하고 싶다면, 변환 함수 또는 강제 형 변환을 수행해야 한다.

<br>

### 📌 문자열 템플릿

> 선언한 변수를 문자열 리터럴 안에서 사용할 수 있는 기능

```kotlin
fun main(args: Array<String>) {
    val name = if (args.size > 0) {}
    println("Hello, $name!")
}
```
- 문자열 리터럴 내부에서 변수가 들어가야 할 곳에 `$변수명`을 작성하면 된다.
- 만약 $문자를 문자열에 넣고 싶으면 `\$x`와 같이 이스케이프를 이용해야 한다.
- 중괄호를 이용해 복잡한 식도 문자열 템플릿으로 제공할 수 있다.
  ```kotlin
    fun main(args: Array<String>) {
        if (args.size > 0) {
            println("Hello, ${args[0]}!")
        }
    }
  ```

<br>

## 2.2 클래스와 프로퍼티

> 코틀린을 이용해 더 적은 코드로 클래스와 관련 있는 많은 작업들을 수행할 수 있다.

```java
public class Person {
  
  private final String name;
  
  public Person(String name) {
    this.name = name;
  }
  
  public String getName() {
    return name;
  }
  
}
```

- 자바빈 클래스 예시. 필드가 늘어날 수록 생성자 등에서 반복적인 코드 입력 과정이 필요해진다.

```kotlin
class Person (val name: String)
```

- 코틀린으로 동일한 클래스를 작성한 예시. 
- 값 객체 (value object) : 코드 없이 데이터만 저장하는 클래스
- 코틀린 기본 가시성 = public 이므로 가시성 변경자 생략 가능

<br>

### 📌 프로퍼티

> 클래스의 목적 = 데이터 캡슐화 & 관련 코드를 하나의 주체로 묶는 것

- 자바의 클래스는?
  - 멤버 필드의 디폴트 가시성은 private
  - 클래스는 외부 클라이언트가 데이터에 접근할 수 있게 접근자 메서드를 제공
    - 데이터를 읽는 getter와 필드를 변경하는 setter
  - 프로퍼티 = (필드 + 접근자)

```kotlin
class Person (
    // 읽기 전용 프로퍼티 = 비공개 필드와 공개 getter 자동 생성
    val name: String,
    // 쓸 수 있는 프로퍼티 = 비공개 필트, 공개 getter, 공개 setter 자동 생성
    var isMarried: Boolean
)
```

- 코틀린의 클래스는?
  - 프로퍼티를 언어 기본 기능으로 제공, 자바의 필드와 접근자 메소드 완벽하게 대체
  - val 프로퍼티 : 읽기 전용
  - var 프로퍼티 : 변경 가능
  - 프로퍼티를 뒷받침하는 필드 (backing field) : 프로퍼티의 값을 저장하기 위한 필드
    - custom getter를 만들어 프로퍼티 값을 그때그때 갱신할 수도 있다.

<br>

> 👉 프로퍼티 선언 방식만 다를 뿐, 사용하는 방식은 완전히 동일하다! <br>
> : 따라서 자바와 코틀린 클래스를 **완전히 동일한 방식**으로 사용할 수 있다. <br>
> ```kotlin
>   val person = Person("Bob", true)  // Person person = new Person("Bob", true);
>   person.name                       // person.getName()
>   person.isMarried                  // person.isMarried()
>   person.isMarried = false          // person.setIsMarried(false)
> ```

<br>

#### ⚡️ Java에서 선언한 클래스에 대해 Kotlin 문법 사용이 가능하다!

- 자바 클래스의 getter = val 프로퍼티
  - ex. `setName` & `getName` = `name`
- getter & setter 쌍 = var 프로퍼티
  - ex. `isMarried` & `setMarried` = `isMarried`

<br>

### 📌 커스텀 접근자

> 프로퍼티의 접근자를 직접 작성할 수 있다.

```kotlin
class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean
      get() { // 프로퍼티 getter 선언
          return height == width
      }
      // get() = height == width 로도 사용 가능
}
```

<br>

#### ⚡️ Java에서 선언한 클래스에 대해 Kotlin 문법 사용이 가능하다!

- 자바 클래스의 getter = val 프로퍼티
  - ex. `setName` & `getName` = `name`
- getter & setter 쌍 = var 프로퍼티
  - ex. `isMarried` & `setMarried` = `isMarried`

<br>

### 📌 코틀린 소스코드 구조 : 디렉터리와 패키지

> Java의 패키지와 동일. 파일 안에 있는 모든 선언(클래스, 함수, 프로퍼티 등)을 패키지에 포함할 수 있다.

- 코틀린에서는 함수 import와 클래스 import에 차이가 없다.
- 최상위 함수는 이름을 이용해 import 할 수 있다. 
- 스타 임포트 (*)를 이용해 패키지 안의 모든 선언을 임포트할 수 있다.

```kotlin
package geometry.example
import geometry.shapes.createRandomRectangle  // 이름으로 함수 import
```

- Java와는 달리, 코틀린은 여러 클래스를 한 파일에 넣을 수 있고, 파일의 이름도 마음대로 정할 수 있다.
- 동시에 어느 디렉터리에 소스코드를 위치시키든 상관 없다.
- 하지만 Java와의 상호운용성을 고려하여 Java에서 사용하던 패키지 관리 방식을 따르는 것이 좋다.

<br>

## 2.3 선택 표현과 처리 : enum과 when

> Kotlin의 when은 Java의 switch를 보다 강력하게 대치할 수 있는 구성 요소이다.

### 📌 enum 클래스 정의
```kotlin
enum class Color {
    RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
}
```

- 코틀린의 enum은 soft keyword 라고 불린다.
  - enum이 class 앞에 붙으면 특별한 의미를 가지지만, 그렇지 않다면 이름에 사용할 수 있다.

```kotlin
enum class Color(
    val r: Int, val g: Int, val b: Int
) {
  RED(255, 0, 0), ORANGE(255, 165, 0);
  
  fun rgb() = (r * 256 * g) * 256 + b
}

>>> println(Color.BLUE.rgb())
>>> 255
```

- enum 클래스 안에 프로퍼티나 메서드를 정의할 수도 있다.
- enum 상수를 정의할 때는 상수에 해당하는 프로퍼티 값을 지정해야 한다.
  - enum 상수 목록과 메서드 정의 사이에는 세미 콜론이 필수다.

<br>

### 📌 when으로 enum 클래스 다루기

```kotlin
fun getMnemonic(color: Color) = 
    when (color) {
        Color.RED -> "Richard"
        Color.ORANGE -> "Of"
        Color.YELLOW -> "York"
    }

>>> println(getMnemonic(Color.ORANGE))
>>> Of
```

- when의 각 분기 끝에 break를 넣지 않아도 된다.
- 식이 본문인 함수에 when을 사용할 수 있다.

```kotlin
fun getWarmth(color: Color) = 
    when (color) {
        Color.RED, Color,ORANGE, Color,YELLOW -> "warm"
        Color.GREEN -> "neutral"
        Color.BLUE, Color.INDIGO -> "cold"
    }

>>> println(getMnemonic(Color.ORANGE))
>>> warm
```

- 한 분기 안에서 콤마를 이용해 여러 값을 매치 패턴으로 사용할 수도 있다.
- enum 상수 자체를 import해서 코드를 간결하게 만들 수 있다.

<br>

### 📌 when으로 enum 클래스 다루기

> kotlin의 when의 분기 조건은 상수, 숫자 리터럴 외에도 임의의 객체를 활용할 수 있다.

```kotlin
fun mix(c1: Color, c2: Color) = 
    // when 식의 인자로 아무 객체나 사용할 수 있다
    // 호출 시, when은 받은 객체가 각 분기 조건에 있는 객체와 같은지 확인한다
    when (setOf(c1, c2)) {
        setOf(RED, YELLOW) -> ORANGE
        setOf(YELLOW, BLUE) -> GREEN
        // 매치 결과가 없다면 예외를 던진다
        else -> throw Exception("Dirty color")
    }

>>> println(mix(BLUE, ORANGE))
>>> GREEN
```

- `setOf` : 인자로 전달받은 여러 객체를 해당 객체들을 포함하는 Set 객체로 만드는 함수. 
- `집합` : 원소가 모여 있는 컬렉션. 각 원소의 순서는 중요하지 않다.
- when 식은 인자 값과 매치하는 조건 값을 찾을 때까지 동등성(equality)을 이용해 각 분기를 검사한다.

<br>

### 📌 인자 없는 when 사용

> 위의 예제는 매치되는 조건을 찾을 때까지 새로운 Set 인스턴스를 생성하기에 비효율적이다. <br>
> 불필요한 가비지 객체가 늘어나는 것을 방지하기 위해 인자 없는 when 식을 사용할 수 있다.

```kotlin
fun mixOptimized(c1: Color, c2: Color) = 
    when {
      (c1 == RED && c2 == YELLOW) || (c1 == YELLOW && c2 == RED) -> ORANGE
      (c1 == YELLOW && c2 == BLUE) || (c1 == BLUE && c2 == YELLOW) -> GREEN
      else -> throw Exception("Dirty color")
    }

>>> println(mix(BLUE, ORANGE))
>>> GREEN
```

- when에 아무 인자도 없으려면, 각 분기의 조건이 불리언 결과를 계산하는 식이어야 한다.
- 추가 객체를 만들ㄹ지 않아 성능은 좋아지지만, 가독성은 구려진다.

<br>

### 📌 스마트 캐스트 : 타입 검사와 타입 캐스트를 조합

```kotlin
interface Expr
// value라는 프로퍼티만 존재하는 단순한 클래스로, Expr 인터페이스를 구현
class Num(val value: Int) : Expr
// Expr 타입의 객체라면 어떤 것이나 Sum 연산의 인자가 될 수 있다. 따라서 Num 이나 다른 Sum이 인자로 올 수 있다.
class Sum(val left: Expr, val right: Expr) : Expr
```

- 위 예제에서 Expr은 단지 여러 타입의 식 객체를 아우르는 공통 타입 역할만을 수행한다.
- 클래스가 구현하는 인터페이스를 지정하기 위해, 콜론 뒤에 인터페이스 이름을 지정한다.

```kotlin
/* java 스타일의 if 연쇄 */
fun eval(e: Expr): Int {
    if (e is Num) {
        val n = e as Num
        return n.value
    }
    if (e is Sum) {
        // e의 타입이 Sum인지 검사했으므로, 명시적 캐스팅 없이 바로 아래 같이 쓸 수 있다.
        return eval(e.right) + eval(e.left)
    }
    throw IllegalArgumentException("Unknown expression")
}

>>> println(eval(Sum(Sum(Num(1), Num(2)), Num(4))))
>>> 7
```

- Expr 인터페이스에는 두 개의 구현 클래스가 존재하므로, 식의 평가를 위해 두 케이스를 고려해야 한다.
  - 어떤 식이 수라면 그 값을 반환한다
  - 어떤 식이 합계라면 좌항과 우항의 값을 계한한 다음, 그 두 값을 합한 값을 반환한다.
- 코틀린은 `is`를 이용해 변수 타입을 검사한다.
  - 자바의 `instanceOf`와 비슷한 친구. 
  - 단, 자바와 달리 명시적 캐스팅을 해 줄 필요가 없다. 컴파일러가 해줌 = 스마트 캐스팅
- 스마트 캐스팅은 `is 변수에 든 값의 타입이 검사가 완료되었고` + `해당 값이 바뀔 수 없는 경우`에만 작동한다.
  - 따라서 var이거나 커스텀 접근자를 이용한 경우, `as`를 이용해 명시적으로 캐스팅할 수 있다.
  ```kotlin
    val n = e as Num
  ```

<br>

### 📌 리팩토링 : if를 when으로 변경

> if문이 값을 만들기에 3항 연산자가 필요 없는 성질을 이용해 간단하게 수정 가능

```kotlin
/* Kotlin 스타일로 리팩토링 */
fun eval(e: Expr): Int =
    if (e is Num) {
        e.value
    }
    else if (e is Sum) { 
        eval(e.right) + eval(e.left)
    }
    else {
      throw IllegalArgumentException("Unknown expression")
    }

>>> println(eval(Sum(Sum(Num(1), Num(2)), Num(4))))
>>> 7
```

- if 분기에 식이 하나뿐이라면 중괄호를 생략할 수 있다.
- 블록을 사용한다면, 블록의 마지막 식이 분기의 결과 값이다.

```kotlin
/* if 중첩 대신 when 사용하기 */
fun eval(e: Expr): Int =
    when (e) {
        is Num -> e.value
        is Sum -> eval(e.right) + eval(e.left)
        else -> throw IllegalArgumentException("Unknown expression")
    }

>>> println(eval(Sum(Sum(Num(1), Num(2)), Num(4))))
>>> 7
```

- if와 동일하게 값의 타입을 검사한 후 스마트 캐스트가 이뤄진다.

<br>

### 📌 if와 when의 분기에서 블록 사용

> if와 when 모두 분기에 블록을 사용할 경우, 블록의 마지막 문장이 전체의 결과가 된다.

```kotlin
/* 분기에 복잡한 동작이 들어가 있는 when */
fun evalWithLogging(e: Expr): Int =
    when (e) {
        is Num -> {
            println("num: ${e.value}")
            e.value
        }
        is Sum -> {
            val left = evalWithLogging(e.left)
            val right = evalWithLogging(e.right)
            println("sum: $left + $right")
            left + right
        }
        else -> throw IllegalArgumentException("Unknown expression")
    }

>>> println(evalWithLogging()(Sum(Sum(Num(1), Num(2)), Num(4))))
>>> num: 1
>>> num: 2
>>> sum: 1+2
>>> num: 4
>>> sum: 3+$
>>> 7
```

- **블록의 마지막 식이 블록의 결과라는 규칙**은 블록이 값을 만들어야 하는 경우 **항상 유효**하다.
- 식이 본문인 함수는 블록을 분문으로 가질 수 없다.
- 블록이 본문인 함수는 내부에 return 문을 반드시 가져야 한다.

<br>

## 2.4 대상을 이터레이션 : while과 for 루프

> `for <아이템> in <원소들>` 포맷을 취한다

<br>

### 📌 while 루프

```kotlin
/* while loop */
while (조건) {
    /* ... */
}

/* do-while loop */
do {
    /* ... */
} while (조건)
```

<br>

### 📌 수에 대한 이터레이션 : 범위와 수열

- 자바와 같이 변수를 초기화 & 갱신할 필요 없이, 범위(range)를 이용해 반복할 수 있다.
- 범위는 기본적으로 두 값으로 이루어지며, `..` 연산자로 시작과 끝 값을 연결해서 생성한다.
  - 코틀린에서 범위는 폐구간 또는 양끝을 포함하며, 범위에 포한된 모든 값에 대해 반복한다.
  - 수열 : 어떤 범위에 속한 값을 일정 순서로 이터레이션하는 경우

> 피즈버즈 게임 만들기 <br>
> : 순차적으로 수를 세면서 3으로 나눠떨어지면 피즈, 5로 나눠떨어지면 버즈, 3, 5 모두로 나눠떨어지면 피즈버즈

```kotlin
/* when을 이용해 피즈버즈 게임 만들기 */
fun fizzBuzz(i: Int) = when {
    i % 15 == 0 -> "FizzBuzz"
    i % 5 == 0 -> "Buzz"
    i % 3 == 0 -> "Fizz"
    else -> "$i"
}

// 1부터 100까지 순차적으로 실행하는 경우
>>> for (i in 1..100) {
  print(fizzBuzz(i))
}
>>> 1 2 Fizz 4 Buzz Fizz 7 ...

// 100부터 거꾸로 세되, 짝수만으로 진행
>>> for (i in 100 downTo 1 step 2) {
    print(fizzBuzz(i))
}
>>> Buzz 98 Fizz 94 92 FizzBuzz 88 ...
```

- `100 downTo 1` : 100부터 1까지의 역방향 수열 생성
- `step 2` : 증가값의 절대값이 2로 바뀐다.
  - 앞에서 역방향 수열로 만들었으므로, 이 경우에는 -2씩 증가한다.
- `for (x in 0 until size)` : 끝 값을 포함하지 않는 반개구간을 만들고 싶다면 `until` 사용

<br>

### 📌 맵에 대한 이터레이션

```kotlin
val binaryReps = TreeMap<Char, String> ()

// 범위 연산자는 문자에도 사용할 수 있다
for (c in 'A' ... 'F') {
    // 아스키를 2진 표현으로 변환 후 맵에 저장
    val binary = Integer.toBinaryString(c.toInt())
    binaryReps[c] = binary
}

// 맵의 키와 값을 이용해 맵 내용 출력
for ((letter, binary) in binaryReps) {
    println("$letter = $binary")
}
```

- 코틀린의 맵은 키를 사용해 값을 가져오거나, 키에 해당하는 값을 설정하는 기능을 제공한다.
  - `get == map[key]`, `value == map[key] = value`
  - ex. `binaryReps[c] = binary` == `binaryReps.put(c, binary)`

- 맵이 아닌 컬렉션에서도 동일하게 구조 분해 구문을 사용할 수 있다.
  - 구조 분해 구문을 쓰는 이유? : 원소의 현재 인덱스를 유지하면서 컬렉션을 이터레이션할 수 있기 때문.
  ```kotlin
  val list = arrayListOf("10", "11", "12")
  // 인덱스와 함께 컬렉션을 이터레이션하는 예시
  for ((index, element) in list.withIndex()) {
    println("$index: $element")
  }
  >>> 0: 10
  >>> 1: 11
  >>> 2: 12
  ```

<br>

### 📌 in으로 컬렉션이나 범위의 원소 검사

> in으로 값이 범위에 속하는지를, !in으로 값이 범위에 속하지 않는지를 검사한다.

```kotlin
// 실제 범위 클래스 내부에선 'a' .. 'z'는 a' <= c && c <= 'z' 로 변환된다
fun isLetter(c: Char) = c in 'a' .. 'z' || c in 'A' .. 'Z'
fun isNotDigit(c: Char) = c !in '0' .. '9'

>>> println(isLetter('q'))
>>> true
>>> println(isNotDigit('x'))
>>> true
```

- in, !in 연산자는 when 식 안에서도 사용할 수 있다.
- 비교 가능한 클래스(= java.lang.Comparable 인터페이스의 구현체)라면 클래스 인스턴스를 이용해 범위를 만들 수 있다.
  - 이 경우, 범위 내의 모든 객체를 항상 이터레이션할 순 없지만 범위 안에 속하는지는 확인 가능하다.
  ```kotlin
  /* String에 있는 Comparable 구현을 이용해 두 문자열을 알파벳 순서로 비교 가능 */
  // 주어진 값이 특정 범위 안에 속하는지 확인 : Java <= Kotlin <= Scala 사전순 비교
  >>> println("Kotlin" in "Java".."Scala")
  >>> true
  
  // 컬렉션에 in 연산 사용 : 집합의 원소에 "Kotlin"이 들어있는가?
  >>> println("Kotlin" in setOf("Java", "Scala"))
  >>> false
  ```

<br>

## 2.5 코틀린의 예외 처리

```kotlin
// 기본 예외 처리 구문
throw IllegalArgumentException("oh my god")

// Java와 달리 throw가 식이므로 new를 붙이지 않아도 된다.
val percentage = 
    if (number in 0..100)
        number
    else
        throw IllegalArgumentException("oh my god")
```

<br>

### 📌 try, catch, finally

```kotlin
// 함수가 던질 수 있는 예외를 명시할 필요 없음
fun readNumber (reader: BufferedReader): Int? {
    // 정상 케이스
    try {
        val line = reader.readLine()
        return Integer.parseInt(line)
    }
    // 예외 타입을 : 오른쪽에 작성
    // 해당 예외가 발생하면 이거 해라
    catch (e: NumberFormatException) {
        return null
    }
    // 자바와 똑같이 작동
    finally {
        reader.close()
    }
}
```

- Java는 checked exception을 명시적으로 처리해주어야 하므로 throws가 필요하다.
  - 함수에서 발생 가능한 예외나, 함수가 호출한 다른 함수에서 발생 가능한 예외를 모두 catch로 처리한다.
  - 처리하지 못한 그 외의 예외는 throws로 처리해야 한다.
  - 자주 발생하지만 부드럽게 넘겨야 하는 경우, 또는 예외가 발생햏봤자 클라이언트가 취할 수 있는 액션이 없는 경우 비효율적이다.
- 코틀린은 Java와 달리 checked exception와 unchecked exception을 구별하지 않는다.

<br>

### 📌 try를 식으로 사용

```kotlin
fun readNumber (reader: BufferedReader): Int? {
    val number = try {
        // 예외 발생하지 않으면 이 값을 사용
        Integer.parseInt(reader.readLine())
    } catch (e: NumberFormatException) {
        // 예외 발생하면 이 값을 사용
        null
    }
    println(number)
}

>>> val reader = BufferedReader(StringBuffer("not a number"))
>>> readNumber(reader) 
>>> null
```