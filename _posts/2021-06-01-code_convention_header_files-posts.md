---
layout: post
title: Google C++ Style Guide 1
subtitle: Header Files 부분 정리.
tags: [C++]
---

-------------

# Header Files
## Header Files
- *small .cc files, main* 함수를 제외한 모든 .cc 파일 대응하는 헤더 파일이 있어야 함.
- 헤더 파일로 인해 코드의 가독성, 크기 및 성능이 달라질 수 있음.

## Self-contained Headers
- 헤더 가드가 있어야 하며 헤더 파일에서 요구하는 모든 헤더를 include 해야 함.
- 템플릿 및 인라인 함수에 대한 선언과 정의는 동일한 파일에 배치하는 것이 좋음.

## The #define Guard
- 헤더 파일을 중복으로 include하는 것을 방지하기 위한 가드가 있어야 함.
- 심볼 이름은 고유성을 지키기 위해 *PROJECT*\_*PATH*\_*FILE*\_*H*\_의 규칙을 따라야 함.
~~~C
    #ifndef FOO_BAR_BAZ_H_
    #define FOO_BAR_BAZ_H_
    #endif // FOO_BAR_BAZ_H_
~~~
## Forward Declarations
- 장점
    - 컴파일 시간을 줄일 수 있음.
	- 불필요한 재컴파일 시간을 줄일 수 있음.
- 단점
    - 종속성이 가려질 수 있음.
    - 라이브러리의 변경사항에 유연하지 않음.
    - 전방 선언이 필요한 지 #include가 필요한 지 결정하기 어려움.
    - #include를 전방 선언으로 바꾸면 코드의 의미가 바뀔 수 있음.
~~~C
        //b.h:
        struct B {};
        struct D : B {};
		
        //good_user.cc:
        #include "b.h"
        void f(B*);
        void f(void*);
        void test(D* x) { f(x); } // calls f(B*)
		
        // 여기서 #include를 전방 선언으로 바꾸면 test 함수 내부에서 void f(void*) 함수가 호출 됨.
~~~
- 결론 
    - 가능하면 전방 선언을 사용하지 말아야 함.
	
## Inline Functions
- 장점
    - 좀 더 효율적인 목적 코드를 만들 수 있음.
- 단점
    - 인라인을 남용하면 프로그램이 오히려 느려질 수 있음.
	- 작은 함수에 대해서는 코드 크기가 더 줄어들지만 비교적 큰 함수에 대해서는 코드 크기가 늘어남.
	- 최신 프로세서는 명령 캐시를 사용하기 때문에 더 작은 코드를 더 빠르게 동작시킴.
- 결론 
    - 10라인 이상의 함수에 대해서는 인라인을 지양할 것.
    - 반복문 및 switch를 사용하는 함수를 인라인 하는 것은 효율적이지 않음.
	
## Names and Order of Includes
- 헤더 파일를 include 하는 순서:
    1. 관련 헤더
	2. C 시스템 헤더
	3. C++ 표준 라이브러리 헤더
	4. 그 외 라이브러리 헤더
	5. 프로젝트 헤더
- 프로젝트의 모든 헤더 파일은 상대 경로를 사용하지 않고 프로젝트 소스 디렉토리의 하위 항목으로 나열되어야 함.
~~~C
    #include "base / logging.h"  // google-awesome-project/src/base/logging.h
~~~
- 비어 있지 않은 각 그룹은 하나의 빈 줄로 구분.
- 각 그룹은 알파벳 순으로 정렬해야 함.
~~~C
    #include "foo / server / fooserver.h" // 관련 헤더

    #include <sys / types.h> // C 시스템 헤더
    #include <unistd.h> 

    #include <string> // C++ 표준 라이브러리 헤더
    #include <vector> 

    #include "base / basictypes.h"  //  프로젝트 헤더
    #include "base /commandlineflags.h " 
    #include "foo / server / bar.h "
~~~
- 조건부 #include의 경우는 마지막에 넣어도 무방.