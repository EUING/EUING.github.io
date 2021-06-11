---
layout: post
title: Google C++ Style Guide 6-2
subtitle: Other C++ Features 부분 정리.
tags: [C++, guide]
---

-------------

# Other C++ Features
## Type Deduction (including auto)
- C++에서 명시적으로 형식을 작성하는 것이 아닌 컴파일러가 형식을 연역하는 몇 가지 경우가 있음.
    1. Function template argument deduction
	    - 함수 템플릿은 명시적인 인수 없이 컴파일러가 함수 인수 유형을 연역하여 호출 할 수 있음
~~~C
            template <typename T>
            void f(T t);
			
            f(0); // f<int>(0)을 호출
~~~
    2. auto variable declarations
	    - 변수 선언은 auto를 사용하면 함수 템플릿과 같은 규칙으로 형식을 연역함.
        - auto는 const, pointer, 참조 형식이 될 수 있지만 템플릿 인자는 될 수 없음.
~~~C
            auto a = 42; // int
            auto& b = a; // int&
            auto c = b;  // int
            auto d{42}; // std::initializer_list<int>가 아닌 int
~~~
    3. Function return type deduction
	    - auto, decltype(auto)는 함수의 return type으로 사용할 수 있음.
		- 람다 표현식도 같은 방법으로 형식이 연역되지만 보통 명시적으로 auto를 사용하지 않음.
            `auto f() { return 0; } // return type은 int`
    4. Generic lambdas
	    - 람다 표현식은 하나 이상의 매개 변수에 대해 auto를 사용할 수 있으며 함수 템플릿처럼 동작하게 됨.
            `std::sort(vec.begin(), vec.end(), [](auto lhs, auto rhs) { return lhs > rhs; });`
    5. Lambda init captures
	    - 람다 캡처에서 명시적인 지역 변수 초기화가 일어날 때 형식을 연역함.
            `[x = 42, y = "foo"]() { ... } // x는 int, y는 const char*`
    6. Structured bindings
	    - tuple, struct 등 structured binding을 선언할 때 형식을 연역함.
~~~C
            auto [iter, success] = my_map.insert({key, value});
            if(!succcess) {
                iter->second = value;
            }
~~~
    7. Class template argument deduction
	    - 하단 내용 참고
- 장점
    - 네임 스페이스, 템플릿을 포함한 형식은 길고 복잡 할 수 있음.
	- 내용이 적은 코드에서 C++ 형식을 반복적으로 작성하면 가독성이 안 좋을 수 있음.
	- 의도하지 않은 복사 또는 형 변환을 방지하기 위해 형식을 연역하는 것이 더 안전할 수 있음.
- 단점
    - auto의 형식 연역을 숙지해야만 복사가 일어날 지 안 일어날 지 알 수있음.
    - 인터페이스 일부에 auto를 사용하면 의도한 것보다 훨씬 큰 API의 변경이 일어날 수 있음.
    - C++ 코드는 형식을 명시적으로 작성하는 것이 훨씬 명확함.
~~~C
        auto foo = x.add_foo(); 
        auto i = y.Find(key); // y의 형식이 잘 알려져 있지 않은 경우 명확하지 않음.
~~~
- 결론
    - 형식 영역을 사용했을 때 코드가 더 명확하고 안전해지는 경우에만 사용할 것.
	    1. Function template argument deduction
		    - 함수 템플릿 형식 연역은 거의 항상 괜찮음.
		2. Local variable type deduction
		    - 지역 변수의 경우, 불 필요한 형식 정보를 제거하거나 코드를 명확하게 하기 위해 형식 영역을 사용할 수 있음.
			
            ~~~C
            std::unique_ptr<WidgetWithBellsAndWhistles> widget_ptr =
                absl::make_unique<WidgetWithBellsAndWhistles>(arg1, arg2);
            absl::flat_hash_map<std::string,
                std::unique_ptr<WidgetWithBellsAndWhistles>>::const_iterator
            it = my_map_.find(key);
            std::array<int, 6> numbers = {4, 8, 15, 16, 23 ,42};

            auto widget_ptr = absl::make_unique<WidgetWithBellsAndWhistles>(arg1, arg2);
            auto it = my_map_.find(key);
            std::array numbers = {4, 8, 15, 16, 23, 42};
            ~~~

            - 형식 정보가 중요한 경우 auto를 통해 형식을 연역한 후 명시적으로 형식을 지정하여 사용할 것.
            ~~~C
            if (auto it = my_map_.find(key); it != my_map_.end()) {
                WidgetWithBellsAndWhistles& widget = *it->second;
                // widget 참조 변수로 처리
            }
            ~~~

            - 다른 방법으로 비슷한 동작이 되면 decltype(auto)를 사용하지 말 것.
		3. Return type deduction
		    - 함수 몸체에 return이 매우 적고 다른 코드가 거의 없는 경우에 형식 연역을 사용할 것.
			- 헤더 파일의 공용 함수에서 반환 형식 연역을 하지 말 것.
		4. Parameter type deduction
		    - 람다에 대한 형식 영역은 람다를 호출하는 코드에 의해 결정되므로 주의해야 함.
			- 명시적으로 형식을 작성하는 것이 훨씬 명확함.
		5. Lambda init captures
		    - 람다 규칙을 따름
		6. Structured bindings
		    - 다른 형식 연역과 다르게 structured binding 선언은 추가 정보를 제공할 수 있음.
            - 구조체 바인딩의 경우 field name과 달라질 수도 있으므로 주석으로 field name을 나타내는 것이 좋음. 
			    e.g.) `auto [/ * field_name1 = * / bound_name1, / * field_name2 = * / bound_name2] = ...`

