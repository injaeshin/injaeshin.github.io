# EC# - 챕터 3

## 제네릭 활용

- 타입 매개변수 제약 조건이란?
    
    https://learn.microsoft.com/ko-kr/dotnet/csharp/programming-guide/generics/constraints-on-type-parameters
    

### 아이템 18: 반드시 필요한 제약 조건만 설정하라

- 올바른 작업을 수행하기 위한 최소한의 제약 조건만을 설정해야 한다.
- 제약 조건을 설정하지 않으면 런타임에 더 많은 검증 코드가 들어가야 하고, 자주 형변환을 해야고 리플렉션을 사용해야 한다.
- 불필요한 제약 조건을 설정하면 해당 조건에 부합하기 위해 과도하게 추가 작업을 해야 한다.
- 컴파일러의 제한 조건 기준은 System.Object 가 정의하고 있는 최소한의 기능만을 가지고 가정한다. 이는 System.Object가 제공하는 기능만을 사용한다는 이야기다.
    
    ```csharp
    // 타입 매개변수 T 에 제약 조건이 없는 경우
    public static bool AreEqual<T>(T left, T right)
    {
    	if (left == null)
    		return right == null;
    
    	if (left is IComparable<T>)
    	{
    		IComparable<T> lval = left as IComparable<T>;
    		if(right is IComparable<T>)
    			return lval.CompareTo(right) == 0;
    		else
    			throw new ArgumentException("Type does not implement IComparable<T>", nameof(right));
    	}
    	else
    	{
    		throw new ArgumentException("Type does not implement IComparable<T>", nameof(left));
    	}
    }
    
    // 제약 조건 추가
    public static bool AreEqual2(T left, T right) where T : IComparable<T>
    										=> left.CompareTo(right) == 0;
    ```
    
    - 제약 조건이 추가되면 컴파일 타임에 발생할 수 있는 오류를 확인할 수 있다.
    - 런타임 오류를 줄일 수 있으며, 오용 가능성도 낮출 수 있다.
- 최소한의 제약 조건과, 반드시 필요한 기능만 제약 조건으로 설정해야 한다.
    
    ```csharp
    public static bool AreEqual<T>(T left, T right) => left.Equals(right)
    ```
    
    - 위 코드는 System.Object.Equals 가 호출된다.
    - 하지만 IEquatable<T> 의 제약 조건이 정의 되어 있었다면, IEquatable<T>.Equals() 가 호출되었을 것이다.
- 기본 생성자를 제약조건으로 설정
    - default() 연산자는 값 타입인 경우는 0, 참조 타입인 경우는 null 을 반환한다.
- 과도한 제약 조건을 설정하면, 사용 빈도가 떨어질 것이다. 제네릭 타입을 만드는 이유는 다양한 시나리오에서 적용할 수 있는 범용 타입을 정의하기 위한 것임을 잊지 말자.