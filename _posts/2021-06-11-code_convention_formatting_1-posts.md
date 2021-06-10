---
layout: post
title: Google C++ Style Guide 9-1
subtitle: Formatting 부분 정리.
tags: [C++, guide]
---

-------------

# Formatting
## Line Length
- 아래의 상황을 제외하고 한 줄에 80자를 넘기지 말 것.
    1. 테스트 코드
	2. 80자를 초과하는 순수 문자열(이 경우 파일 최상단위 위치해야 함)
	3. 헤더 가드
	4. 별칭 선언
	5. 80자르 자를 경우 가독성이 떨어지는 경우
	
## Non-ASCII Characters
- Non-ASCII 문자는 UTF-8 형식으로 드물게 사용해야 함.
- 일반적으로 unit test 코드에는 Non-ASCII 문자열이 포함될 수 있음.
- u8 접두어를 사용하면 \\uXXX escape sequence를 포함한 문자열이 UTF-8로 인코딩되도록 보장함.
- u8 접두어를 UTF-8로 인코딩 된 Non-ASCII를 포함한 문자열에 사용하지 말 것.
- char16_t, char32_t, wchar_t 자료형은 UTF-8이 아닌 텍스트에 사용되므로 사용하지 말 것.

## Spaces vs. Tabs
- tabs은 사용하지말고 2 space로 들여쓸 것.

## Function Declarations and Definitions
- 반환 형식은 함수 명과 같은 줄에 있어야 하고, 인자는 같은 줄에 맞춰야 함.
- 함수 인자를 한 줄로 쓰지 못할 경우 매개 변수 목록을 묶어야 함.
- 만약 매크로를 속성으로 사용할 경우 함수 선언이나 정의 시 반환 형식 이전에 작성해야 함.
- 함수 선언 시 고려할 사항
    1. 좋은 매개 변수 이름을 사용할 것.
	2. 함수 정의에서 사용되지 않는 매개 변수에 한해서 이름을 생략 가능.
	3. 반환 형식과 함수 이름을 한 줄로 쓰지 못할 경우 둘 사이를 분리해야 함.
	4. 함수 선언 이나 정의 시 반환 형식을 분리할 경우 들여쓰지 말 것.
	5. 매개 변수 여는 괄호는 항상 함수 이름과 같은 줄에 있어야 함.
	6. 함수 이름과 매개 변수 여는 괄호 사이에는 공백이 없어야 함.
	7. 여는 중괄호는 항상 함수 선언의 마지막 줄 끝에 있어야 함.
	8. 닫는 중괄호는 단독으로 마지막 줄에 있거나 여는 중괄호와 같은 줄에 있어야 함.
	9. 매개 변수 닫는 괄호와 여는 중괄호 사이에 공백이 있어야 함.
	10. 가능하면 모든 매개 변수는 정렬해야 함.
	11. 기본 들여쓰기는 2 space
	12. 묶인 매개 변수는 4space로 들여 쓸 것.
	
~~~C
    ReturnType ClassName::FunctionName(Type par_name1, Type par_name2) {
      DoSomething();
      ...
    }
	
    ReturnType ClassName::ReallyLongFunctionName(Type par_name1, Type par_name2,
                                                 Type par_name3) {
      DoSomething();
      ...
    }
	
    ReturnType LongClassName::ReallyReallyReallyLongFunctionName(
        Type par_name1,  // 4 space 들여 쓰기
        Type par_name2,
        Type par_name3) {
      DoSomething();  // 2 space 들여 쓰기
      ...
    }
	
    class Foo {
      public:
        Foo(const Foo&) = delete; // 인자를 명확하게 사용하지 않으면 매개 변수 이름을 생략 가능
        Foo& operator=(const Foo&) = delete;
    };
	
    class Shape {
      public:
        virtual void Rotate(double radians) = 0;
    };

    class Circle : public Shape {
      public:
        void Rotate(double radians) override;
    };

    void Circle::Rotate(double /*radians*/) {} // 인자를 불명확하게 사용하지 않을 경우 주석 처리
~~~

## Lambda Expressions
- 인자와 body는 다른 함수와 같은 규칙을 적용하고 참조 캡처의 경우 &과 변수명에 띄어쓰기가 없어야 함.
- 짧은 람다 표현식은 함수 인자 안에 직접 쓸 수 있음.
~~~C
    int x = 0;
    auto x_plus_n = [&x](int n) -> int { return x + n; }
	
    std::set<int> to_remove = {7, 8, 9};
    std::vector<int> digits = {3, 9, 1, 8, 4, 7, 1};
    digits.erase(std::remove_if(digits.begin(), digits.end(), [&to_remove](int i) {
                  return to_remove.find(i) != to_remove.end();
                }),
                digits.end());
~~~

## Floating-point Literals
- 부동 소수점은 지수 표기법을 사용하더라도 반드시 정수부와 소수부를 가져야 함.
- 정수 표현식을 부동 소수점 변수에 초기화 하는 것은 허용.
- 지수 표기법은 정수 표현이 아님.
~~~C
    float f = 1.f;  // Bad
    long double ld = -.5L;  // Bad
    double d = 1248e6;  // Bad
	
    float f = 1.0f;  // Good
    float f2 = 1;   // Also OK
    long double ld = -0.5L;  // Good
    double d = 1248.0e6;  // Good
~~~

