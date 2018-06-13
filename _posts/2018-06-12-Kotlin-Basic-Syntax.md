---
title: Kotlin 기본문법
layout: posts
tags: Kotlin, Andorid
categories: Android
---



---

### 패키지 정의

패키지는 소스파일의 상단에 명세한다.

```kotlin
package my.demo

import java.util.*

//...
```
해당 디렉터리나 패키지가 옳바르게 맞지 않을경우 소스파일에서 임의로 대체한다.
[Packages](https://kotlinlang.org/docs/reference/packages.html)

---

### 함수 정의

아래 함수는 두개의 `Int` 파라메터와 `Int` 리턴타입을 가진다.

```kotlin
fun sum(a: Int, b: Int): Int{
    ;return a + b
}
```

리턴타입에 정의된 함수.
```kotlin
fun sum(a: Int, b: Int) = a + b
```

의미있는 값을 반환하지않는 함수.
```kotlin
fun printSum(a: Int, b: Int): Unit {
    println("sum of $a and $b is ${a + b}")
}
```

`Unit` 리턴타입을 정의하지 않아도 가능하다.
```
fun printSum(a: Int, b: Int) {
    println("sum of $a and $b is ${a + b}")
}
```
[Functions](https://kotlinlang.org/docs/reference/functions.html)

---

### 변수 정의

Assign-once(읽기 전용) 지역변수

```kotlin
val a; Int = 1 //값과 함께 선언
val b = 2      //Int 타입 정의안함
val c: Int     //값을 초기화 하지않고 타입만 선언
c = 3
```

Mutable 변수
```kotlin
var x = 5 
x += 1
```

Top-level 변수
```kotlin
val PI = 3.14
var x = 0
fun incrementX() {
    x += 1
}
```
[Properties And Fields](https://kotlinlang.org/docs/reference/properties.html)

---

### 주석

코틀린은 자바와 자바스크립트와 같이 end-of-line 과 block 주석을 지원한다.

```kotlin
// This is an end-of-line comment

/* This is a block comment
   on multiple lines. */
```
자바와는 달리 코틀린의 블록 주석은 중첩 될 수 있다.
[Documenting Kotlin Code](https://kotlinlang.org/docs/reference/kotlin-doc.html)

---

### 스트링 템플릿

```kotlin
var a = 1
val s1 = "a is $a"

a = 2
val s2 = "${s1.replace("is", "was")}, but now is $a"
```

[String Templates](https://kotlinlang.org/docs/reference/basic-types.html#string-templates)

---

### 조건부 표현식

```kotlin
fun maxOf(a: Int, b: Int): Int{
    if (a > b){
        return a
    } else {
        return b
    }
}
```

`if` 문을 간단히 쓰면

```kotlin
fun maxOf(a: Int, b: Int) = if (a > b) a else b
```

[if-expressions](https://kotlinlang.org/docs/reference/control-flow.html#if-expression)

---

### `nullable`값과 `null`체크

`null`값이 가능하면 참조는 `nullable`로 명시되어야 한다.

`str`파라메터 가 정수가 아닐때 `null`을 리턴

```kotlin
fun parseInt(str: String): Int?{
    //...
}
```

`nullable` 값을 리턴하는 함수

```kotlin
fun printProduct(arg1: String, arg2: String) {
    val x = parseInt(arg1)
    val y = parseInt(arg2)

    // Using `x * y` yields error because they may hold nulls.
    if (x != null && y != null) {
        // x and y are automatically cast to non-nullable after null check
        println(x * y)
    }
    else {
        println("either '$arg1' or '$arg2' is not a number")
    }    
}
```

[Null-safty](https://kotlinlang.org/docs/reference/null-safety.html)

---

### Type check와 automatic cast

`is`연산자는 `if`구문의 인스턴스 타입을 체크한다. 만약 변하지않는 지역변수나 속성이 특정 타입으로 판별되면 따로 선언할 필요가 없이 자동으로 선언된다.

```kotlin
fun getStringLength(obj: Any): Int? {
    if (obj is String) {
        // `obj` is automatically cast to `String` in this branch
        return obj.length
    }

    // `obj` is still of type `Any` outside of the type-checked branch
    return null
}
```

```kotlin
fun getStringLength(obj: Any): Int? {
    if (obj !is String) return null

    // `obj` is automatically cast to `String` in this branch
    return obj.length
}
```

```kotlin
fun getStringLength(obj: Any): Int? {
    // `obj` is automatically cast to `String` on the right-hand side of `&&`
    if (obj is String && obj.length > 0) {
        return obj.length
    }

    return null
}
```

[Type Checks and Casts](https://kotlinlang.org/docs/reference/typecasts.html)

---

### for 반복문

```kotlin
val items = listOf("apple", "banana", "kiwifruit")
for (item in items) {
    println(item)
}
```

```kotlin
val items = listOf("apple", "banana", "kiwifruit")
for (index in items.indices) {
    println("item at $index is ${items[index]}")
}
```

[for 반복문](https://kotlinlang.org/docs/reference/control-flow.html#for-loops)

---

### while 반복문

```kotlin
val items = listOf("apple", "banana", "kiwifruit")
var index = 0
while (index < items.size) {
    println("item at $index is ${items[index]}")
    index++
}
```

[while 반복문](https://kotlinlang.org/docs/reference/control-flow.html#while-loops)

---

### when 구문

```kotlin
fun describe(obj: Any): String =
when (obj) {
    1          -> "One"
    "Hello"    -> "Greeting"
    is Long    -> "Long"
    !is String -> "Not a string"
    else       -> "Unknown"
}
```

[when 구문](https://kotlinlang.org/docs/reference/control-flow.html#when-expression)

---

### 범위(range)

`if`구문에서 `in`연산자를 이용해  숫자 범위안에 있는지 확인하기

```kotlin
val x = 10
val y = 9
if (x in 1..y+1) {
    println("fits in range")
}
```

`if` 구문에서 범위 밖인지 확인하기

```kotlin
val list = listOf("a", "b", "c")

if (-1 !in 0..list.lastIndex) {
    println("-1 is out of range")
}
if (list.size !in list.indices) {
    println("list size is out of valid list indices range too")
}
```

범위내 반복

```kotlin
for (x in 1..5) {
    print(x)
}
```

```kotlin
for (x in 1..10 step 2) {
    print(x)
}
println()
for (x in 9 downTo 0 step 3) {
    print(x)
}
```

[Ranges](https://kotlinlang.org/docs/reference/ranges.html)

---

### 콜렉션

콜렉션 반복

```kotlin
for (item in items) {
    println(item)
}
```

`in`연산자를 사용해 콜렉션 확인

```kotlin
when {
    "orange" in items -> println("juicy")
    "apple" in items -> println("apple is fine too")
}
```

`lambda`표현식

```kotlin
fruits
.filter { it.startsWith("a") }
.sortedBy { it }
.map { it.toUpperCase() }
.forEach { println(it) }
```

[Higher-order functions and Lambdas](https://kotlinlang.org/docs/reference/lambdas.html)

---

### 기본 클래스 만들기 와 인스턴스

```kotlin
val rectangle = Rectangle(5.0, 2.0) //no 'new' keyword required
val triangle = Triangle(3.0, 4.0, 5.0)
```

[Class](https://kotlinlang.org/docs/reference/classes.html) [Objects and instances](https://kotlinlang.org/docs/reference/object-declarations.html)