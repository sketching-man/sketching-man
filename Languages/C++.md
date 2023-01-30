# 스마트 포인터란?

객체를 heap 영역에 생성할 때 new 키워드를 쓰는데, 이걸 명시적으로 지워주지 않으면 프로세스가 동작하는 동안 heap에 계속 남아있음.  
스마트 포인터는 포인터처럼 동작하는 클래스 템플릿으로, 사용이 끝난 메모리를 자동으로 해제함

# 스마트 포인터 종류?

1. unique_ptr + make_unique
2. shared_ptr + make_shared
3. weak_ptr

# unique_ptr에 대해

하나의 스마트 포인터만이 특정 객체를 소유할 수 있도록, 객체에 소유권 개념을 도입한 스마트 포인터  
unique_ptr 인스턴스는 move() 멤버 함수를 통해 소유권을 이전할 수는 있지만, 복사는 불가  
```
	unique_ptr<int> ptr01(new int(5)); // int형 unique_ptr인 ptr01을 선언하고 초기화함.
	auto ptr02 = move(ptr01);          // ptr01에서 ptr02로 소유권을 이전함.
	// unique_ptr<int> ptr03 = ptr01;  // 대입 연산자를 이용한 복사는 오류를 발생시킴. 
	ptr02.reset();                     // ptr02가 가리키고 있는 메모리 영역을 삭제함.
	ptr01.reset();                     // ptr01가 가리키고 있는 메모리 영역을 삭제함.
```

# shared_ptr에 대해

하나의 특정 객체를 참조하는 스마트 포인터가 총 몇 개인지를 참조하는 스마트 포인터  
참조하고 있는 스마트 포인터의 개수를 참조 횟수(reference count)라 칭함, `(instance).use_count()`로 횟수 열람 가능  
참조 횟수가 0이 되면 delete 키워드를 사용하여 메모리를 자동으로 해제  
```
	shared_ptr<int> ptr01(new int(5)); // int형 shared_ptr인 ptr01을 선언하고 초기화함.
	cout << ptr01.use_count() << endl; // 1
	auto ptr02(ptr01);                 // 복사 생성자를 이용한 초기화
	cout << ptr01.use_count() << endl; // 2
	auto ptr03 = ptr01;                // 대입을 통한 초기화
	cout << ptr01.use_count() << endl; // 3  
```

# weak_ptr에 대해

하나 이상의 shared_ptr 인스턴스가 소유하는 객체에 대한 접근을 제공하지만, 소유자의 수에는 포함되지 않는 스마트 포인터  
대놓고 shared_ptr를 이니셜 인자로 받아도 ref count가 올라가지 않음.  
때문에 weak_ptr가 참조하고 있어도 ref count가 0이 되면 짤없이 해제됨.  
주로 weak_ptr은 shared_ptr 인스턴스 사이의 순환 참조를 제거하기 위해서 사용됨.  

# 왜 make_ 함수를 쓰는가? (참조)

1. make 함수가 new를 사용하지 않는다는 점입니다.  
make 함수를 사용하지 않을 때  Widget이라는 type이 2번 등장하고 있습니다.  
이는 중복되는 코드를 피하자는 소프트웨어 엔지니어링의 중요 견해와 충돌하게 되는 지점입니다.  
중복 코드가 나타났을 때 생기는 컴파일 타임의 증가, 코드량의 증가, 일치하지 않는 코드가 나타나 생길 수 있는 버그등의 문제 때문에 make 함수는 선호 됩니다. 

2. make 함수가 exception safety 하게 작업이 이루어진다는 것입니다.  
make 함수를 상용하지 않고 processWidget 함수를 실행 하겠습니다.  
`processWidget(std::shared_ptr<Widget>(new Widget), computePriority());`  
이 함수를 실행하기 위해서는 다음의 세 가지가 먼저 실행되야 합니다. 

	a. 새로운 Widget 객체 생성 (new를 통해서)  
	b. Widget 객체를 인자로 전달해서 std::shared_ptr 생성  
	c. computePriority 함수 실행  

이 세 가지의 실행이 컴파일러마다 다른 순서로 일어나 문제가 발생할 수 있습니다.  
만약 1-3-2 순서로 실행이 이루어지는 경우 1번에서 Widget 객체가 생성되고 3번에서 computePriority 함수에서 예외가 발생할 수 있습니다.  
이렇게 되면 Widget 객체는 만들어진 후 std::shared_ptr에 의해서 관리될수 없으므로 자원 누출이 일어납니다. 

# Casting (업 캐스팅 / 다운 캐스팅)

부모 클래스와 자식 클래스가 있다고 생각해보자.  
Parent(부모) -상속-> Child(자식)이라 할 때,