## Class Template Argument Deduction
- CTAD를 지원하는 템플릿 클래스에만 사용할 것.
~~~C
    namespace std {
    template <class T, class ...U>
    array (T, U ...) -> std :: array <T, 1 + sizeof ... (U)>;
    }
~~~

## Designated Initializers
- designated Initializers는 C++20 호환 형식으로만 사용할 것.
~~~C
    struct Point {
        float x = 0.0;
        float y = 0.0;
        float z = 0.0;
    }
	
    Point p = {
        .x = 1.0,
        .y = 2.0,
        // z는 0.0으로 초기화
    };
~~~

## Lambda Expressions
- 람다 표현식은 익명 함수 객체를 간결하게 생성할 수 있어 함수 객체를 인자로 전달할 때 유용함.
- 기본 캡처는 암묵적으로 this를 포함하여 모든 변수를 캡처 함.
- init captures을 이용해서 이동 전용 변수를 캡처하는 데 사용할 수 있음.
~~~C
    std::unique_ptr<Foo> foo = ...;
    [foo = std::move(foo)] () {
        ...
    }
~~~
- init captures는 람다 객체의 멤버를 정의하는 방법으로 auto와 같은 규칙으로 형식이 연역 됨.
~~~C
    [foo = std::vector<int>({1, 2, 3})] () {
        ...
    }
~~~
- 장점
    - 람다는 STL 알고리즘에 전달 할 함수 객체를 정의하는 간결하고 가독성을 향상할 수 있는 방법임.
	- 람다, std::function, std::bind는 범용적인 콜백 매커니즘에 함께 사용될 수 있으며 바인딩 함수를 쉽게 작성할 수 있음.
- 단점
    - 람다의 캡처는 댕글링 포인터의 원인이 될 수 있음.
	- 값으로 포인터를 캡처하면 얕은 복사가 일어나므로 수명 문제가 일어날 수 있으며 특히, this를 암시적으로 캡처하기 때문에 인식하기 어려움.
	- init captures는 실제로 새 변수를 선언하지만 형식을 지정하지 않으므로 선언으로 인식하기 어려움.
	- init captures는 auto와 같이 형식을 연역하므로 auto 비슷한 문제를 가지고 있음.
- 결론
    - 람다의 수명이 캡처된 변수보다 확실하게 짧을 경우에만 기본 참조 캡처를 사용할 것.
	- 캡처된 변수 집합이 한 눈에 볼 수 있는 짧은 람다에 대해 몇 개의 변수를 바인딩하는 수단으로만 기본 값 캡처를 사용할 것.
	- 주변 범위에서 변수를 사용할 때만 캡처를 사용할 것.
	- 기존 변수명의 의미를 변경하기 위해 init captures를 사용하지 말 것.
	- 람다가 현재 범위를 벗어날 수 있는 경우 명시적인 캡처를 선호할 것.
	
    ~~~C
    {
    Foo foo;
    ...
    executor-> Schedule ( [&] { Frobnicate(foo); } )
    ...
    }
	// 람다가 foo를 참조하는 것과 this을 통해 Frobnicate를 호출하는 것이 잘 드러나지 않을 수 있음.
	// 함수가 반환된 후 람다를 호출하면 foo가 파괴될 수 있기 때문에 좋지 않음.
	
	{
    Foo foo;
    ...
    executor-> Schedule ( [&foo] { Frobnicate(foo); } )
    ...
    }
	// 만약 Frobnicate가 멤버 함수 인 경우 컴파일 에러가 될 것이고, foo를 참조하는 것이 명시적임.
    ~~~

## Template Metaprogramming
- 장점
    - 템플릿 메타 프로그래밍은 타입 안정성과 고성능을 가지는 유연한 인터페이스를 사용할 수 있음.
- 단점
    - 템플릿 메타 프로그래믕은 전문가가 아닌 이상 잘 알지 못하는 경우가 많고, 디버그 및 유지 관리하기 어려움.
