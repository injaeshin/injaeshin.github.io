# 자료구조와 알고리즘 with C++

- 코딩 테스트를 위한 자료 구조와 알고리즘 with C++

## 1장 리스트, 스택, 큐

### 연속된 자료 구조와 연결된 자료 구조

- 연속된 자료 구조(contiguous data structures)
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4f020c85-fbf1-404b-aeb2-3dcee6a3f1fa/Untitled.png)
    
    - 모든 원소를 단일 메모리 청크(chunk)에 저장함
    - 모든 원소가 같은 타입 (같은 메모리를 사용)
    - 다음 원소로 이동은 현재 위치(BA, Base Address) + 메모리 타입 크기 * n 으로 이동 가능
    원소에 곧바로 접근할 수 있음 빅오 표기법으로 O(1)
- 연결된 자료 구조(linked data structures)
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/735375b7-4bab-4494-a5d0-87618d711905/Untitled.png)
    
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