```
Child* object = new Child();
Parent* base = object; // 업 캐스팅: 나를 가리키는 포인터의 상속 위계를 올림. 캐스트 명시가 필요없음
object = (Child*)base; //다운 캐스팅: 나를 가리키는 포인터의 상속 위계를 내림. 캐스트 타입 명시 필요
```

# c++ cast operator 종류

* static_cast: 정수형/실수형/열거형/클래스 등 논리적으로 변경이 가능한 경우에 변경을 허용함. const 붙이기도 가능.  
c-style 캐스팅보다 안전함.  
업캐스팅/다운캐스팅이 가능하지만 다운캐스팅 시 발생할 수 있는 위험에 대한 책임은 프로그래머가 져야 함  
* dynamic_cast: 상속 관계에 있는 클래스 간 형변환을 위해 사용 됨.  
업캐스팅은 언제나 가능하지만, 다운캐스팅은 해당 클래스가 가상함수가 하나라도 있어야 함(= 다형성을 띄어야 함)
* const_cast: 데이터의 상수 속성 혹은 volitile을 제거할 수 있음
* reinterpret_cast: 데이터의 강제 변환. 메모리를 재해석한다고 이해해도 된다.

# STL

## 컨테이너(container)

STL에서 컨테이너(container)는 같은 타입의 여러 객체를 저장하는 일종의 집합  
컨테이너는 클래스 템플릿으로, 컨테이너 변수를 선언할 때 컨테이너에 포함할 요소의 타입을 명시  
컨테이너에는 복사 생성과 대입을 할 수 있는 타입의 객체만을 저장  

* Sequence containers: array, vector, deque, forward_list, list  
* Associative containers: set, map, multiset, multimap  
* Unordered associative containers: unordered_set, unordered_map, unordered_multiset, unordered_multimap  
* Container adaptors: stack, queue, priority_queue  

## 각 컨테이너 별 시간복잡도  

https://users.cs.northwestern.edu/~riesbeck/programming/c++/stl-summary.html

## vector란?
크기가변배열.  
vector는 배열과 마찬가지로 원소들이 하나의 메모리 블록에 연속하게 저장.  
속도적인 측면에서는 배열에 비해 떨어지지만 메모리를 효율적으로 관리할 수 있다는 장점  
원소가 추가되거나 삽입될 때 메모리 재할당이 발생할 수 있고 상당한 부하가 발생하게 된다는 점은 단점

## vector vs list

벡터는 어레이처럼 움직이며, 리스트는 양방향 링크드 리스트(forward_list는 단방향).  
때문에 벡터는 조회가 빠르고, 리스트는 추가/제거가 빠름.

## set vs map

set은 value만 있고, map은 key-value 형태로 존재함.  
set은 value가 중복 불가, map은 key가 중복 불가

## map vs unordered_map

map은 데이터 넣자마자 key 기준으로 정렬, red-black tree(BST + Self-balancing 기능 추가)  
unordered_map은 key 기준으로 순서따위 없음, hash table  
이 특징은 set에도 동일하게 적용  

# 멀티 쓰레딩에서의 동작 (atomic vs volatile)

## std::atomic이란?

std::atomic 은 원자성을 보존해주기 위해서 사용된다.  
다시 말하자면 mutex 없이도 두 개의 스레드가 동시에 접근해도 data race 문제가 발생하지 않는다.

## volatile이란?

volatile 은 컴파일러가 최적화하며 특정 라인을 수행하지 않는 경우가 없도록 해준다.
```
{
	volatile int vi(0);
	++vi;
	--vi;
}
```
이 경우, 컴파일러의 최적화로 인해 아래 두 줄이 무시될 수 있으나, volatile 선언 된 변수이기 때문에 무조건 연산을 한다.  
즉, 컴파일러의 최적화 연산으로 인한 오동작의 가능성을 배제한다. 또한 여러 쓰레드가 동시에 접근할 수 있다.  
반대로 std::atomic 에서는 무의미한 계산을 무시할 수 있다. 그러나 하나의 쓰레드만 동시에 접근이 가능하다.  
+) std::atomic의 경우, 특정 연산이 있을 때 절대 컴파일러에 의해 임의로 순서를 뒤바꾸지 않는다.  
volatile의 경우, 특정 연산이 있을 때 무조건 해당 연산을 수행하지만 순서의 앞뒤는 보장하지 않는다. (<- 멀티스레딩 보조에 어려움)  

# friend 키워드