- 결론
    - 복잡한 템플렛 메타 프로그래밍은 자제할 것.
	
## Boost
- Boost 라이브러리 컬렉션에서 승인된 라이브러리만 사용할 것.
    - Call Traits from boost/call_traits.hpp
    - Compressed Pair from boost/compressed_pair.hpp
    - The Boost Graph Library (BGL) from boost/graph, except serialization (adj_list_serialize.hpp) and parallel/distributed algorithms and data structures (boost/graph/parallel/* and boost/graph/distributed/*).
    - Property Map from boost/property_map, except parallel/distributed property maps (boost/property_map/parallel/*).
    - Iterator from boost/iterator
    - The part of Polygon that deals with Voronoi diagram construction and doesn't depend on the rest of Polygon: boost/polygon/voronoi_builder.hpp, boost/polygon/voronoi_diagram.hpp, and boost/polygon/voronoi_geometry_type.hpp
    - Bimap from boost/bimap
    - Statistical Distributions and Functions from boost/math/distributions
    - Special Functions from boost/math/special_functions
    - Root Finding Functions from boost/math/tools
    - Multi-index from boost/multi_index
    - Heap from boost/heap
    - The flat containers from Container: boost/container/flat_map, and boost/container/flat_set
    - Intrusive from boost/intrusive.
    - The boost/sort library.
    - Preprocessor from boost/preprocessor.
	
## std::hash
- std::hash는 특수화하기 어렵고 해시 플러딩 공격으로 인해 보안 취약점이 될 수 있으므로 특수화 하지 말 것.
- 만약 std::hash를 지원하지 않는 key를 사용할 경우 lagacy hash container를 고려 할 것. e.g.) hash_map

## Other C++ Features
- 다음 언급되는 C++ 기능은 사용하지 말 것.
    1. \<ratio\>
	2. \<cfenv\> and \<fenv.h\>
	3. \<filesystem\>
	
## Nonstandard Extensions
- 장점
    - 비표준 확장은 표준 C++에 없는 유용한 기능을 제공할 수 있음.
	- 컴파일러에 대한 중요한 성능 가이드는 비표준 확장을 통해서만 지정할 수 있음.
- 단점
    - 비표준 확장은 일부 컴파일러에서는 작동하지 않으므로 코드의 이식성이 줄어듬.
	- 모든 컴파일러에서 지원된다고 하더라도 컴파일러 간 동작 차이가 있을 수 있음.
- 결론
    - C++에 대한 비표준 확장은 별도로 지정하지 않는 한 사용하지 말 것.
	
## Aliases
- 장점
    - 별칭 선언은 길거나 복잡한 이름을 단순화하여 가독성을 높일 수 있음.
	- 별칭 선언은 API에서 반복적으로 사용되는 형식에 대한 중복을 줄일 수 있으므로 나중에 형식을 쉽게 바꿀 수 있음.
- 단점
    - 클라이언트 코드가 참조 할 수 있는 헤더에 배치하면 헤더의 선언이 많아져서 복잡해짐.
	- 클라이언트 코드가 별칭 선언에 의존하게 되므로 변경하기 어려워짐.
	- 별칭 선언으로 인해 이름 충돌이 생길 수 있음.
	- 별칭 선언이 익숙하지 않은 이름을 부여하면 오히려 가독성이 떨어질 수 있음.
- 결론
    - 고객이 사용하지 않는 한 구현 시 입력을 줄이기 위한 별칭 선언을 공용 API에 넣지 말 것.
	- 공용 API에 네임 스페이스 별칭을 넣지 말 것.
	- 함수 정의, 클래스의 private 영역, 명시적으로 표시된 내부 네임 스페이스 및 .cc file에서는 구현 편의 목적의 별칭 선언을 사용 가능.
	
    ~~~C
    namespace mynamespace {
    using DataPoint = ::foo::Bar*;
	// DataPoint는 추후 Bar*에서 다른 내장 형식으로 변경 될 수 있음.
	// 클라이언트는 opaque pointer인 DataPoint를 사용할 것.
	// Good. 사용자에게 해당 별칭 선언에 대한 설명을 하고 있음.
	
    using TimeSeries = std::unordered_set<DataPoint, std::hash<DataPoint>, DataPointComparator>;
	// 사용자 편의를 위한 별칭 선언
    } // namespace mynamespace
	
    namespace mynamespace {
    using DataPoint = ::foo::Bar*; // Bad. 사용자에게 별칭 선언에 대한 사용법을 알려주지 않음.
    using ::std::unordered_set; // Bad. 단순히 구현 편의 목적임.
    using ::std::hash; // Bad. 단순히 구현 편의 목적임.
    typedef unordered_set<DataPoint, hash<DataPoint>, DataPointComparator> TimeSeries;
    } // namespace mynamespace
    ~~~