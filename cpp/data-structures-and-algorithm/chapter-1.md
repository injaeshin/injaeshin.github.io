- 코딩 테스트를 위한 자료 구조와 알고리즘 with C++

## 1장 리스트, 스택, 큐

### 연속된 자료 구조와 연결된 자료 구조

- 연속된 자료 구조(contiguous data structures)    
    - 모든 원소를 단일 메모리 청크(chunk)에 저장함
    - 모든 원소가 같은 타입 (같은 메모리를 사용)
    - 다음 원소로 이동은 현재 위치(BA, Base Address) + 메모리 타입 크기 * n 으로 이동 가능
    원소에 곧바로 접근할 수 있음 빅오 표기법으로 O(1)
- 연결된 자료 구조(linked data structures)
    - 노드(node)라고 하는 여러 개의 메모리 청크에 데이터를 저장하며, 서로 다른 위치에 데이터가 저장
    - 노드에는 저장할 데이터(data)와 다음 노드를 가르키는 포인터(next)를 가지고 있음
    마지막 노드의 next 는 null 을 가지고 있음
    - 특정 원소에 접근하려면 연결 리스트 내부를 이동해야 찾을 수 있음
    - 원소 접근 시간은 노드 개수에 비례하여 빅오 표기법으로 O(n)

### std::array

- 연속된 메모리를 자동으로 할당하고 해제하는 클래스 템플릿

```cpp
std::array<int, 10> arr1;
arr1[0] = 1;
std::cout << "첫번째 원소" << arr1[0] << "\n";

std::array<int, 4> arr2 = {1, 2, 3, 4};
for (int i = 0; i < arr2.size(); i++)
	std::cout << arr2[i] << " ";
```

- [  ] 연산자를 제공함
인덱스값이 배열의 크기보다 작은지 (올바른지) 검사하지 않음
- at 함수도 제공함
at(idx) 에서 idx 값이 유효하지 않으면 std::out_of_range 예외가 발생
- 다른 함수에 전달하는 기본 방식은 값 또는 참조로 전달할 수 있고, const 를 함께 사용할 수 있음
    
    ```cpp
    #include <iostream>
    #include <array>
    
    template <size_t N>
    void print(const std::array<int, N>& arr)
    {
        for (auto el : arr) {
            std::cout << el << " ";
        }
    }
    
    int main()
    {
        std::array<int, 5> arr = {1, 2, 3, 4, 5};
        print(arr);
        
        return 0;
    }
    ```
    
    1. 반복자(iterator)와 범위 기반 for(range-based for) 문법을 이용하여 접근 가능함
- 첫번째를 나타내는 begin(), 마지막의 다음 위치를 나타내는 end() 함수를 제공함
- 반복자에 증가 연산자(++, —), 덧셈 연산자를 사용하여 이동할 수 있음
- front() - 첫번째 원소에 대한 참조 반환
back() - 마지막 원소에 대한 참조 반환
data() - 실제 데이터 메모리 버퍼를 가리키는 포인터 반환
- 깊은 비교(deep comparison) 를 위한 관계 연산자, 깊은 복사를 위한 복사 할당 연산자(copy-assignment operator) 를 지원함
- 데이터 타입에 크기 비교 (<, >, ≤, ≥, ==, ≠) 를 지원할 경우 관계 연산자를 이용하여 두 배열 객체를 비교할 수 있음
이때는 두 배열의 크기가 같아야 함. 크기가 다른 배열은 서로 다른 타입으로 인식하여 비교 불가
- std::array의 특징 및 단점
    - 의 크기는 컴파일 시간에 결정되는 상수이어야 함. 따라서, 런타임에 변경될 수 없음
    - 크기가 고정되어 있어서 원소를 추가하거나 삭제할 수 없음
    - 메모리 할당 방법을 변경할 수 없음. 항상 스택 메모리를 사용함