private 건 protected 건 friend로 선언된 대상에게는 완벽하게 public으로 작용한다. 대상이 외부이건, 상속 관계도에 있건 그런 것은 중요치 않다.  
접근 지정자(Access Modifier)를 완벽히 무시할 수 있는 예외적인 기능  
'정보를 특정 클래스에게 오픈한다'는 개념으로 사용되며, 때문에 당하는 쪽에서 선언  

# 클래스 vs 구조체

언어 내부 구조로는 같은걸로 아는데. 다른 점은 기본 접근자가 다름.  
struct는 기본이 public vs class는 기본이 private  
C++의 구조체와 C의 구조체는 다름. 다만 C에서 쓰던 것 처럼 C++에서 구조체를 똑같이 쓸 수 있음.  

# 여기의 문제는?

## 1.
```
my_struct_t* bar;
/* ... do stuff, including setting bar to point to a defined my_struct_t object ... */
memset(bar, 0, sizeof(bar));
```
A. memset할 때 sizeof(*bar) 로 해야한다. bar가 가리키는 실제 메모리 크기만큼 세팅해야 함!

## 2.
```
size_t sz = buf->size();
while ( --sz >= 0 )
{
	/* do something */
}
```
A. 이거 size_t가 unsigned이기 때문에 무한 루프로 돔.

## 3.
```
unsigned char half_limit = 150;
for (unsigned char i = 0; i < 2 * half_limit; ++i)
{
    // do something;
}
```
A. 이 구문은 영원히 돈다. `2 * half_limit` 이 식은 자동으로 int로 바뀌어 300을 나타내고, unsigned char은 영원히 300에 갈 수 없음.

# 파편화 된 C++의 string 관리 방식 (주로 바이트 수에 따른 이슈)

* char = singlebyte character, ASCII  
* w_char = multibyte character, unicode  
* t_char = 컴파일러가 percompile option을 보고, 환경에 맞게 동작하는 코드를 작성할수 있게 만든 Macro  
* LPSTR = long pointer string = char*  
* LPCSTR = long pointer constant string = const char*  
* LPWSTR = long porinter wide string = w_char*  
* LPCWSTR = long porinter constant wide string = const w_char*  
* LPCTSTR = long pointer constant t_string = const tchar*  

이 외에도 std::string 도 있지만, 그건 따로 생각하자.

# 가상 상속이란?

1. 다중상속에서 다이아몬드 구조를 띄게 될 경우 데이터의 중복과 불필요한 생성자 호출을 막기위해 virtual inheritance(가상 상속)을 사용한다.
2. 가상 상속시 vbptr이라는 offset을 가르키는 포인터가 생성되며, virtual로 상속된 클래스는 메모리 구조에서 제일 아래로 가게된다.
3. 시작 offset은 0이 될수도 있고 -(음수)가 될 수도 있다.
4. 이 때 offset을 모르기 때문에 넉넉하게 메모리를 잡고, 계산하기 위해 성능을 투자함.
	= 기존 데이터 크기보다 더 커질 수 있으며, 성능 저하를 야기할 수 있다.

# 다이아몬드 상속 문제

*We cannot create hybrid inheritance using multiple and hierarchical inheritance.*  
A -> B, A-> C 인 상황에서 B, C -> D 형태로 상속받는 것을 말함.  
만약 A에서 abstract method가 하나 있고, B와 C에서 각각 구현했을 때, D에서 구현하지 않은 method를 call했다면 무슨 method를 동작할 지 모르게 됨.  
그래서 이 때는 가상 상속을 써도 된다 카더라...

# Tips.

* (1+3)[a] is the same as a[1+3]

* for문 돌 때 ++i가 i++보다 유리함. 아래 구문을 보자.
```
Foo& Foo::operator++()   // called for ++i
{
    this->data += 1;
    return *this;
}
```
```
Foo Foo::operator++(int ignored_dummy_value)   // called for i++
{
    Foo tmp(*this);   // variable "tmp" cannot be optimized away by the compiler
    ++(*this);
    return tmp;
}
```

* 부모 클래스를 상속받아 자식 클래스를 만들 때, 자식 클래스에서 destructor를 정의하려면 부모 클래스의 destructor가 virtual이어야 함.  
아니면 자식 클래스의 destructor가 콜이 안되어서 leak 발생할 수 있음.

* *The volatile keyword informs the compiler that a variable may change without the compiler knowing it.*  
volatile로 선언된 개체는 컴파일러가 효율 등을 고려해 optimize 관점에서 코드를 임의로 변경(의미 없는 코드 삭제나 실행 순서 변경)을 시키지 않으며,  
volatile로 선언된 개체는 메모리에 할당되고 작업 요청시 직접(해당 주소에), 바로(효율을 위한 지연 없이) 처리된다.
