---
title: Koans를 해결하며 Kotlin 익히기 1 Introduction (2)
layout: posts
tags: Kotlin, Android
categories: Android
---



---

### Data Classes

코틀린에서는 data저장 목적으로 하는 클래스를 `data`로 표기하여 `data class`를 만들 수 있다.

```kotlin
data class User(val name: String, val age: Int)
```

컴파일러는 기본 생성자에서 선언 된 모든 속성에서 다음 멤버를 자동으로 생성 한다.

- `equals()`/`hashCode()` 
- `"User(name=John, age=42)"`에서의  `toString()` 
- [`componentN()`](https://kotlinlang.org/docs/reference/multi-declarations.html) 선언 순서대로 속성에 해당하는 함수
- `copy()` 함수

생성 된 코드의 일관성과 의미있는 동작을 보장하기 위해 `data class`는 다음과 같은 요구 사항을 충족해야 한다.

- 기본 생성자는 최소 하나 이상의 파라미터가 필요하다.
- 모든 기본 생성자의 파라미터는  `val` 이나 `var`로 표시해야한다.
- `Data class`는 `abstract`, `open`, `sealed`, `inner` 가 될 수없다.

추가로 멤버를 생성할때 아래의 상속에 관한 규칙을 따라야 한다.

- 데이터 클래스 본문 또는 수퍼 클래스의 `final` 구현에서 `equals()`, `hashCode()`, `toString()`을 명시적으로 구현한 경우 이러한 함수가 생성되지 않고 기존에 구현된것이 사용된다.
- 수퍼 타입에 `open`이거나 호환 가능한 타입의 `componetN()` 함수가 있는 경우 해당 함수가 데이터 클래스에 생성 되고 수퍼타입의 함수를 대체한다. 호환되지 않는 서명 또는 최종본으로 인해 수퍼타입의 함수를 대체할  수 없는 경우 오류가 보고된다.
- 일치하는 서명이 있는 `copy(...)`함수가 있는 타입에서 데이터 클래스를 파생시키는것은 금지 된다.
- `componentN()`와 `copy()`함수에 대한 명시적 구현은 허용되지 않는다.

1.1버전부터 데이터 클래스는 다른 클래스를 확장할 수 있다. (예 [Sealed classes](https://kotlinlang.org/docs/reference/sealed-classes.html))

JVM에서 클래스가 파라미터없는 생성자가 필요할 때 모든속성에 대한 기본값을 명시해야한다. ([Constructors](https://kotlinlang.org/docs/reference/classes.html#constructors))

```kotlin
data class User(val name: String = "", val age: Int = 0)
```

#### Properties Declared in the Class Body

컴파일러는 자동 생성 된 함수에 대해 기본 생성자 내부에서 정의 된 속성 만 사용한다.  속성을 자동생성 하지 않으려면 클래스 본문 내에 선언한다.

```kotlin
data class Person(val name: String) {
    var age: Int = 0
}
```

내부함수 `toString()`,`equals()`,`hashCode()`,`copy()`는 `name` 속성만 사용 되며 구성요소 함수는 오직 `component1()`함수 하나만 존재 한다. 두개의 `Person`객체는 다른 `age`값을 가질 수 있지만 동일하게 취급된다.

```kotlin
fun main(args: Array<String>) {
    val person1 = Person("John")
    val person2 = Person("John")

    person1.age = 10
    person2.age = 20


    println("person1 == person2: ${person1 == person2}")
    println("person1 with age ${person1.age}: ${person1}")
    println("person2 with age ${person2.age}: ${person2}")
}
```

#### Copying

대부분의 경우 객체를 복사할때는 어떤 속성을 대체할 필요가 있지만 다른 부분은 바꾸지 않아도 된다. 아래의 `User`클래스에 대해 생성된 함수 `copy()` 를 보자.

```kotlin
fun copy(name: String = this.name, age: Int = this.age) = User(name, age)
```

이렇게 쓰는것이 허용된다.

``` kotlin
val jack = User(name = "Jack", age = 1)
val olderJack = jack.copy(age = 2)
```

#### Data Classes and Destructuring Declarations

데이터 클래스에 생성 된 구성 요소 함수는 선언을 소멸시키는데 사용할 수 있다. ([destructuring declarations](https://kotlinlang.org/docs/reference/multi-declarations.html))

```kotlin
val jane = User("Jane", 35) 
val (name, age) = jane
println("$name, $age years of age") // prints "Jane, 35 years of age"
```

#### Standard Data Classes

표준 라이브러리에서는 `Pair`와 `Triple`을 제공한다. 대부분의 경우 의미있는 이름을 가진 속성으로 명명된 데이터 클래스를 선언하여 사용하는것이 더 나은 선택이다.

---

### Null Safety

#### Nullable types and Non-Null Types

코틀린의 타입 시스템은 [The Billion Dollar Mistake](http://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions)로 알려진 `null`참조 코드의 위험성을 제거하는데 목표를 두고 있다.

자바를 포함해서 많은 프로그램 언어가 빠지는 함정중에 하나는 `null`참조의 멤버에 엑세스하면 `null`참조 예외가 발생한다는 것이다. 자바에서는 이것을 `NullPointerException` 줄여서 `NPE` 라고 한다.

코틀린의 타입 시스템은 코드에서 발생하는  `NullPointerException` 을 제거하는데 목표를 두고 있으며 오직 아래의 경우에만 `NPE`가 발생할 가능성이 있다.

- `throw NullPointerException()`에 대한 명시적 호출
- 아래에 설명된 `!!`연산자에 대한 사용법
- 다음과 같은 경우 초기화와 관련하여 일부 데이터가 일치하지 않을떄:
  - 생성자에서 사용할 수 있는 초기화 되지 않은 `this` 항목이 전달되어 어딘가에서 사용되는경우
  - 수퍼클래스 생성자가 파생 클래스의 구현에서 초기화되지 않은 상태의  [`open`멤버를 호출](https://kotlinlang.org/docs/reference/classes.html#derived-class-initialization-order)하는 경우 
- Java 상호작용 에서
  - [플랫폼 타입](https://kotlinlang.org/docs/reference/java-interop.html#null-safety-and-platform-types)의 `null`참조 멤버에 엑세스 시도 하려는 경우
  - 자바 상호작용에서 사용되는 제네릭타입이 올바르지않은 Null허용을 사용한경우.
  - 다른 외부 자바코드에서 발생한 경우

코틀린에서 타입 시스템은 `null`을 가질 수있는 참조와 불가능한 참조를 구별 한다. 예를들어 `String`유형의 일반 변수는 `null`을 보유 할 수 없다.

```kotlin
var a: String = "abc"
a = null // compilation error
```

`null`을 허용하는 `nullable sstring`은 `String?`으로 명시가능하다.

```kotlin
var b: String? = "abc"
b = null // ok
```

`a`의 속성에 접근하거나 메서드를 호출할때 `NPE`가 발생되지 않음을 보장한다. 하지만 `b`는 안전하지 않기 때문에 컴파일러가 에러를 보고한다.

```kotlin
val l = a.length
val l = b.length // error: variable 'b' can be null
```

#### Checking for `null` in conditions

먼저 `b`가 `null`인지 여부를 명시적으로 확인하고 두 옵션을 각각 처리 할 수 있다.

```kotlin
val l = if (b != null) b.length else -1
```

컴파일러는 `null`여부에 따라 `if`문 내부의 `length`함수를 호출하는것을 허용 한다. 더 복잡한 조건도 지원한다.

```kotlin
if (b != null && b.length > 0) {
    print("String of length ${b.length}")
} else {
    print("Empty string")
}
```

이것은 b가 변하지 않을때 작동한다.(검사와 사용 사이에 수정되지않는 로컬변수 또는  `val` 로 선언된 멤버)

#### Safe Calls

두번째 방법은 안전 호출 연산자인 `?`를 쓰는것이다.

```kotlin
b?.length
```

이것은 `b`가 `null`이아니면 `b.length`를 리턴하고 그 외엔 `null`을 리턴한다. 타입표현은 `Int?` 이다.

안전 호출은 연쇄되어 쓸 때 유용하다. 예를들어 밥이 직원이고 부서에 배치되고(혹은 아니거나) 다른 직원이 부장이고 그 부장의 이름을 호출 하려면 아래와 같이 쓴다.

```kotlin
bob?.department?.head?.name
```

위 호출은 어느 속성이라도 `null`일 경우 `null`을 리턴한다.

`null`이 아닌 값에 대해서만 연산하려면 안전 호출 연산자를  [`let`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/let.html) 과 함꼐 쓴다.

```kotlin
val listWithNulls: List<String?> = listOf("A", null)
for (item in listWithNulls) {
     item?.let { println(it) } // prints A and ignores null
}
```

안전 호출은 할당시 왼쪽에 위치 할 수 있다. 안전 호출 연쇄의 수신자가 `null`을 받을경우 할당은 넘어가고 식의 오른쪽은 무시된다.

```kotlin
// If either `person` or `person.department` is null, the function is not called:
person?.department?.head = managersPool.getManager()
```

#### Elvis Operator

`null` 참조가능한 `r`에 대하여 "`r`이 `null`이 아니면 사용하고 그외엔 `null`이아닌 값 `x`를 쓴다." 라고 할 수 있다.

```kotlin
val l: Int = if (b != null) b.length else -1
```

위와 같은 완전한 `if `표현식을 Elvis 연산자인 `?:`을 사용하여 표현할 수 있다.

```kotlin
val l = b?.length ?: -1
```

`?:`의 왼쪽 부분에는 `null`이 아닐때 리턴 그외에는 오른쪽 부분을 리턴한다. 오른쪽 부분은 오직 왼쪽 부분이 null 일경우에만 유효하다.

코틀린의 `throw`와 `return` 표현식은 elvis 연산자의 오른쪽 부분에서 사용가능하다.

 ```kotlin
fun foo(node: Node): String? {
    val parent = node.getParent() ?: return null
    val name = node.getName() ?: throw IllegalArgumentException("name expected")
    // ...
}
 ```

#### The `!!` Operator

세번째 방법은 널 아님 연산자인 `!!`이다. `!!`은 임의의 값을 `null`이 아닌 타입으로 변환해 값이 `null`인 경우 예외를 `throw` 한다. 예를 들어 `String?`으로 선언된 `b`에 대해서 `b!!`으로 쓸 수 있다.

```kotlin
val l = b!!.length
```

#### Safe Casts

객체가 타겟 타입이 아닐경우 정규 선언은 ClassCastException가 될 가능성이 있다.  또 다른 옵션은 성공적인 호출이 아닐경우 null을 반환하는 안전 호출을 사용하는 것이다.

```kotlin
val aInt: Int? = a as? Int
```

#### Collections of Nullable Type

`nullable` 타입의 요소 컬렉션이 있고 `null`이 아닌 요소를 필터링하려면 `filterNotNull`을 쓰면 된다.

```kotlin
val nullableList: List<Int?> = listOf(1, 2, null, 4)
val intList: List<Int> = nullableList.filterNotNull()
```

---

### Type Checks and Cast:`is` and `as`

#### `is` and `!is` Operators

객체가 런타임에서 주어진 타입이 맞는지 `is`와 그 역인 `!is`연산자를 사용 할 수 있다.

```kotlin
if (obj is String) {
    print(obj.length)
}

if (obj !is String) { // same as !(obj is String)
    print("Not a String")
}
else {
    print(obj.length)
}
```

#### Smart Casts

코틀린에서 대부분의 경우는 명시적 선언을 할 필요가 없다. 컴파일러가 `is`체크를 따라가면서 명시적으로 선언될 불변값에 대하여 필요한경우 자동으로 선언 한다.

```kotlin
fun demo(x: Any) {
    if (x is String) {
        print(x.length) // x is automatically cast to String
    }
}
```

`!is`체크에서도 자동으로 선언한다.

```kotlin
	if (x !is String) return
	print(x.length) // x is automatically cast to String
```

`&&` 이나 `||` 의 오른쪽에서도 자동으로 선언한다.

```kotlin
    // x is automatically cast to string on the right-hand side of `||`
    if (x !is String || x.length == 0) return

    // x is automatically cast to string on the right-hand side of `&&`
    if (x is String && x.length > 0) {
        print(x.length) // x is automatically cast to String
    }
```

[`when`](https://kotlinlang.org/docs/reference/control-flow.html#when-expression)표현식 이나 [`while`](https://kotlinlang.org/docs/reference/control-flow.html#while-loops) 루프 에서도 작동 한다.

```kotlin
when (x) {
    is Int -> print(x + 1)
    is String -> print(x.length + 1)
    is IntArray -> print(x.sum())
}
```

컴파일러가 체크와 사용 사이에 변수를 변경 할 수 없다고 보장 할 수 없는 경우 스마트 캐스트가 작동하지 않는다.

스마트 캐스트는 다음 규칙에 따라 적용 한다.

- `val` 지역 변수 : [위임된 속성](https://kotlinlang.org/docs/reference/delegated-properties.html#local-delegated-properties-since-11)(local delgated properties)을 가진것을 제외한 모든것
- `val` 속성 : 속성이 `private` 이거나 `internal`이거나 같은 모듈내에서 작동하는것으로 명시됨이 확인된경우. 스마트 캐스팅은 속성을 열거나 사용자 정의 게터가 있는 속성에 적용되지 않는다.
- `var` 지역 변수 :  변수가 검사와 사용 사이에 수정되지않는 경우, 변수를 수정하는 람다가 아닌 경우, 위임된 속성이 아닌 경우
- `var` 속성 : 불가능

---

### Objects

#### Object expressions

어떤 타입으로 부터 상속받은 익명의 클래스 객체를 생성하기위해 아래와 같이 쓴다.

```kotlin
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
        // ...
    }

    override fun mouseEntered(e: MouseEvent) {
        // ...
    }
})
```

수퍼타입에 생성자가 있는경우 적절한 생성자 파라미터를 전달해야한다. 여러개의 수퍼타입은 쉼표로 구분될수 있다.

```kotlin
open class A(x: Int) {
    public open val y: Int = x
}

interface B {...}

val ab: A = object : A(1), B {
    override val y = 15
}
```

수퍼타입이 없는 그냥 객체는 간단히 표현 할 수 있다.

```kotlin
fun foo() {
    val adHoc = object {
        var x: Int = 0
        var y: Int = 0
    }
    print(adHoc.x + adHoc.y)
}
```

익명 객체는 `local`이나 `private`선언에서만 타입으로 사용할 수 있다. 익명객체를 `public`함수의 리턴타입 이나 속성으로 사용하는 경우 해당 함수 또는 속성의 실제 타입은 익명 객체의 선언 된 수퍼타입이거나 수퍼타입을 선언 하지않은 경우 `Any`가 되며 익명개체에 추가된 구성원은 접근이 불가하다.

```kotlin
class C {
    // Private function, so the return type is the anonymous object type
    private fun foo() = object {
        val x: String = "x"
    }

    // Public function, so the return type is Any
    fun publicFoo() = object {
        val x: String = "x"
    }

    fun bar() {
        val x1 = foo().x        // Works
        val x2 = publicFoo().x  // ERROR: Unresolved reference 'x'
    }
}
```

Java의 익명 내부 클래스와 마찬가지로 객체 표현식의 코드는 중괄호로 둘러 싸여진 범위 내의 변수에 접근 가능하다. 

```kotlin
fun countClicks(window: JComponent) {
    var clickCount = 0
    var enterCount = 0

    window.addMouseListener(object : MouseAdapter() {
        override fun mouseClicked(e: MouseEvent) {
            clickCount++
        }

        override fun mouseEntered(e: MouseEvent) {
            enterCount++
        }
    })
    // ...
}
```

#### Object declarations

[Singleton](http://en.wikipedia.org/wiki/Singleton_pattern) 은 여러 경우에 유용하게 쓰이며 코틀린에서는 쉽게 선언 가능하다.

```kotlin
object DataProviderManager {
    fun registerDataProvider(provider: DataProvider) {
        // ...
    }

    val allDataProviders: Collection<DataProvider>
        get() = // ...
}
```

이것을 객체 선언 이라 부르며 항상 `object` 키워드 다음에 이름을 가진다. 변수 선언과 같이 객체선언은 표현식이 아니며 대입문의 오른쪽 부분에 사용할 수 없다.

객체선언의 초기화는 스레드로부터 안전하다.

객체 참조를위해 이름을 직접 쓴다.

```kotlin
DataProviderManager.registerDataProvider(...)
```

객체는 수퍼타입을 가질 수 있다.

```kotlin
object DefaultListener : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
        // ...
    }

    override fun mouseEntered(e: MouseEvent) {
        // ...
    }
}
```

주의 : 객체선언은 local 에서 불가하지만 다른 객체 선언 또는 내부클래스가 아닌경우에 중첩 될 수 있다. 

#### Companion Objects

객체는 클래스 내부에 `companion`키워드로 표시 될 수 있다.

```kotlin
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}
```

`companion`객체의 멤버는 한정자 없이 간단히 클래스이름 으로 호출 할 수 있다.

```kotlin
val instance = MyClass.create()
```

`companion`객체의 이름은 생략 될 수 있으며 이때 이름은 `Companion`으로 사용된다.

```kotlin
class MyClass {
    companion object {
    }
}

val x = MyClass.Companion
```

`companion`객체의 멤버가 다른언어의 `static`멤버처럼 보이나 런타임에서 실제 객체의 인스턴스 멤버이다. 예를 들면 인터페이스로 구현할 수 있다.

```kotlin
interface Factory<T> {
    fun create(): T
}


class MyClass {
    companion object : Factory<MyClass> {
        override fun create(): MyClass = MyClass()
    }
}
```

실제 `static`으로 생성 하고자 할 경우 JVM 에서 @JvmStatic 주석을 사용하면 된다. ( [Java interoperability](https://kotlinlang.org/docs/reference/java-to-kotlin-interop.html#static-fields) 참조 )

#### Semantic difference between object expressions and declarations

객체 표현과 객체 선언 사이에는 중요한 차이가 있다.

- 객체 표현은 사용 되는 곳에서 즉시 실행(초기화)된다.
- 객체 선언은  처음 접근할때 느리게 초기화 된다.
- `companion`객체는 대응하는 클래스가 로드 되었을때 초기화되어 Java static initializer의 의미와 일치한다.

---

### Extensions

코틀린은 C#과 Gosu와 유사하게 클래스에서 상속이나 데코레이터와 같은 모든 유형의 디자인 패턴을 사용하지 않고도 새로운 기능으로 클래스를 확장 할 수 있다. 이것은 확장 이라는 특별한 선언을 통해 이루어진다. 코틀린은 확장 기능과 확장 속성을 지원한다.

#### Extension Functions

확장 함수를 선언하기위해 이름 앞에 수신자 타입을 선언한다. 아래의 코드는 `swap`함수에 `MutableList<Int>`로 받는것을 추가한 것이다.

```kotlin
fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' corresponds to the list
    this[index1] = this[index2]
    this[index2] = tmp
}
```

확장 함수 내의`this`키워드는 수신자 객체( . 이전의 전달되는 것)을 지칭한다. 아래와같이 임의의 `MutableList<Int>`에 대하여  함수를 호출 할 수 있다.

```kotlin
val l = mutableListOf(1, 2, 3)
l.swap(0, 2) // 'this' inside 'swap()' will hold the value of 'l'
```

이 함수는 임의의 `MutableList<T>`에 대하여 의미가 있으며 이것을 `generic`으로 만들 수 있다.

```kotlin
fun <T> MutableList<T>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' corresponds to the list
    this[index1] = this[index2]
    this[index2] = tmp
}
```

`generic`타입 파라미터 선언은 함수 이름 이전에 해야한다. ([Generic functions](https://kotlinlang.org/docs/reference/generics.html))

#### Extensions are resolved statically

확장은 실제로 확장한 클래스를 수정하지 않는다. 확장은 클래스에 새 멤버를 삽입하지 않고 이 유형의 변수에 점  표기법으로 호출 할 수있는 새함수를 만든다.

호출되는 확장 함수는 런타임에 해당 표현식을 평가 한 결과의 타입이 아니라 함수가 호출된 표현식의 타입에 따라 결정 된다.

```kotlin
open class C

class D: C()

fun C.foo() = "c"

fun D.foo() = "d"

fun printFoo(c: C) {
    println(c.foo())
}

printFoo(D())
```

위 코드는 확장 함수가 `C`클래스를 지칭하는 `c`파라미터 에만 의존하기 때문에 "c"를 인쇄 할것이다.

클래스에 멤버 함수가 있고 동일한 수신자 타입 동일한 이름 및 주어진 인수에 적용 가능한 확장 함수가 정의된 경우 항상 멤버가 우선된다.

```kotlin
class C {
    fun foo() { println("member") }
}

fun C.foo() { println("extension") }
```

`c.foo()`를 호출하면 "extension"이 아닌 "member"를 인쇄한다.

하지만 확장 함수가 이름은 같지만 서명이 다른 멤버 함수를 오버로드 하는것도 가능하다.

```kotlin
class C {
    fun foo() { println("member") }
}

fun C.foo(i: Int) { println("extension") }
```

`C().foo(1)`을 호출하면 "extension"을 인쇄한다.

#### Nullable Receiver

확장은 `nullable`수신자 타입으로 정의가 가능하다. 확장에서 값이 `null`인 경우에도 호출 가능하며 `this == null`으로 확인 가능하다. 이런 경우 확장 함수 내부에서 검사를 하기 때문에 `toString()`함수를 검사 없이 호출이 가능하다.

```kotlin
fun Any?.toString(): String {
    if (this == null) return "null"
    // after the null check, 'this' is autocast to a non-null type, so the toString() below
    // resolves to the member function of the Any class
    return toString()
}
```

#### Extension Properties

함수와 비슷하게 코틀린은 확장 속성을 지원한다.

```kotlin
val <T> List<T>.lastIndex: Int
    get() = size - 1
```

확장 기능은 실제로 멤버를 클래스에 삽입 하지 않으므로 확장 속성에 대한 [backing field](https://kotlinlang.org/docs/reference/properties.html#backing-fields)가 있는 효율적인 방법은 없다. 이것이 초기화가 확장 속성에 허용되지 않는 이유다. getter와 setter를 명시적으로 정의하는것으로 초기화 할 수 있다.

```kotlin
val Foo.bar = 1 // error: initializers are not allowed for extension properties
```

#### Companion Object Extensions

클래스가 [`companion`객체](https://kotlinlang.org/docs/reference/object-declarations.html#companion-objects)로 정의 되었을때에도 확장 함수와 속성을 정의 가능하다.

```kotlin
class MyClass {
    companion object { }  // will be called "Companion"
}

fun MyClass.Companion.foo() {
    // ...
}
```

`companion`객체의 정규맴버와 같이 클래스 이름으로 한정자 없이 호출 가능하다.

```kotlin
MyClass.foo()
```

#### Scope of Extensions

대부분의 경우 확장은 최상위 레벨에서 선언된다(즉 패키지 바로아래).

```kotlin
package foo.bar
 
fun Baz.goo() { ... } 
```

패키지 밖에서 선언된 확장을 쓰기 위해서는 호출 범위를 `import` 해야 한다.

```kotlin
package com.example.usage

import foo.bar.goo // importing all extensions by name "goo"
                   // or
import foo.bar.*   // importing everything from "foo.bar"

fun usage(baz: Baz) {
    baz.goo()
}
```

#### Declaring Extensions as Members

클래스 안에서 다른 클래스를 확장 선언 할 수 있다. 확장 안에서 다수의 암시적 수신자가 있을 수 있으며 이 수신자는 한정자 없이 접근이 가능하다. 확장이 선언 된 클래스의 인스턴스를 디스패치 수신자 라고 한다. 확장 메소드의 수신자 타입의 인스턴스를 확장 수신자 라고 한다.

```kotlin
class D {
    fun bar() { ... }
}

class C {
    fun baz() { ... }

    fun D.foo() {
        bar()   // calls D.bar
        baz()   // calls C.baz
    }

    fun caller(d: D) {
        d.foo()   // call the extension function
    }
}
```

디스패치 수신자와 확장 수신자간의 이름이 겹칠경우 확장 수신자가 우선된다. 디스패치 수신자에 참조하기 위해서는 [qualified `this`문법](https://kotlinlang.org/docs/reference/this-expressions.html#qualified)을 써야한다.

```kotlin
class C {
    fun D.foo() {
        toString()         // calls D.toString()
        this@C.toString()  // calls C.toString()
    }
```

확장 선언된 멤버는 `open`이나 오버라이딩된 서브클래스로 선언이 가능하다. 이것은 함수를 디스패치하는 것에 대해서 디스패치 수신자 타입과 관련하여 가상의 의미를 가지고 확장 수신자 타입에 관련해서는 정적의 의미를 가진다.

```kotlin
open class D {
}

class D1 : D() {
}

open class C {
    open fun D.foo() {
        println("D.foo in C")
    }

    open fun D1.foo() {
        println("D1.foo in C")
    }

    fun caller(d: D) {
        d.foo()   // call the extension function
    }
}

class C1 : C() {
    override fun D.foo() {
        println("D.foo in C1")
    }

    override fun D1.foo() {
        println("D1.foo in C1")
    }
}

C().caller(D())   // prints "D.foo in C"
C1().caller(D())  // prints "D.foo in C1" - dispatch receiver is resolved virtually
C().caller(D1())  // prints "D.foo in C" - extension receiver is resolved statically
```