- 연습 문제 1 : 동적 크기 배열 구현
    
    ```cpp
    #include <iostream>
    #include <sstream>
    #include <algorithm>
    
    template <typename T>
    class dynamic_array
    {
    	T* data;
    	size_t n;
    
    public:
    	dynamic_array(size_t n)
    	{
    		this->n = n;
    		data = new T[n];
    	}
    
    	dynamic_array(const dynamic_array<T>& other)
    	{
    		n = other.n;
    		data = new T[n];
    
    		for (int i = 0; i < n; i++)
    			data[i] = other[i];
    	}
    
    	~dynamic_array()
    	{
    		delete[] data;
    	}
    
    	T& operator[](int index)
    	{
    		return data[index];
    	}
    
    	const T& operator[](int index) const
    	{
    		return data[index];
    	}
    
    	T& at(int index)
    	{
    		if (index < n)
    			return data[index];
    		throw "Index out of Range";
    	}
    
    	size_t size() const
    	{
    		return n;
    	}
    
    	T* begin() { return data; }
    	const T* begin() const { return data; }
    	T* end() { return data + n; }
    	const T* end() const { return data + n; }
    
    	friend dynamic_array<T> operator+(const dynamic_array<T>& arr1, dynamic_array<T>& arr2)
    	{
    		dynamic_array<T> result(arr1.size() + arr2.size());
    		std::copy(arr1.begin(), arr1.end(), result.begin());
    		std::copy(arr2.begin(), arr2.end(), result.begin() + arr1.size());
    		return result;
    	}
    
    	std::string to_string(const std::string& sep = ", ")
    	{
    		if (n == 0)
    			return "";
    
    		std::ostringstream os;
    		os << data[0];
    
    		for (int i = 1; i < n; i++)
    			os << sep << data[i];
    
    		return os.str();
    	}
    };
    
    struct student
    {
    	std::string name;
    	int standard;
    
    	student() = default;
    	student(const std::string& n, int s) : name(n), standard(s) {}
    };
    
    std::ostream& operator<<(std::ostream& os, const student& s)
    {
    	return (os << "[" << s.name << ", " << s.standard << "]");
    }
    
    int main()
    {
    
    	int nStudents = 3;
    	//std::cout << "1반 학생 수를 입력하세요: ";
    	//std::cin >> nStudents;
    
    	dynamic_array<student> class1(nStudents);
    	class1[0] = student{ "Hello", 10 };
    	class1[1] = student{ "Array", 15 };
    	class1[2] = student{ "World", 20 };
    
    	//for (int i = 0; i < nStudents; i++)
    	//{
    	//	std::string name;
    	//	int standard;
    	//	std::cout << i + 1 << "번째 학생 이름과 나이를 입력하세요: ";
    
    	//	std::cin >> name >> standard;
    	//	class1[i] = student{ name, standard };
    	//}
    
    	class1.at(0) = student{ "John", 8 };
    	std::cout << class1[0].name << " " << class1[0].standard << "\n";
    
    	auto class2 = class1;
    	std::cout << class2.to_string() << "\n";
    
    	auto class3 = class1 + class2;
    	std::cout << class3.to_string() << "\n";
    
    	return 0;
    }
    ```
    
    - 위 코드를 이해하려면 몇 가지 사전 지식이 필요합니다. 아래의 항목들을 미리 학습하시면 코드 이해에 도움이 될 것입니다:
    1. **템플릿 (Templates)**: C++의 템플릿은 일반화된 코드를 생성하기 위한 매커니즘입니다. 클래스 템플릿, 함수 템플릿 등 다양한 형태로 사용됩니다. 템플릿은 일반적인 코드를 작성하고, 여러 다른 타입에 대해 동작하도록 할 수 있게 합니다.
    2. **std::array**: `std::array`는 표준 라이브러리에서 제공하는 고정 크기 배열 컨테이너입니다. C 스타일 배열과 달리 크기가 컴파일 타임에 결정되며, 편리한 멤버 함수와 연산자 오버로딩을 제공합니다.
    3. **R-value 참조 (R-value References)**: C++11에서 소개된 개념으로, 임시 객체나 우측값에 대한 참조를 지원합니다. 이를 사용하여 불필요한 객체 복사를 피하고 효율적인 이동 연산을 수행할 수 있습니다.
    4. **std::forward**: `std::forward`는 참조를 전달하면서 이동과 복사를 정확하게 처리하기 위해 사용됩니다. 이를 사용하여 오버로딩된 함수나 생성자에 참조를 완벽하게 전달할 수 있습니다.
    5. **템플릿 메타프로그래밍 (Template Metaprogramming)**: 템플릿을 사용하여 컴파일 타임에 코드를 생성하거나 조작하는 기법입니다. 템플릿 매개변수를 사용하여 프로그램을 작성할 수 있습니다.
    6. **std::common_type**: `std::common_type`은 여러 타입의 공통 타입을 계산하는 메타함수입니다. 주어진 타입들의 공통된 부모 타입을 찾아줍니다.
    
    이러한 개념들을 숙지하면 위의 코드를 이해하는 데 도움이 될 것입니다. 이런 개념들은 C++ 프로그래밍에서 꽤 고급 주제이므로 차근차근 학습하면서 코드 이해를 높여나가시는 것이 좋습니다.
    
    - `auto` 키워드와 `->`를 함께 사용하여 반환 타입을 지정하는 것은 코드의 가독성과 유연성을 높이기 위한 기법입니다. 이를 사용하는 이유는 몇 가지가 있습니다:
    1. **복잡한 반환 타입 표현**: 함수의 반환 타입이 복잡하거나 긴 표현식으로 표현되어 가독성이 떨어지는 경우, `auto`와 `>`를 사용하여 명확하게 반환 타입을 나타낼 수 있습니다. 이렇게 하면 코드를 이해하기 쉬워집니다.
    2. **템플릿과 추론**: 위 코드에서 사용된 `std::common_type`과 같은 템플릿을 사용할 때, 템플릿 매개변수로부터 타입을 추론하여 반환 타입을 설정할 수 있습니다. `auto`와 `>`를 사용하면 컴파일러가 이러한 타입 추론을 수행할 수 있게 됩니다.
    3. **코드 변경 용이성**: 반환 타입을 명시적으로 지정하면 반환 타입이 변경되는 경우 해당 함수의 반환 타입을 일일이 수정해야 합니다. 그러나 `auto`와 `>`를 사용하면 반환 타입이 코드 내의 변화에 따라 자동으로 조정됩니다.
    4. **함수 템플릿과 일반 함수 통일성**: 함수 템플릿과 일반 함수 모두에서 `auto`와 `>`를 사용하여 반환 타입을 표현할 수 있습니다. 이를 통해 템플릿과 일반 함수 간의 유사성을 유지하면서 코드를 작성할 수 있습니다.
    
    코드에서 `auto`와 `->`를 사용하여 반환 타입을 나타내는 것은 타입 추론과 코드 유지보수 측면에서 이점을 제공합니다. 코드가 간결해지며, 함수의 반환 타입을 확실히 인식할 수 있습니다.
    
