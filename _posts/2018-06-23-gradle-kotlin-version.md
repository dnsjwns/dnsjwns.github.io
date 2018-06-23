---
title: gradle을 통한 빌드시 설치된 kotlin 버전 이 맞지 않는 경우
layout: posts
tags: Kotlin, Android
categories: Android
---



---

Android Studio 같은 툴을 사용하다보면 kotlin의 버전을 업데이트 해버려서 작업중이던 kotlin 프로젝트가 빌드 되지않는 경우가 있다. 그이유는 gradle 빌드 스크립트에서 지정된 kotlin 버전값이 맞지않은 경우가 대부분인데 아래와 같이 해결 할 수 있다.

프로젝트의 `build.gradle`을 찾아 `ext.kotlin_version`의 값을 현재 설치된 kotlin 버전으로 수정해준다.

```gradle
buildscript {
    ext.kotlin_version = '1.2.41' // <- 이부분을 현재 사용하는 버전으로 수정한다.

    repositories {
        mavenCentral()
    }

    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}
```

수정한뒤 싱크후 빌드 해보면 프로젝트가 정상 빌드 됨을 확인 할 수 있다.

- https://kotlinlang.org/docs/reference/using-gradle.html