---
layout: post
title: Google C++ Style Guide 5
subtitle: Google-Specific Magic 부분 정리.
tags: [C++, guide]
---

-------------

# Google-Specific Magic
## Ownership and Smart Pointers
- 동적으로 할당 된 객체에 대해 하나의 소유자가 있는 것과 스마트 포인터를 통해 소유권을 이전하는 것을 선호 함.
- 장점
    - 소유권의 개념 없이 동적 할당된 메모리를 관리 하는 것은 사실상 불가능.
	- 소유권을 이전하는 것이 복사하는 것 보다 가벼울 수 있음.
	- 소유권을 이전하는 것이 객채의 수명 관점에서 참조나 포인터를 사용하는 것보다 간단할 수 있음.
	- 상수 객체에 대해 공유 소유권이 깊은 복사에 대한 간단하고 효율적인 대안이 될 수 있음.
- 단점
    - C++11의 이동 연산의 개념이 들어가서 혼동이 있을 수 있음.
	- 순환 참조가 생기면 객체가 삭제되지 않을 수 있음.
	- 스마트 포인터는 일반 포인터를 완벽하게 대채할 수 없음.
	- 소유권을 이전하는 API는 클라이언트에 스마트 포인터를 강제하게 됨.
	
- 결론
    - 동적 할당이 필요한 경우 할당한 코드로 소유권을 유지하는 것이 좋음.
	- 다른 코드에서 해당 객체에 접근이 필요하면 소유권 이전 없이 복사본을 전달하거나, 포인터 혹은 참조로 전달할 것.
	- 공유를 하는 것으로 성능 이점이 크고 객체가 상수인 경우에 공유 소유권을 사용할 것. e.g.) `std::shared_ptr<const Foo>`
	- std::unique_ptr을 사용해서 소유권 이전을 명시적으로 선언하는 것을 선호.
~~~C
        std::unique_ptr<Foo> FooFactory();
        void FooConsumer(std::unique_ptr<Foo> ptr);
~~~