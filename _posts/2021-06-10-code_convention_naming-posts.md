---
layout: post
title: Google C++ Style Guide 7
subtitle: Naming 부분 정리.
tags: [C++, guide]
---

-------------

# Naming
## General Naming Rules
- 객체의 목적이나 의도를 설명하는 이름을 사용해야 코드를 즉시 이해할 수 있드록 만들 수 있음.
- 프로젝트를 잘 모르는 사람이 알 수 없는 약어를 최소화 할 것.
- 보편적으로 사용하는 반복문 내 i나 템플릿 인자 T는 괜찮음.
- 템플릿 형식은 형식 이름 규칙, 템플릿 매개 변수는 변수 이름 규칙으로 작성 함.
- 하나의 단어는 띄어쓰기가 없어야 하고 만약 camel case에서 약어를 사용 할 경우 첫 문자만 대문자로 작성 하는 것을 선호.
- 객체의 범위를 고려하여 이름을 정할 것. e.g.) 5줄 함수 내 int n은 괜찮지만 클래스 범위에서는 너무 모호함.
~~~C
    class MyClass { 
    public : 
        int CountFooErrors (const std :: vector <Foo> & foos) { 
            int n = 0; // 좁은 범위에서 간단한 의미
            (const auto & foo : foos) { 
                ... 
                ++ n; 
            } 
            return n; 
        } 
        void DoSomethingImportant () { 
        std :: string fqdn = ...; // 잘 알려진 약어
    } 
    private : 
    const int kMaxAllowedConnections = ...; // 넒은 범위에서 명확한 이름
    };
	
    class MyClass { 
    public : 
        int CountFooErrors (const std :: vector <Foo> & foos) { 
            int total_number_of_foo_errors = 0; // 좁은 범위에서 너무 복자한 이름을 사용
            for (int foo_index = 0; foo_index < foos.size(); ++foo_index) {  // 관용적으로 i를 사용할 것
                ...
                ++total_number_of_foo_errors;
            }
            return total_number_of_foo_errors; 
        } 
        void DoSomethingImportant () { 
        int cstmr_id = ...;  // 내부에서 사용하는 약어
    } 
    private : 
    const int kNum = ...; // 넒은 범위에서 추상적인 이름
    };
~~~

## File Names
- 파일 이름은 소문자로 작성해야 하며 \- 혹은 \_을 사용할 수 있음.
- 만약 기존 프로젝트의 파일 규칙이 없는 경우 \_를 선호.
- 헤더 파일은 .h, 소스 코드는 .cc로 작성해야 함.
- 일반적으로 파일 이름은 구체적으로 지정해야 함.
- 일반적으로 파일이름이 foo_bar.h와 foo_bar.cc로 이루어져있으면 해당 파일은 FooBar class를 정의한 파일.
- 허용하는 파일 이름 예시
    1. my_useful_class.cc
    2. my-useful-class.cc
    3. myusefulclass.cc
    4. myusefulclass_test.cc // \_unittest and \_regtest 사용은 비 권장.
	
## Type Names
- 타입 이름은 \_없이 새로운 문자가 나올 떄마다 대문자로 시작해야 함. e.g.) MyExcitingClass, MyExcitingEnum
- 클래스, struct, 별칭 선언, enum, 템플릿 인자는 모두 같은 규칙이 적용 됨.
~~~C
    // classes and structs
    class UrlTable { ...
    class UrlTableTester { ...
    struct UrlTableProperties { ...

    // typedefs
    typedef hash_map<UrlTableProperties *, std::string> PropertiesMap;

    // using aliases
    using PropertiesMap = hash_map<UrlTableProperties *, std::string>;

    // enums
    enum class UrlTableError { ...
~~~

## Variable Names
- 함수 매개 변수를 포함한 변수와 멤버 변수는 \_로 문자를 구분하여 모두 소문자로 작성해야 함. e.g.) a_local_variable, a_struct_data_member
- struct를 제외한 클래스의 멤버 변수는 끝에 \_를 붙여야 함. e.g.) a_class_data_member_
~~~C
    std::string table_name; // OK
    std::string tableName; // Bad
	
    class TableInfo {
    private:
        std::string table_name_; // OK
        static Pool<TableInfo>* pool_; // OK
    }
	
    struct UrlTableProperties {
        std::string name;
        int num_entries;
        static Pool<UrlTableProperties>* pool;
    }
~~~

## Constant Names
- 프로그램 동작 중 값이 고정된 const, constexpr 변수는 k로 시작하여 camel case를 사용해야 함.
- 대문자로 문자를 구분할 수 없는 드믄 경우에 한해서 \_를 허용 함.
- static, global 변수에 대해서 해당 규칙을 적용하고 지역 변수에 대해서는 선택 사항임.
~~~C
    const int kDaysInAWeek = 7;
    const int kAndroid8_0_0 = 24;  // Android 8.0.0
~~~

## Function Names
- 일반적인 함수는 camel case로 작성해야 하고, get, set 함수는 변수와 같은 규칙으로 작성해도 됨.
- get, set 함수는 실제 멤벼 변수 이름으로 작성해도 되지만 필수 사항은 아님. e.g.) int count(), void set_count(int count).

## Namespace Names
- 네임 스페이스는 \_로 문자를 구분하며 소문자로 작성해야 함.
- 최상단 네임 스페이스는 프로젝트 또는 팀의 이름이어야 함.
- 네임 스페이스의 코드는 일반적으로 네임 스페이스와 일치하는 디렉토리 또는 하위 디렉토리에 있어야 함.
- 네임 스페이스에 대한 축약 선언은 변수 이름 규칙을 따르지만, 네임 스페이스 내부에서는 일반적으로 축약 선안을 사용할 필요가 없음.
- 잘 알려진 최상위 네임 스페이스와 이름이 일치하는 중첩 네임 스페이스를 사용하지 말 것.
- 특히, 중첩된 std 네임 스페이스를 만들지 말 것.
- 깊은 중첩 네임 스페이스과 충돌이 발생하기 쉬운 이름을 사용하지 말 것

## Enumerator Names
- 열거형은 매크로가 아닌 상수와 같은 규칙으로 작성할 것.
~~~C
    enum class UrlTableError {
        kOk = 0,
		kOutOfMemory,
		kMalformedInput,
    };
	
    enum class AlternateUrlTableError {
        OK = 0,
		OUT_OF_MEMORY = 1,
		MALFORMED_INPUT = 2,
    };	
~~~

## Macro Names
- 매크로는 지양해야 하지만 부득이하게 사용할 시 대문자와 \_을 조합해서 사용할 것. e.g.) `#define PI_ROUNDED 3.0`

## Exceptions to Naming Rules
- 기존 C, C++에서 사용하던 유사한 이름을 지정하는 경우 해당 규칙을 적용할 수 있음.