## Function Calls
- 호출 시 인자를 모두 한 줄로 작성하거나 4개 공백으로 들여 쓰거나 첫 번째 인수와 정렬되도록 여러 줄로 나눠야 함.
- 특별히 가독성에 문제가 없으면 함수를 호출 할 때 필요한 줄 수를 줄이려면 한 줄에 여러 인수를 넣을 것.
- 한 줄에 여러 인수를 사용하면 복잡해질 경우 설명이 포함된 이름으로 변수를 할당하거나 주석을 사용할 것.
~~~C
    int my_heuristic = scores[x] * y + bases[x];
    bool result = DoSomething(my_heuristic, x, y, z);
	
    bool result = DoSomething(scores[x] * y + bases[x],  // Score heuristic.
                              x, y, z);

    bool result = DoSomething(argument1, argument2, argument3);
	
    bool result = DoSomething(averyveryveryverylongargument1,
                              argument2, argument3);
							  
    if (...) {
      ...
      ...
      if (...) {
        bool result = DoSomething(
            argument1, argument2,  // 4 space 들여 쓰기
            argument3, argument4);
          ...
      }
    }
~~~

## Conditionals
- else if와 else를 포함한 if문은 if와 여는 괄호를 띄어써야 하며, 닫는 괄호와 여는 중괄호를 띄어 써야함.
- 하지만 여는 괄호와 조건식에는 띄어 쓰기가 없어야 함.
- 조건식 내부에 optional initializer가 존재하면 세미콜론 후 띄어 써야 함.
- else if와 else가 포함된 경우 항상 중괄호를 사용해야 함.
- 만약 if문만 있고 문장이 단순 할 경우 중괄호를 생략해도 됨.
~~~C
    if(condition) {              // Bad - if문 이후 띄어쓰기가 없음
    if ( condition ) {           // Bad - 조건식과 괄호 사이에 띄어쓰기가 있음
    if (condition){              // Bad - 닫는 괄호와 여는 중괄호 사이에 띄어쓰기가 없음
    if(condition){               // Bad

    if (int a = f();a == 10) {   // Bad - 세미콜론 후 띄어쓰기가 없음
	
    if (condition) {                   // OK
      DoOneThing();                    // OK
      DoAnotherThing();
    } else if (int a = f(); a != 3) {  // OK
      DoAThirdThing(a);
    } else {
      DoNothing();
    }
	
    if (x == kFoo) return new Foo();  // OK

    if (x == kBar)
      return new Bar(arg1, arg2, arg3);  // OK

    if (x == kQuz) { return new Quz(1, 2, 3); }  // OK
	
    // Bad - if와 else가 있지만 중괄호가 없음
    if (x) DoThis();
    else DoThat();

    // Bad - if와 else가 있지만 중괄호가 없음
    if (condition)
      foo;
    else {
      bar;
    }

    // Bad - if문만 단독으로 있지만 주석으로 인해 문장이 간단하지 않음.
    if (condition)
      // Comment
      DoSomething();

    // Bad - if문만 단독으로 있지만 조건문이 간단하지 않음.
    if (condition1 &&
        condition2)
      DoSomething();
~~~

## Loops and Switch Statements
- switch문은 블럭에 중괄호를 사용할 수 있으며 의도적으로 break를 넣지 않았을 경우 주석을 추가해야 함.
- 단일 반복문일 경우 중괄호는 선택사항임.
- 빈 루프는 continue나 빈 중괄호를 추가해야 함.
- 열거형을 사용하지 않은 swtich문 인 경우 항상 default 블록을 추가해야 함.
~~~C
    switch (var) {
      case 0: {  // 2 space 들여 쓰기
        ...      // 4 space 들여 쓰기
        break;
      }
      case 1: {
        ...
        break;
      }
      default: {
        assert(false);
      }
    }
~~~

## Pointer and Reference Expressions
- 마침표나 화살표, 포인터, 참조는 띄어쓰기가 없어야 함.
~~~C
    x = *p;
    p = &x;
    x = r.y;
    x = r->y;
~~~
- 포인터, 참조 형식은 띄어쓰기를 포함해야 하며, 앞이나 뒤 둘 중 하나를 선택 해야함.
- 동일한 형식에 대해서는 여러 변수를 선언하는 것은 허용하지만, 포인터 또는 참조 형식이 하나라도 있으면 허용하지 않음.
~~~C
    // 띄어쓰기가 있어서 괜찮음.
    char *c;
    const std::string &str;
    int *GetPointer();
    std::vector<char *>

    // 띄어쓰기가 있어서 괜찮음.
    char* c;
    const std::string& str;
    int* GetPointer();
    std::vector<char*>  // 이 경우 '*' 와 '>' 사이에 띄어쓰기가 없음
	
    int x, *y;  // Bad - & 및 *은 한 줄에 선언하면 안됨
    int* x, *y;  // Bad - & 및 *은 한 줄에 선언하면 안되고 띄어쓰기가 다름
    char * c;  // Bad - 포인터에 띄어쓰기가 앞 뒤로 있음
    const std::string & str;  // Bad - 참조에 띄어쓰기가 앞 뒤로 있음
~~~

## Boolean Expressions
- 만약 조건식를 사용해서 80자를 넘길 경우, 줄을 일관성 있게 분리해야 함.
~~~C
// 이 경우 논리 연산자 &&가 항상 맨 끝에 있음
if (this_one_thing > this_other_thing &&
    a_third_thing == a_fourth_thing &&
    yet_another && last_one) {
  ...
}
~~~