- 연습 문제 2 : 빠르고 범용적인 데이터 저장 컨테이너 만들기
    
    ```cpp
    #include <array>
    #include <iostream>
    #include <type_traits>
    
    template <typename... Args>
    auto build_array(Args &&... args) -> std::array<typename std::common_type<Args...>::type, sizeof...(args)>
    {
        using commonType = typename std::common_type<Args...>::type;
        return { std::forward<commonType>(args)... };
    }
    
    int main()
    {
        auto data = build_array(1, 0u, 'a', 3.2f, false);
        for (auto i : data)
            std::cout << i << " ";
        std::cout << std::endl;    
    }
    ```
    

### std::vector

- 벡터는 원소 크기를 지정하지 않고 선언할 수 있음. 명시적으로 지정하지 않거나, 초깃값을 유추할 수 없을 경우 컴파일러 구현 방법에 따른 용량(capacity)을 갖는 벡터가 생성됨
- push_back
[새 원소를 추가할 공간이 있는 경우]
- 마지막 원소 다음에 새로운 원소 추가
- 벡터 크기를 1만큼 증가
- O(1) 만큼 걸림
[할당된 메모리 공간이 가득 찬 경우]
- 2 * size 크기의 메모리를 새로 할당함
- 기존 원소를 전부 새로 할당한 곳으로 복사/이동함
- 데이터 포인터를 새로 할당한 메모리 주소로 지정
- 마지막 원소 다음에 새로운 원소 추가
- 벡터 크기를 1만큼 증가
- O(n) 만큼 걸림
- insert
원하는 위치에 원소를 추가할 수 있음
필요한 경우 메모리를 새로 할당하는 작업도 수행함
O(n) 만큼 걸
- emplace_back, emplace
push_back, insert 함수는 추가할 원소를 임시로 생성할 후 벡터 버퍼 내부 위치로 복사 또는 이동을 수행함.
emplace_back, emplace 함수는 새로운 원소가 추가될 위치에 해당 원소를 생성하는 방식으로 최적화함
- pop_back
벡터의 맨 마지막 원소를 제거하며 벡터 크기가 1만큼 줄어듭니다.
O(1) 만큼 걸림
- erase
특정 위치의 원소를 삭제한 후 뒤쪽의 원소를 모두 앞으로 이동합니다.
O(n) 만큼 걸림

