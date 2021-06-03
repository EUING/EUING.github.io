---
layout: post
title: Google C++ Style Guide 3
subtitle: Classes 부분 정리.
tags: [C++, guide]
---

-------------

# Classes
## Doing Work in Constructors
- 생성자에서 가상함수 호출은 피하고 에러 신호를 보낼 수 없는 초기화는 피해야 함.

- 장점
    1. 객체가 초기화 되었는 지 걱정할 필요가 없음.
	2. 생성자에 의해 초기화 된 객체는 const로 선언할 수 있으며 표준 컨테이너 혹은 알고리즘에 쓰이기 쉬움.
- 단점
    1. 생성자의 에러를 처리하기 쉽지 않음. 
	2. IsValid() 함수를 이용해서 초기화 성공 여부를 확인 하는 방법은 잊기 쉬움.
- 결론
    1. 에러 처리를 해야 할 경우 생성자는 private로 숨기고 static factory 함수를 정의 할 것.
	
## Implicit Conversions
- 변환 연산자 및 단일 인자 생성자에 explicit 키워드를 사용할 것.
- 복사 및 이동 연산자는 explicit 키워드를 사용하지 말 것.
- 단일 인자로 생성자를 호출 할 수 없는 경우 explicit 키워드 생략 가능.
- std::initialzer_list로 생성자를 호출 할 경우 explicit 키워드 생략 가능. e.g.) `MyType m = {1, 2}`.

## Copyable and Movable Types
- 클래스가 복사 가능, 이동 전용, 복사 및 이동 가능 인지 명확해야 함.
- 인터페이스는 명시적으로 복사, 이동 연산자를 선언 또는 삭제를 해야 함.
- private 영역이 없는 클래스는 복사, 이동 가능 여부가 public 멤버 변수에 의해 정해 질 수 있고 연산자 선언을 생략 할 수 있음.
- 복사 혹은 이동에 대해 생성자 혹은 연산자를 선언하거나 제거 했으면 나머지 작업도 선언 혹은 제거 해야 함.
- 복사 가능한 클래스에 대한 이동 연산은 복사보다 훨씬 더 효율적이지 않는 한 정의하지 말 것.
- 상속이 예정된 클래스에 대해서 객체 슬라이싱의 위험으로 연산자 및 생성자를 제공하지 않는 것이 좋음.
- 기본 클래스를 복사 할 수 있어야 하는 경우 public 가상 함수를 제공하고 파생 클래스에서 이를 구현하기 위해 protected 복사 생성자를 제공할 것.
~~~C
    class Copyable {
    public:
        Copyable(const Copyable& other) = default;
        Copyable& operator=(const Copyable& other) = default;

        // 이동 연산자는 위에 선언으로 인해 암시적으로 삭제 됨.
        // 복사 연산자보다 효율적이라면 이동 연산자를 선언할 수 있음.
    };

    class MoveOnly {
    public:
        MoveOnly(MoveOnly&& other) = default;
        MoveOnly& operator=(MoveOnly&& other) = default;

        // 복사 연산자는 위에 선언으로 인해 암시적으로 삭제 되었지만 원하면 명시적으로 삭제 해도 됨.
        MoveOnly(const MoveOnly&) = delete;
        MoveOnly& operator=(const MoveOnly&) = delete;
    };

    class NotCopyableOrMovable {
    public:
        // Not copyable or movable
        NotCopyableOrMovable(const NotCopyableOrMovable&) = delete;
        NotCopyableOrMovable& operator=(const NotCopyableOrMovable&) = delete;

        // 이동 연산자는 위에 선언으로 인해 암시적으로 삭제 되었지만 원하면 명시적으로 삭제 해도 됨.
        NotCopyableOrMovable(NotCopyableOrMovable&&) = delete;
        NotCopyableOrMovable& operator=(NotCopyableOrMovable&&) = delete;
    };
~~~

## Structs vs. Classes
- 여러 수동 객체를 전달할 목적일 때에만 구조체를 사용하고 그 외는 클래스를 사용할 것.
- 구조체는 생성자, 소멸자, 멤버 함수를 가질 수 있지만 멤버 변수끼리는 종속성이 없어야 함.

## Structs vs. Pairs and Tuples
- 의미 있는 인자를 사용한다면 구조체를 사용할 것.
- first, second, std::get<X>으로 pair, tuple의 인자를 접근하므로 해당 데이터가 무엇인지 명확하지 않음.

## Inheritance
- 포함 관계가 대체로 상속보다 적절하고 상속을 사용한다면 public으로 선언할 것.
- 구현 상속을 남용하지 말고 상속의 사례를 is a kind of 관계로 제한할 것.
- 하위 클래스에서 접근하는 멤버 함수는 protected로 선언하고 멤버 변수는 private로 선언할 것.
- override, final 지정자를 명시적으로 사용하고 재정의 할 때 virtual 지정자를 붙이지 말 것.
- 다중 상속은 허용하지만, 다중 구현 상속은 권장하지 않음.

## Operator Overloading
- 연산자 오버로딩은 해당 기본 제공 연산자와 동작이 비슷할 경우에만 정의 할 것.
- 사용자 정의 리터럴을 사용하지 말 것.
- 연산자 오버로딩을 템플릿으로 정의하지 말 것.
- 수정하지 않는 이항 연산자는 비 멤버 함수로 선언할 것.
- 연산자를 오버로딩 하지 않고 별도의 함수를 선언하지 말 것.
- std::set와 같이 라이브러리에서 요구하는 연산자를 충족하기 위해 억지로 연산자를 정의하지 말 것.
- &&, \|\|, ,(comma), &, "" 연산자를 오버로딩 하지 말 것.
- 표준 라이브러리를 포함하여 사용자 정의 리터럴을 도입하지 말것.

## Access Control
- 상수를 제외하고 멤버 변수는 private로 선언 할 것.
- 테스트 클래스를 작성할 때는 protected로 선언해도 됨.

## Declaration Order
- 비슷한 종류끼리 그룹화 하고 public, protected, private 순서로 선언 할 것.
- 선언 순서
    1. types(typedef, using, enum, 중첩 구조체 및 클래스)
    2. constants
    3. factory functions
    4. constructors and assignment operators
    5. destructor
    6. all other methods
    7. data members