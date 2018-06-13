---
title: Koans를 해결하며 Kotlin 익히기 1 Introduction (1)
layout: posts
tags: Kotlin, Android
categories: Android
---



---

### Koans?

Koans는 Kotlin 문법을 연습할 수 있는 여러 테스트들의 모음집이다. [Github](https://github.com/Kotlin/kotlin-koans)을 통해 배포중이며, [Koans online](http://try.kotlinlang.org/koans) 이나 Android Studio등에서 빌드하여 사용할 수 있다.

1.  GitHub 에서 Koans를 다운받는다.

   ```
    git clone https://github.com/Kotlin/kotlin-koans
   ```

2. IntelliJ IDEA 나 Android Studio등에서 프로젝트를 열고 Kotlin library를 연동한다음 사용한다.

3. 각 test를 실행하고 통과하도록 만든다.

4. 모든 테스트를 반복한다.



Github 저장소의 resoultions 브랜치에서 풀이를 구할 수 있다.

앞으로의 내용들은 Koans를 풀이하는데 필요한 문서를 작성하는 것이다.

---

### Java to Kotlin Converter

IntelliJ IDEA 혹은 Android Studio 의 Kotlin 플러그인은 Java 코드를 Kotlin으로 컨버팅해주는 기능이 기본 탑재 되어있다.

Kotlin 프로젝트에서 Java코드를 .kt 파일로 복사할 경우 해당 코드를 Kotlin으로 컨버팅 할 것인지 다이얼로그를 띄우고 변경하기로한다면 종속성 체크후 변환된다.

---

### Default Arguments

함수 파라미터는 기본 값을 가질수 있으며 해당 인수가 생략 될때 기본 값으로 사용된다. 따라서 다른 언어에 비교해 과부하가 줄어든다.

```kotlin
fun read(b: Array<Byte>, off: Int = 0, len: Int = b.size) {
...
}
```

기본값은 타입 선언후 `=` 뒤에 입력한다. `'이름':  <타입> = <값>`



오버라이딩 메소드는 항상 기본 메소드와 같은 기본 파라미터 값을 사용한다.  기본 파라미터 값을 사용하여 메소드를 오버라이딩 하는 경우 기본 파라미터 값을 생략해야한다.

```kotlin
open class A {
    open fun foo(i: Int = 10) { ... }
}

class B : A() {
    override fun foo(i: Int) { ... }  // no default value allowed
}
```

만약 기본 파라미터가 기본값이없는 파라미터 앞에 오는 경우 이름 인수로 함수를 호출해서 사용해야만 기본값을 사용 할 수 있다.

```kotlin
fun foo(bar: Int = 0, baz: Int) { /* ... */ }

foo(baz = 1) // The default value bar = 0 is used
```

하지만 마지막 인수 lambda가 괄호 밖의 함수 호출에 전달되면 기본 매개 변수에 값을 전달하지 않아도 된다.

```kotlin
fun foo(bar: Int = 0, baz: Int = 1, qux: () -> Unit) { /* ... */ }

foo(1) { println("hello") } // Uses the default value baz = 1 
foo { println("hello") }    // Uses both default values bar = 0 and baz = 1
```

---

###  Named Arguments

함수 파라미터는 함수호출시 명명될 수 있다. 이 기능은 함수에서 많은 수 의 파라미터를 가지고 있을때 유용하다.

아래의 함수를 보자.

```kotlin
fun reformat(str: String,
             normalizeCase: Boolean = true,
             upperCaseFirstLetter: Boolean = true,
             divideByCamelHumps: Boolean = false,
             wordSeparator: Char = ' ') {
...
}
```

위 함수는 기본 인수를 사용하여 함수를 호출 할 수 있다.

```kotlin
reformat(str)
```

하지만 기본 인수가아닌 것을 사용해 호출 할경우 아래와 같이 호출 할 것이다.

```kotlin
reformat(str, true, true, false, '_')
```

이름 인수 를 사용하면 좀더 읽기 편한 코드를 만들 수 있으며 모든 인수를 적지 않아도 된다.

```kotlin
reformat(str,
    normalizeCase = true,
    upperCaseFirstLetter = true,
    divideByCamelHumps = false,
    wordSeparator = '_'
)
```

```kotlin
reformat(str, wordSeparator = '_')
```

함수가 위치 인수와 이름 인수를 모두 사용하여 호출되면, 모든 위치 인수는 첫 번째 이름 인수 앞에 놓여 야한다.

예를들면 `f(1, y = 2)` 는 허용 되지만  `f(x = 1, 2)`는 허용 되지 않는다.



가변 인자 (vararg)는 스프레드 연산자를 사용하여 명명 된 형식으로 전달 될 수 있다.

```kotlin
fun foo(vararg strings: String) { /* ... */ }

foo(strings = *arrayOf("a", "b", "c"))
```

Java 코드는 함수 파라미터의 이름을 보존하지 않기 때문에 Java 함수 호출시 명명된 이름 인수를 사용 할 수 없다.

---

### Lambda Expressions

람다 표현식은 'function literals' 즉 선언되지 않고 즉시 식으로 전달되는 함수이다. 다음예제를 보자.

```kotlin
max(strings, { a, b -> a.length < b.length })
```

함수 `max`는 최상위 함수이며 두 번째 인수로 함수 값을 사용한다. 이 두 번째 인수는 그 자체로 함수, 즉 function literal인 표현식으로 다음 함수와 같다.

```kotlin
fun compare(a: String, b: String): Boolean = a.length < b.length
```



#### Lambda expression syntax

람다 표현식의 전체 구문 형식은 다음과같다.

```kotlin
val sum = { x: Int, y: Int -> x + y }
```

람다 표현식은 항상 중괄호 `{}`로 둘러 싼다. 전체 구문 형식에서 매개 변수 선언은 중괄호 안에  들어가고 선택적 주석은 중괄호 밖에 선언하고 `->` 다음에 본문이 온다.  람다 리턴 타입이 `Unit` 이 아닐경우 본문의 마지막 식이 반환 값으로 처리된다.

모든 선택적 주석을 남겨 두면 다음과 같다.

```kotlin
val sum: (Int, Int) -> Int = { x, y -> x + y }
```



#### Passing a lambda to the last  parameter

코틀린에서 함수의 마지막 파라미터가 함수를 받으면 해당 인수로 전달될 람다 표현식을 괄호 외부에 배치할 수 있다.

```kotlin
val product = items.fold(1) { acc, e -> acc * e }
```

람다 표현식이 그 호출의 유일한 인수라면 괄호를 완전히 생략 할 수 있다.

```kotlin
run { println("...") }
```



#### it: implicit name of a single parameter

람다 표현식에는 하나의 파라미터가 있는게 일반적이다.

컴파일러가 서명 자체를 파악할 수 있는 경우 유일한 파라미터를 선언하지않고 `->`를 생략 할 수 있으며 파라미터는 `it`으로 선언 된다.

```kotlin
ints.filter { it > 0 } // this literal is of type '(it: Int) -> Boolean'
```



#### Returning a value from a lambda expression

정규 표현식을 사용하여 람다 표현식의 값을 명시하여 받을 수 있다. 그렇지 않다면 마지막 식의 값이 반환 된다.

그러므로 아래의 두 코드는 일치 한다.

```kotlin
ints.filter {
    val shouldFilter = it > 0 
    shouldFilter
}

ints.filter {
    val shouldFilter = it > 0 
    return@filter shouldFilter
}
```

이규칙은 괄호 밖에서 람다 표현식을 전달하는것과 함께 [LINQ-style](http://msdn.microsoft.com/en-us/library/bb308959.aspx) 코드를 허용한다.

```kotlin
strings.filter { it.length == 5 }.sortedBy { it }.map { it.toUpperCase() }
```



#### Underscore for unused variables (since 1.1)

만약 람다 파라미터를 쓰지 않을 경우 이름대신 밑줄 `_`으로 대체 가능하다.

```kotlin
map.forEach { _, value -> println("$value!") }
```



#### Destructuring in lambdas (since 1.1)

[Destructuring declarations](https://kotlinlang.org/docs/reference/multi-declarations.html#destructuring-in-lambdas-since-11). 

---

### String Templates

문자열에는 템플릿 표현식, 즉  evaluate 되고 결과가 문자열로 연결되는 코드가 포함 될 수 있다.

템플릿 표현식은 `$`를 붙여 간단하게 표현하거나

```kotlin
val i = 10
println("i = $i") // prints "i = 10"
```

중괄호 안에 임의로 표현한다.

```kotlin
val s = "abc"
println("$s.length is ${s.length}") // prints "abc.length is 3"
```

템플릿은 raw 문자열이나 escape 문자열 모두를 지원한다. 문자 `$` 를 raw 문자열로 표현하려면 다음 구문을 사용하면 된다.( `\`는 지원하지 않는다.)

```kotlin
val price = """
${'$'}9.99
"""
```