### std::forward_list

- 기본적인 연결 리스트의 성능을 유지하면서 추가적인 기능을 제공하고 있음
성능을 이유로 전체 리스트 크기를 반환하거나, 첫 원소를 제외한 나머지 원소에 직접 접근하는 기능을 제공하지 않음.
- 처음 원소에 접근하는 front 함수는 제공하지만, 반대 방향의 원소로 이동하는 back 함수는 없음
- 삽입
    - 원소 삽입은 push_front와 insert_after 함수를 사용함
    연결 리스트에 새로운 원소를 추가하면 현재 원소에서 다음 원소를 연결해야 하기 때문에 next 포인터를 수정함 이로 인해 insert_after를 사용하여 현재 원소의 다음에 원소를 추가함
    또는, 가장 첫번째로 원소를 추가 후 next 를 연결함
    - 시간 복잡도는 O(1)
    - emplace_front, emplace_after 함수도 제공함
    삽입 함수와 같은 기능을 수행하지만 추가적인 복사 또는 이동을 하지 않음
- 삭제
    - pop_front, erase_after 함수를 사용하여 삭제함
    - 범위 삭제도 가능함
    - 원소 이동이 필요하지 않으므로 시간 복잡도는 O(1)
    - remove, remove_if 함수로 삭제 조건을 추가할 수 있음
    - 저장된 데이터 타입에서 등호 연산이 지원해야 사용 가능함
- 연습 문제 3: 연결 리스트에서 remove_if 함수를 이용한 조건부 원소 삭제
    
    ```cpp
    #include <string>
    #include <iostream>
    #include <forward_list>
    
    struct citizen
    {
    	std::string name;
    	int age;
    
    	citizen() = default;
    	citizen(const std::string& n, int a) : name(n), age(a) {}
    };
    
    std::ostream& operator<<(std::ostream& os, const citizen& c)
    {
    	return (os << "[" << c.name << ", " << c.age << "]");
    }
    
    int main()
    {
    	std::forward_list<citizen> citizens = {
    		{"Kim", 22}, {"Lee", 25}, {"Park", 18}, {"Jin", 16}
    	};
    
    	auto citizens_copy = citizens;
    
    	std::cout << "시민들: ";
    	for (const auto& c : citizens)
    		std::cout << c << " ";
    	std::cout << "\n";
    
    	citizens.remove_if([](const citizen& c) {return (c.age < 19);});
    
    	std::cout << "투표권이 있는 시민들: ";
    	for (const auto& c : citizens)
    		std::cout << c << " ";
    	std::cout << "\n";
    
    	std::cout << "복사 객체: ";
    	for (const auto& c : citizens_copy)
    		std::cout << c << " ";
    }
    ```
    
    - remove 와 remove_if 는 시간 복잡도는 O(n)
