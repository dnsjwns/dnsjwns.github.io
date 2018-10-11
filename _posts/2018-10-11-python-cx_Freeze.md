---
title: cx_freeze
layout: posts
tags: python, cx_freeze
categories: python
---



---

Android Studio 같은 툴을 사용하다보면 kotlin의 버전을 업데이트 해버려서 작업중이던 kotlin 프로젝트가 빌드 되지않는 경우가 있다. 그이유는 gradle 빌드 스크립트에서 지정된 kotlin 버전값이 맞지않은 경우가 대부분인데 아래와 같이 해결 할 수 있다.

---

python은 유용한 도구지만 다른이들에게 배포하는 경우에는 적절하지 않다.

일반 윈도우 운영체제에 쓰이는 `exe` 파일로 만들기위해선 `cx_freeze` 같은 툴이 필요하다.

`pip install cx_freeze`

`setup.py`를 작성하여 빌드를 해보자

```python
import sys
from cx_Freeze import setup, Executable

build_exe_options = dict(
    packages = ["os"],
    excludes =["tkinter"],
    include_files = [],
    include_msvcr = []
	)

base = None
if sys.platform == "win32":
    base = "Win32GUI"
    
setup(
	name = "Mytestbuild",
    version = "0.1",
    author = "upda",
    description = "test build",
    options = {"build_exe": build_exe_options},
    executables = [Executable("omg.py", base = base)]
	)
```



``` bash
python setup.py build
```



대부분의 의존성 라이브러리는 `lib`폴더에 포함된다. python36.dll 같은 라이브러리가 빠져 있는 경우가 있는데 이때는 `include_files` 에 포함 시켜주자.



- https://cx-freeze.readthedocs.io/en/latest/distutils.html