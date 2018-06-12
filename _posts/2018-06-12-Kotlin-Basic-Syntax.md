---
title: Kotlin 기본문법
layout: posts
tags: Kotlin, Andorid
categories: Android
---

#Kotlin 기본문법
###패키지 정의
패키지는 소스파일의 상단에 명세한다.
```Kotlin
package my.demo

import java.util.*

//...
```
해당 디렉터리나 패키지가 옳바르게 맞지 않을경우 소스파일에서 임의로 대체한다.
[Packages](https://kotlinlang.org/docs/reference/packages.html)

###함수 정의
아래 함수는 두개의 `Int` 파라메터와 `Int` 리턴타입을 가진다.
```Kotlin
fun sum(a: Int, b: Int): Int{
    ;return a + b
}
