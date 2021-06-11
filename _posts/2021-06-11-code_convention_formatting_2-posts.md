---
layout: post
title: Google C++ Style Guide 9-2
subtitle: Formatting 부분 정리.
tags: [C++, guide]
---

-------------

# Formatting
## Return Values
- 표현식을 반환 할 경우와 표현식을 변수에 넣고 반환 할 경우에만 괄호를 사용할 것.
~~~C
    return result;  // 간단한 경우 괄호가 없어야 함.
    // 복잡한 표현식을 더 읽기 쉽게 만드므로 괄호 허용.
    return (some_long_condition &&
            another_condition);
			
    return (value);  // var = (value)로 쓰지 않음
	return(result);  // return은 함수가 아님
~~~

## Variable and Array Initialization
- 변수 선언 시 =, (), {} 중 고르면 됨.
- (), {}은 동작이 다를 수 있으므로 조심할 것.
~~~C
    // 모두 허용
    int x = 3;
    int x(3);
    int x{3};
    std::string name = "Some Name";
    std::string name("Some Name");
    std::string name{"Some Name"};
~~~

## Preprocessor Directives
- 전처리기 지시문은 들여 쓰기 된 본문에 있는 경우에도 항상 줄의 시작부분에 있어야함.
~~~C
    // Good - directives at beginning of line
      if (lopsided_score) {
    #if DISASTER_PENDING      // 줄의 시작에서 시작함
        DropEverything();
    # if NOTIFY               // # 뒤에 띄어쓰기 허용
        NotifyClient();
    # endif
    #endif
        BackToNormal();
      }
  
    // Bad - indented directives
    if (lopsided_score) {
      #if DISASTER_PENDING  // Bad -- 줄의 시작이 아님
      DropEverything();
      #endif                // Bad -- 들여 쓰기 금지
      BackToNormal();
    }
~~~
## Class Format
- public, protected, private 순서로 작성하고 1 space 들여 쓰기를 해야 함.
- 상위 클래스는 80자 제한에 따라 하위 클래스와 같은 줄에 있어야 함.
- public, protected, private 끼리는 한 줄을 띄어야 함.
- 접근 지정자 뒤에는 띄어쓰지 말 것.
~~~C
    class MyClass : public OtherClass {
     public:      // 1 space 들여 쓰기
      MyClass();  // 2 space 들여 쓰기
      explicit MyClass(int var);
      ~MyClass() {}

      void SomeFunction();
      void SomeFunctionThatDoesNothing() {
      }

      void set_some_var(int var) { some_var_ = var; }
      int some_var() const { return some_var_; }

     private:
      bool SomeInternalFunction();

      int some_var_;
      int some_other_var_;
    };
~~~

## Constructor Initializer Lists
- 생성자 초기화 목록은 모두 한 줄에 있거나 다음 줄에 4 space 들여 쓰기로 작성할 수 있음.
~~~C
    // 생성자 초기화 목록이 한 줄로 구성된 경우
    MyClass::MyClass(int var) : some_var_(var) {
      DoSomething();
    }

    // 생성자 초기화 목록이 한 줄로 구성하기에는 너무 길면,
    // 4칸 들여 쓰고 콜론 이후 목록을 작성해야 함
    MyClass::MyClass(int var)
        : some_var_(var), some_other_var_(var + 1) {
      DoSomething();
    }

    // 생성자 초기화 목록이 여러 줄로 작성할 경우 4칸 들여 쓰기 후 한 줄에 멤버 하나씩 작성 후 열을 맞춰야 함
    MyClass::MyClass(int var)
        : some_var_(var),             // 4 space indent
          some_other_var_(var + 1) {  // lined up
      DoSomething();
    }

    // 생성자의 여는 중괄호와 닫는 중괄호는 적절한 경우에 한 줄에 있을 수 있음
    MyClass::MyClass(int var)
        : some_var_(var) {}
~~~

## Namespace Formatting
- 네임 스페이스의 내용은 들여 쓰지 말 것.

## Horizontal Whitespace
- 줄의 끝에 trailing whitespace를 절대 넣지 말 것.
1. General
~~~C
    void f(bool b) {  // 열린 중괄호는 항상 1 space를 가져야 함
      ...
    int i = 0;  // 세미클론은 보통 space를 가지지 않음
    // 중괄호 초기화에 대한 띄어쓰기는 선택사항으로 만약 넣을 경우 양쪽에 넣어야 함
    int x[] = { 0 };
    int x[] = {0};

    // 상속을 할 때 콜론 양쪽에 띄어쓰기가 있어야 함
    class Foo : public Bar {
     public:
      // 인라인 함수에 대한 정의부는 선언부에서 1 space를 가져야 함
      Foo(int b) : Bar(), baz_(b) {}  // 정의부가 빈 중괄호인 경우 띄어쓰면 안 됨
      void Reset() { baz_ = 0; }  // 인라인 함수에 대한 정의부는 안에 양쪽 space를 넣어야 함
      ...
~~~
2. Loops and Conditionals
~~~C
    if (b) {          // 조건문 이후에 1 space 후 열린 중괄호를 작성해야 함
    } else {          // else 이후 1 space
    }
    while (test) {}   // 보통 소괄호에는 띄어쓰기를 하지 않음
    switch (i) {
    for (int i = 0; i < 5; ++i) {
    // 소괄호에 드물게 띄어쓰기를 가질 수 있으며 기존 코드에 대해 일관성을 유지할 것
    switch ( i ) {
    if ( test ) {
    for ( int i = 0; i < 5; ++i ) {
    // for문은 세미콜론 후 항상 1 space를 가지고 드물게 세미클론 이전에 띄어쓰기를 가질 수 있음
    for ( ; i < 5 ; ++i) {
      ...

    // 범위 기반 for문은 항상 콜론 양쪽에 space를 가져야 함
    for (auto x : counts) {
      ...
    }
    switch (i) {
      case 1:         // switch case문에서 클론 전에 space를 가지지 않음
        ...
      case 2: break;  // 콜론 이후에 코드가 있을 경우 1 space를 가져야 함
~~~
3. Operators
~~~C
    // 대입 연산자는 항상 양쪽으로 space를 가져야 함
    x = 0;

    // 나머지 이항 연산자는 보통 양쪽 space를 가지지만, 없어도 무방하고 소괄호는 띄어쓰지 않음
    v = w * x + y / z;
    v = w*x + y/z;
    v = w * (x + z);

    // 단항 연산자는 인자와 띄어쓰지 않음
    x = -5;
    ++x;
    if (x && !y)
      ...
~~~
4. Templates and Casts
~~~C
    // <> 안에는 띄어쓰지 않음
    std::vector<std::string> x;
    y = static_cast<char*>(x);

    // 포인터를 나타내기 위해 띄어쓰기는 허용하지만 일관성을 유지할 것
    std::vector<char *> x;
~~~

## Vertical Whitespace
- 수직 공백을 최소화 할 것
- 수직 공백에 대한 몇 가지 규칙
    1. 함수의 시작 또는 끝에 있는 수직 공백은 가독성에 도움이 되지 않음.
	2. if-else 블록 내부에 수직 공백이 있으면 가독성에 도움이 될 수 있음.
	3. 주석 앞에 수직 공백이 있으면 보통 가독성이 좋아짐.