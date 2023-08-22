# 2부 타입 설계

- 제프리 리처의 CLR via C# - 4판
- 목차 - https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=49012922

## 4장. 타입의 기초

### 모든 타입은 System.Object를 상속한다.

- 모든 타입들은 System.Object 타입을 기반으로 생성된다. 때문에, System.Object의 protected, public 으로 선언된 메서드들은 내부적으로 이용할 수 있다.
public - Equals, GetHashCode, ToString, GetType
protected - MemberwiseClone, Finalize
- CLR의 모든 객체들은 반드시 new 연산자에 의해 만들도록 하고 있다. 이때 New연산자는
    1. 할당하려는 타입과 Object 타입을 포함한 모든 타입과, 필드들의 바이트 수를 계산한다.
    2. 관리 힙에는 생성 객체에 필요한 바이트 수 + 타입 객체 포인터(Type Object Pointer) + 동기화 블록 인덱스(Sync Block Index) 만큼의 메모리가 할당되며, 모든 바이트를 0으로 초기화한다.
    3. 객체 타입 포인터, 동기화 블록 인덱스를 초기화 한다.
    4. 클래스 타입의 인스턴스 생성자와 인수가 new 전달된다. 이후 상속 조건에 따라 생성자가 호출된다.

### 타입 간 캐스팅하기

- 기본 타입 변환은 특별한 문법이 필요 없음
파생 타입의 형 변환은 명시적 문법을 사용한다. (실행시에 코드가 실패할 수 있기 때문)
    
    ```cpp
    Object o = new Employee();
    
    Employee e = (Employee) o;
    ```
    
- is 연산자와 as 연산자로 캐스팅하기
    - is - 대상 타입과 호환성이 있는가? true / false 로 반환
    - as - 대상 타입으로 예외없이 캐스팅 진행함. 실패시 null 반환

### 네임스페이스와 어셈블리

- 네임 스페이스 - 관련있는 타입들의 논리적 그룹
- 어셈블리 - 코드와 리소스의 집합. 논리적 단위, 구성 요소(Component)

### 실행 시점과의 연관성

## 5장. 기본, 참조, 값 타입

## 6장. 타입과 멤버의 기본

## 7장. 상수와 필드

## 8장. 메서드

## 9장. 매개변수

## 10장. 속성

## 11장. 이벤트

## 12장. 제네릭

## 13장. 인터페이스