---
title: Kotlin 기본문법
layout: posts
tags: Kotlin, Andorid
categories: Android
---

#Kotlin 기본문법
###패키지 정의
패키지는 소스파일의 상단에 명세한다.
```kotlin
package my.demo

import java.util.*

//...
```
해당 디렉터리나 패키지가 옳바르게 맞지 않을경우 소스파일에서 임의로 대체한다.
[Packages](https://kotlinlang.org/docs/reference/packages.html)

###함수 정의
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

###변수 정의
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

###주석
코틀린은 자바와 자바스크립트와 같이 end-of-line 과 block 주석을 지원한다.
```kotlin
// This is an end-of-line comment

/* This is a block comment
   on multiple lines. */
```
자바와는 달리 코틀린의 블록 주석은 중첩 될 수 있다.
[Documenting Kotlin Code](https://kotlinlang.org/docs/reference/kotlin-doc.html)