- 그 외
    - sort - 원소 정렬 O(nlogn)
    - reverse - 원소 역순 정렬 O(n)
    - unique - 고유 원소만 남겨둠
    인접하여 중복되는 원소에 대해서만 비교 후 하나만 남겨둠
    때문에 정렬

### std::list

- 이중 연결 리스트 (doubly-linked list)
- 다음 노드와 이전 노드가 연결되어 있다.
    
    ```cpp
    struct doubly_linked_list_node
    {
    	int data;
    	doubly_linked_list_node* next;
    	doubly_linked_list_node* prev;
    }
    ```
    
- 삽입을 위한 insert, emplace 함수가 있음
push_back, emplace_back 함수로 맨 뒤에 추가 가능함
- pop_back 으로 삭제 가능함
- 연습 문제 6: std::list의 삽입 또는 삭제 함수 사용하기
    
    ```cpp
    std::list<int> list1 = { 1, 2, 3, 4, 5 };
    list1.push_back(6);
    // next는 이터레이터의 다음을 반환한다.
    list1.insert(next(list1.begin()), 0);
    list1.insert(list1.end(), 7);
    list1.pop_back();
    
    for (auto i : list1)
    	std::cout << i << " ";
    ```
    

### std::deque

- 다음 조건을 만족해야 한다.
    - push_front(), pop_front(), push_back(), pop_back() 동작이 O(1) 시작 복잡도로 동작해야 한다.
    - 모든 원소에 대해 임의 접근 동작이 O(1) 시간 복잡도로 동작해야 한다.
    - 덱 중간에서 원소 삽입 또는 삭제는 O(n) 시간 복잡도로 동작해야 한다.
    (실제로는 최대 n/2 단계로 동작한다.)
- 양방향으로 빠르게 확장할 수 있어야 한다.
모든 원소에 임의로 접근 할 수 있어야 한다.
앞쪽과 뒤쪽으로 모두 확장할 수 있어야 한다.
- 크기가 같은 여러 개의 메모리 청크를 사용하여 데이터를 저장함 (map)
청크의 인덱스 및 크기를 이용하여 특정 위치의 원소가 어느 청크에 저장되어 있는지 알 수 있다. (Block - array)

### std::stack

- 컨테이너 한쪽 끝에서만 원소의 추가 및 삭제를 수행함 - LIFO (Last in First Out, 후입선출)
- empty, size, top, push, pop, emplace 함수를 지원함
- 모든 연산은 시간 복잡도가 O(1)

### std::queue

- FIFO (First In First Out, 선입선출) 기능이 있음
- push, pop, front, back 함수를 지원함
- 시간 복잡도는 O(1)

### std::priority_queue

- 가장 작은/큰 원소에 빠르게 접근할 수 있는 자료 구조 (한가지만 설정할 수 있다)
- 최소/최대 원소에 접근하는 동작은 O(1)의 시간 복잡도를 가진다.
- 원소 삽입은 O(logn) 시간 복잡도로 동작함
- 원소 제거는 최소/최대 원소에 대해서만 가능

### 반복자(iterator)

- STL 컨테이너에 대해 공통의 인터페이스를 제공한다.
반복자 연산은 어떠한 컨테이너에서 정의된 반복자인지에 따라서 결정된다.
- 임의 접근 반복자(random access iterator)
    - 벡터와 배열은 연속된 자료구조를 사용하기 때문에 특정 위치의 원소에 곧바로 접근 가능하다.
- 순방향 반복자(forward iterator)
    - 반대 방향으로 이동하는 기능을 제공하지 않음. 이전 노드로 이동하려면 처음 노드부터 시작해서 다시 이동해야 한다.
