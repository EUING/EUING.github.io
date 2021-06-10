---
layout: post
title: Google C++ Style Guide 8
subtitle: Comments 부분 정리.
tags: [C++, guide]
---

-------------

# Comments
## Comment Style
- //, /\* \*/를 사용하여 주석을 작성할 것. 보통 //를 선호하지만 기존 스타일에 맞춰 일관성을 유지할 것.

## File Comments
- 파일 주석은 파일의 내용을 설명해야 하고 파일의 첫 부분에 저작권을 작성해야 함.
- 작성자가 표기된 파일에 큰 변화를 줄 경우 작성자를 지울 것을 고려해야 함.
- 새 파일에는 보통 저작권 정보나 작성자가 포함되지 않음.
- 헤더 파일에 복합적인 추상화가 있을 경우 파일 주석은 파일의 내용과 추상화가 어떤 관련이 있는 지 설명해야 함.

## Class Comments
- 명확하지 않은 클래스나 구조체 선언에 용도 및 사용 방법을 설명해야 함.
- 여러 스레드에서 객체를 접근할 수 있는 경우 다중 스레드에 대한 규칙을 문서화 할 것.
- 클래스 주석에 간단한 예제 코드를 작성하기 좋음.
- 헤더 파일과 소스 코드파일이 분리되어 있으면 클래스 주석은 인터페이스 정의와 함께 제공되어야 함.

## Function Comments
- 함수 정의에 대한 주석은 함수 동작을 설명하고 함수 선언에 대한 주석은 함수 사용에 대해 설명 함.
- 함수가 단순하고 명백한 경우를 제외하고 함수 선언에는 함수의 기능과 사용 방법을 설명하는 주석이 있어야 함.
- 함수 오버라이딩의 경우 함수의 설명을 반복하기 보다는 오버라이딩의 세부 사항에 대한 주석을 작성할 것.
- 생성자와 소멸자에 대한 설명은 일반적으로 생략하지만 생성자의 동작이 복잡하면 주석을 활용해서 설명할 것.
- 함수 선언에서 주석에 언급할 항목
    1. input과 output에 대한 설명이 필요하고 함수 인자의 경우 \'argument\'.
	2. 객체가 멤버 함수의 종료 이후 참조 인수에 대한 해제 여부.
	3. 함수를 호출한 쪽에서 메모리 해제 여부.
	4. 인자가 nullptr이 될 수 있는 지 여부.
	5. 함수의 사용 방법에 따라 성능의 차이가 있는 경우.
	6. 멀티 스레드에서 동기화 여부.
- e.g.)
~~~C
    // Returns an iterator for this table, positioned at the first entry
    // lexically greater than or equal to `start_word`. If there is no
    // such entry, returns a null pointer. The client must not use the
    // iterator after the underlying GargantuanTable has been destroyed.
    //
    // This method is equivalent to:
    //    std::unique_ptr<Iterator> iter = table->NewIterator();
    //    iter->Seek(start_word);
    //    return iter;
    std::unique_ptr<Iterator> GetIterator(absl::string_view start_word) const;
~~~

## Variable Comments
- 클래스 멤버 변수 중 형식과 변수명이 명확하지 않은 경우 주석을 달아야 하며, 특수한 값에 대해 특정 의미가 있는 경우 주석이 필요함.
~~~C
    private:
    // 테이블의 범위를 검사하는 데 사용되며
    // -1의 의미는 아직 테이블이 비어있다는 뜻
    int num_total_entries_;
~~~
- 모든 전역 변수는 명확하지 않은 경우 전역변수로 선언한 이유와 어디에 사용되는 지 설명해야 함.
~~~C
    // 회귀 테스트에서 실행한 총 테스트 케이스 수를 의미
    const int kNumTestCases = 6;
~~~

## Implementation Comments
- 복잡한 코드 블록 앞에 설명하는 주석이 있어야 함.
~~~C
    // 설명 필요
    for (int i = 0; i < result->size(); ++i) {
        x = (x << 8) + (*result)[i];
        (*result)[i] = x >> 1;
        x &= 1;
    }
~~~

- 명확하지 않은 코드 끝에 주석이 있어야 하며, 공백 2개로 구분해야 함.
~~~C
mmap_budget = max<int64_t>(0, mmap_budget - index_->length());
if (mmap_budget >= data_size_ && !MmapData(mmap_chunk_bytes, mlock))
    return;  // 에러는 이미 로그에 남겼음
~~~

## Function Argument Comments
- 함수의 의미가 명확하지 않은 경우 다음을 고려할 것.
    1. 인자가 리터럴 상수이고 대부분의 함수 호출에서 특정 값을 사용하면 특정 값에 대해 이름을 부여하고 제약 조건을 명시적으로 만들 것.
    2. 인자가 boolean인 경우 enum 인자로 바꿀 경우 인수가 자연스럽게 설명 될 수 있음.
    3. 여러 옵션이 있는 함수의 경우 모든 옵션을 보유한 클래스나 구조체를 정의하고 해당 객체를 전달할 것.
    4. 마지막 수단으로 호출하는 쪽에서 주석으로 인자를 설명할 것.
~~~C
    // What are these arguments?
    const DecimalNumber product = CalculateProduct(values, 7, false, nullptr);
	
    ProductOptions options;
    options.set_precision_decimals(7);
    options.set_use_cache(ProductOptions::kDontUseCache);
    const DecimalNumber product =
        CalculateProduct(values, options, /*completion_callback=*/nullptr);
~~~

## Don'ts
- 코드의 동작이 명확한 경우 코드가 어떤 동작을 하는 지 그대로 설명하지 말 것.
~~~C
    // 벡터에서 element를 찾음.  <-- Bad
    if (std::find(v.begin(), v.end(), element) != v.end()) {
        Process(element);
}

    // 이미 동작하지 않은 element에 대해 동작을 수행. <-- Good
    if (std::find(v.begin(), v.end(), element) != v.end()) {
        Process(element);
}
~~~