---
layout: post
title: Google C++ Style Guide 2
subtitle: Scoping 부분 정리.
tags: [C++, guide]
---

-------------

# Scoping
## Namespaces
- 네임 스페이스는 프로젝트 이름 및 경로에 따라 고유해야하며, 코드를 네임 스페이스에 배치해야 함.

- 네임 스페이스 작성법
    1. std 네임 스페이스에 아무것도 선언하지 말 것.
	2. using 지시문을 사용하지 말 것. e.g.) `using namespace std`.
    3. 주석을 활용해서 명시적으로 네임 스페이스를 종료.
~~~C
        // .h file
        namespace mynamespace {
		
        // 모든 선언은 네임 스페이스 안에 해야 함
        // 들여쓰기를 하지 않음 
        class MyClass{
        public:
            void Foo();
        };
        } // namespace mynamespace
		
        // .cc file
        namespace mynamespace {
        void MyClass::Foo() {
        }
        } // namespace mynamespace
~~~
	4. 함수의 버전 호환을 제외하고는 인라인 네임 스페이스를 사용하지 말 것.
~~~C
        namespace foo {
            namespace v1 {
                void bar() {
                    std::cout << "v1";
                }
            }
		
            inline namespace v2 {
                void bar() {
                    std::cout << "v2";
                }
            }
        }
	
        // foo::bar() 호출 시 v2를 출력
        // foo::v1::bar() 호출 시 v1를 출력
~~~
	
## Internal Linkage
- .cc file의 내용을 외부에서 참조할 필요가 없는 경우 익명 네임 스페이스이나 static을 활용할 것.
- 다른 .cc file에서 같은 이름을 사용하더라도 완전하게 독립적임.
~~~C
    namespace {
    ...
    } // namespace
~~~

## Nonmember, Static Member, and Global Functions
- 전역 함수는 지양하고 네임 스페이스에 배치된 비 멤버 함수를 선호할 것.
- 정적 멤버를 그룹화 하기 위해 클래스(정적 멤버 함수)를 사용하지 말 것.

## Local Variables
- 지역 변수는 가능하면 최대한 늦게 선언하고, 선언과 동시에 초기화 할 것.
- if 및 루프문에서 필요한 변수는 내부에서 선언해야 범위를 제한 할 수 있음.
~~~C
    while(const char* p = strchr(str, '/')) str = p + 1;
~~~

## Static and Global Variables
- trivially destructible
    1. 암시적으로 선언된 destructor 혹은 명시적으로 default가 선언된 destructor.
	2. 비 가상 소멸자.
	3. base class 및 비 정적 멤버도 trivially destructible 해야 함.
- Data segment에 할당되는 변수는 trivially destructible하지 않으면 금지.
- 함수의 static 변수는 동적 초기화를 사용할 수 있지만 정적 클래스 변수 및 네임 스페이스의 변수는 동적 초기화를 권장하지 않음.
- constexpr은 trivially destructible를 보장함.
- 동적 초기화를 사용하거나 trivially destructible하지 않은 변수는 해당 변수가 유효하지 않을 때 접근 할 수 있음.
- 소멸자 결론
    1. trivially destructible 객체.
	2. 기본 자료형, 포인터, 배열, constexpr로 선언된 변수
	3. 참조는 객체가 아니므로 소멸자의 제약이 적용되지 않지만 동적 초기화에 대한 제약은 적용 됨. e.g.) `static T& t = *new T;`.

~~~C
        const int kNum = 10; // allowed.
		
        struct X { int n; };
        const X kX[] = { {1}, {2}, {3} }; // allowed.
		
        void foo() {
            static const char* const kMessages[] = { "hello", "world" } // allowed.
        }
        
        constexpr std::array<int, 3> kArray = { {1, 2, 3} }; // allowed.
~~~

~~~C
        const std::string kFoo = "foo"; // not allowed.
		
        // kBar는 참조이지만 동적으로 초기화 됨
        const std::string& kBar = StrCat("a", "b", "c"); // not allowed.
		
        void bar() {
            // non-trivially destructible
            static std::map<int, int> kData = { {1, 0}, {2, 0}, {3, 0} }; // not allowed.
        }
~~~
- 생성자 결론
    1. 초기화가 constant initialization인 경우 허용
~~~C
        int n = 5; // allowed.
        int m = f(); // not allowed. f()에 따라 다름 
        Foo x; // not allowed. Foo::Foo에 따라 다름 
        Bar y = g(); // not allowed. g() 및 Bar::Bar에 따라 다름 
~~~

~~~C
        struct Foo { constexpr Foo(int){} };
		
        int n = 5; // allowed. 5는 상수 표현식
        Foo x(2); // allowed. 2는 상수 표현식이고 생성자는 constexpr
        Foo a[] = { Foo(1), Foo(2), Foo(3) }; // allowed.
~~~

~~~C
        time_t time(time_t*); // not constexpr.
        int f(); // not constexpr.
        struct Bar { Bar() {} };
		
        time_t m = time(nullptr); // not allowed.
        Foo y(f()); // not allowed.
        Bar b; // not allowed.
~~~
- 공통 패턴
    1. 전역 문자열의 경우 char 배열 혹은 char pointer를 고려.
	2. map, set 등 컨테이너를 사용하고 싶을 때, int 배열 혹은 int and const char* 배열을 고려.
	3. 표준 라이브러리의 컨테이너를 정말로 쓰고 싶으면 함수 내부 정적 변수로 할당할 것.
	4. 스마트 포인터는 사용 금지.
	5. 사용자 정의 정적 변수는 trivially destructible 및 constexpr 생성자를 제공해야 함.
	6. 다른 방법이 없으면 객체를 동적으로 생성하고 함수 정적 포인터 혹은 참조로 받은 후 삭제하지 않을 것. e.g.) `static const auto& impl = *new T(args...);`.
	
## thread_local Variables
- C++11 부터는 threal_local 지정자를 사용하여 변수를 선언할 수 있음. e.g.) `thread_local Foo foo = ...;`.
- thread_local 변수는 Data race에 안전하며 concurrent programming에 효과적임.
- thread_local 변수는 다른 스레드가 접근할 때 실제로 다른 객체에 접근함.
- thread_local 변수는 static 변수와 성질이 유사함.
- static 변수와 비슷하게 함수 내부 변수는 순서와 상관없지만 다른 경우는 초기화 순서 문제가 발생.
- 사실상 전역 변수 이므로 스레드 안전성을 제외하고 전역 변수의 모든 단점을 가지고 있음.