- 연습 문제 4: 다양한 반복자에서 이동하기
    
    ```cpp
    std::vector<std::string> vec = {
    	"A", "B", "C", "D", "E", "F", "G", "H"
    };
    
    auto it = vec.begin();
    std::cout << "0: " << *it << "\n";
    
    it += 5;
    std::cout << "+5: " << *it << "\n";
    
    advance(it, -3);
    std::cout << "-3: " << *it << "\n";
    
    std::forward_list<std::string> fwd(vec.begin(), vec.end());
    
    auto it1 = fwd.begin();
    std::cout << "0: " << *it1 << "\n";
    
    advance(it1, 5);
    std::cout << "+5: " << *it1 << "\n";
    ```
    
- 연습 문제 5: 기본적인 사용자 정의 컨테이너 만들기
    
    ```cpp
    #include <iostream>
    #include <algorithm>
    
    struct singly_ll_node
    {
    	int data;
    	singly_ll_node* next = nullptr;
    };
    
    class singly_ll
    {
    public:
    	using node = singly_ll_node;
    	using node_ptr = node*;
    
    	struct singly_ll_iterator
    	{
    	private:
    		node_ptr ptr;
    
    	public:
    		singly_ll_iterator(node_ptr p) : ptr(p) {}
    
    		int& operator*() { return ptr->data; }
    
    		node_ptr get() { return ptr; }
    
    		singly_ll_iterator& operator++()
    		{
    			ptr = ptr->next;
    			return *this;
    		}
    
    		singly_ll_iterator operator++(int)
    		{
    			singly_ll_iterator result = *this;
    			++(*this);
    
    			return result;
    		}
    
    		friend bool operator==(const singly_ll_iterator& left, const singly_ll_iterator& right)
    		{
    			return left.ptr == right.ptr;
    		}
    
    		friend bool operator!=(const singly_ll_iterator& left, const singly_ll_iterator& right)
    		{
    			return left.ptr != right.ptr;
    		}
    	};
    
    public:
    	singly_ll() = default;
    	singly_ll(const singly_ll& other) : head(nullptr)
    	{
    		if (other.head)
    		{
    			head = new node{ 0, nullptr };
    			auto cur = head;
    			auto it = other.begin();
    			while (true)
    			{
    				cur->data = *it;
    
    				auto tmp = it;
    				++tmp;
    				if (tmp == other.end())
    					break;
    
    				cur->next = new node{ 0, nullptr };
    				cur = cur->next;
    				it = tmp;
    			}
    		}
    	}
    
    	singly_ll(const std::initializer_list<int>& init_list) : head(nullptr)
    	{
    		for(auto it = std::rbegin(init_list); it != std::rend(init_list); it++)
    			push_front(*it);
    	}
    
    private:
    	node_ptr head;
    
    public:
    	void push_front(int val)
    	{
    		auto new_node = new node{ val, nullptr };
    		if (head != nullptr)
    			new_node->next = head;
    
    		head = new_node;
    	}
    
    	void pop_front()
    	{
    		auto first = head;
    		if (head != nullptr)
    		{
    			head = head->next;
    			delete first;
    		}
    	}
    
    	singly_ll_iterator begin() { return singly_ll_iterator(head); }
    	singly_ll_iterator end() { return singly_ll_iterator(nullptr); }
    	singly_ll_iterator begin() const { return singly_ll_iterator(head); }
    	singly_ll_iterator end() const { return singly_ll_iterator(nullptr); }
    };
    
    int main()
    {
    	singly_ll sll = { 1, 2, 3 };
    	sll.push_front(0);
    
    	std::cout << "첫 번째 리스트";
    	for (auto i : sll)
    		std::cout << i << " ";
    	
    	std::cout << "\n";
    
    	auto sll2 = sll;
    	sll2.push_front(-1);
    	std::cout << "두번째 - 맨 앞에 -1 추가";
    	for (auto i : sll2)
    		std::cout << i << " ";
    
    	std::cout << "\n";
    
    	std::cout << "다시 첫 번째 리스트";
    	for (auto i : sll)
    		std::cout << i << " ";
    
    	std::cout << "\n";
    
    	return 0;
    }
    ```
    
- 양방향 반복자(bidirectional iterators)
- 역방향 반복자(reverse iterator)
- 반복자 무효화

### 참고

- 캐시 지역성
- 시간 복잡도