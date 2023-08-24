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

### 아이템 19: 런타임에 타입을 확인하여 최적의 알고리즘을 사용하라

- 어떤 알고리즘이 특정 타입에 대해 더 효율적으로 동작한다고 생각되면 그냥 그 타입을 이용하도록 코드를 작성하라. 제네릭의 인스턴스화는 런타임의 타입을 고려하지 않으며 컴파일타임의 타입만을 고려한다.
- 타입에 대한 제약 조건을 거의 사용하지 않고, 타입 매개변수로 지정될 가능성이 있는 타입들의 고유한 특성을 고려하고 특화된 기능들을 최대한 활용하여 제네릭 타입을 만들 수 있어야 한다.
그러면 재사용성이 높으면서도 개별 타입에 최적화된 코드를 작성할 수 있다.
- !?…

### 아이템 20: IComparable<T>와 IComparer<T>를 이용하여 객체의 선후 관계를 정의하라

- .net framework 에서는 객체의 선후 관계를 정의하기 위해서 IComparable<T> 와 IComparer<T> 2개의 인터페이스를 제공한다.
- IComparable<T>는 타입의 기본적인 선후 관계를 정의한다.
IComparer<T>는 기본적인 선후 관계 이외에 추가적인 선후 관계를 정의할 수 있다. 또한, 타입 내에 관계 연산자(<, >, ≤, ≥)를 재정의하면 해당 타입에 최적화된 방식으로 객체의 선후 관계를 판단할 수 있으므로 기본 관계연산자의 구현 기능을 이용할 때 발생할 수 있는 비효율의 문제를 개선할 수 있다.

### 아이템 21: 타입 매개변수가 IDisposable을 구현한 경우를 대비하여 제네릭 클래스를 작성하라

- 제약 조건을 두가지 역할을 한다.
1. 런타임 오류가 발생할 가능성이 있는 부분을 컴파일 타임 오류로 대체할 수 있다.
2. 타입을 명확히 규정하여 사용자에게 도움을 준다.
- 무엇을 해야 하는지만 규정 할 수 있고, 무엇을 하면 안되는지를 정의할 수 없다.
대부분의 경우 요구하는 작업 외 다른 작업을 추가로 수행할 수 있는지에 대해 신경쓰지 않는다.
- 타입 매개변수로 지정하는 타입이 IDisposable을 구현하고 있다면 특별한 추가 작업이 반드시 필요하다.
    
    ```csharp
    public interface IEngine
    {
    	void DoWork();
    }
    
    // Dispose 미 호출로 메모리 누수 발생
    public class EngineDriverOne<T> where T : IEngine, new()
    {
    	public void GetThingDone()
    	{
    		T driver = new T();
    		driver.DoWork();
    	}
    }
    
    // T 가 IDisposable 을 구현하고 있다면 Dispose 호출함
    public void GetThingDone()
    {
    	T dirver = new T();
    	using (driver as IDisposable)
    	{
    		driver.DoWork();
    	}
    }
    ```
    
    1. T 가 IDisposable을 구현하지 않았다면 이 지역변수의 값은 null이 된다.
    2. C# 컴파일러는 이 지역변수의 값이 null인지를 검사한 후 Dispose() 를 호출하도록 코드를 생성한다.
    3. using 문이 null 인 경우 Dispose()가 호출되지 않는다. 반대인 경우 using 블록을 종료할때 Dispose() 가 호출된다.
- 만약 클래스가 파생클래스를 만들 수 있는 타입이라면, 표준 Dispose 패턴 전체를 구현해야 한다.
클래스를 sealed 선언하면 표준 Dispose 패턴을 모두 구현할 필요가 없다.
    
    ```csharp
    public sealed class EngeineDriver2<T> : IDisposable where T : IEngine, new()
    {
    	private Lazy<T> driver = new Lazy<T>( () => new T() );
    	public void GetThingDone() => driver.Value.DoWork();
    	public void DIspose()
    	{
    		if (dirver.IsValueCreated)
    		{
    			var resource = dirver.Value as IDisposable;
    			resource?.Dispose();
    		}
    	}
    }
    ```
    
    1. driver 변수에 대해 Dispose() 메서드를 한번만 호출한다고 보장하지 않는다. 
    2. IDisposable을 구현하는 모든 타입은 Dispose() 를 여러 번 호출하는 경우에도 문제없이 동작하도록 구형해야 한다.
- Dispose 의 호출 책임을 제네릭 클래스 외부로 전담시키고, 객체의 소유권을 제네릭 클래스 외부로 옮기면 new() 제약 조건을 제거할 수 있다.

```csharp
public sealed class EngineDriver<T> where T : IEngine
{
	private T driver;

	public EngineDriver(T driver)
	{
		this.driver = driver;
	}

	public void GetThingsDone()
	{
		drvier.DoWork();
	}
} 
```

### 아이템 22: 공변성과 반공변성을 지원하라

- https://learn.microsoft.com/ko-kr/dotnet/csharp/programming-guide/concepts/covariance-contravariance/variance-in-generic-interfaces
- 특정 타입의 객체를 다른 타입의 객체로 변환하는 성격 (배열, 인터페이스, 델리게이트)
- 공변성(covariance)
    - 파생 클래스 타입 객체를 베이스 클래스 타입으로 형변환
    - out 키워드 (데코레이션) 로 지정함 - 내용 조회는 하지만 수정하지는 않을 것으로 판단함
    
    ```csharp
    string str = "test";
    object obj = str;
    
    IEnumerable<string> strings = new List<string>();
    IEnumerable<object> objects = strings;
    ```
    
- 반공변성(constravariance)
    - 베이스 클래스 타입 객체를 파생 클래스 타입 객체로 형변환
    - in 키워드 (데코레이션) 로 지정함
    
    ```csharp
    //static void SetObject(object o) { }
    Action<object> actObject = SetObject;  
    Action<string> actString = actObject;
    ```
    

### 아이템 23: 타입 매개변수에 대해 메서드 제약 조건을 설정하려면 델리게이트를 활용하라

- 어떤 제네릭 클래스에 대해 타입 매개변수 T가 반드시 Add() 메서드를 가져야 한다는 제약이 있다면
    - Add 메서드를 정의하는 IAdd<T> 인터페이스를 생성한다.
    - IAdd<T> 인터페이스를 제약 조건으로 설정해야 한다.
    - IAdd<T> 를 구현할 클래스를 생성하고, Add 메서드를 정의해야 한다.
    - 여러가지 작업으로 인해 작업량도 많고 코드도 복잡해짐
- 다른 방법으로 델리게이트를 사용하는 방법도 있다.
    - 제약 조건으로 설정하고 싶은 메서드의 원형에 부합하는 델리게이트를 작업하는 것
        
        ```csharp
        public static class Example
        {
        	public static T Add<T>(T left, T right, Func<T, T, T> AddFunc) => AddFunc(left, right);
        }
        
        int a = 6;
        int b = 7;
        int sum = Example.Add(a, b, (x, y) => x + y);
        ```
        

### 아이템 24: 베이스 클래스나 인터페이스에 대해서 제네릭을 특화하지 말라

- 제네릭 메서드의 타입 매개변수로 특정 타입이 주어질 경우 그에 부합하도록 제네릭 특화를 수행하기로 결정했다면, 해당 타입뿐 아니라 이 타입을 상속한 모든 파생 타입에 대해서도 특화를 수행해야 한다. 인터페이스에 대해 특화를 수행하기로 결정했다면 이 인터페이스를 구현하고 있는 모든 타입에 대해서도 특화를 수행해야 한다.
- !?..

### 아이템 25: 타입 매개변수로 인스턴스 필드를 만들 필요가 없다면 제네릭 메서드를 정의하라

- 무작정 제네릭 클래스를 만들것이 아니라, 제네릭 메서드를 만드는것이 도움이 된다.
- 제네릭 클래스는 컴파일러의 입장에서 전체 클래스에 대하여 타입 매개변수에 대한 제약 조건을 고려하여 컴파일해야 하기 때문이다.
- 일반 클래스 내에 제네릭 메서드들을 배치하면 각 메서드별로 제약 조건을 달리 설정할 수 있고, 요청되는 메서드의 원형에 좀 더 정확히 부합하는 메서드를 생성 할 수 있으므로 사용자 입장에서도 메서드를 활요하기가 수월해진다.

### 아이템 26: 제네릭 인터페이스와 논제네릭 인터페이스를 함께 구현하라

- C#의 제네릭이 포함되기 이전에 개발됐던 코드를 무시하기 어려운 상황이다. 고전적인 방식도 함께 지원한다면 라이브러리 활용도를 높일 수 있다.
- 논제네릭 Equals() 메서드 내에서 IEquatable<T>.Equals() 메서드를 호출하도록 구현해야 한다. (GetHashCode 도 재정의해야 한다.)

### 아이템 27: 인터페이스는 간략히 정의하고 기능의 확장은 확장 메서드를 사용하라

- 인터페이스에는 가능한한 최소한의 기능만 정의하고 새로운 동작은 확장 메서드를 활용하면 API를 추가적으로 정의하지 않고도 새로운 기능을 추가할 수 있다.
- 여러 클래스에서 반드시 구현해야 하는 인터페이스를 정의하는 경우 인터페이스 내에 정의하는 멤버의 수를 최소한으로 하기 위해 노력해야 한다.
사용자 편의 기능은 확장 메서드 형태로 구현하는 것이 코드의 양을 줄일 수 있고, 코드량이 감소된다.
- 기존 IComparable<T> 의 CompareTo의 반환값보다 새로운 확장 메서드의 네이밍으로 LessThan, GreaterThanEqual와 같이 명명하면 읽기가 더 쉽다.
확장 메서드를 이용하면 이런 메서드를 구현하기 쉽다.
- 

### 아이템 28: 확장 메서드를 이용하여 구체화된 제네릭 타입을 개선하라

- 구체화된 제네릭 타입을 상속하여 메서드를 추가하기보다는 화장 메서드를 구현하는 편이 훨씬 낫다.
단순한 기능을 제공하는 메서드를 다양하게 재사용할 수 있다.

```csharp
public static IEnumerable<Customer> LostProspects(IEnumerable<Customer> targetList)
{
	IEnumerable<Customer> answer = from c in targetList
																	where DateTime.Now - c.LastOrderDate > TimeSpan.FromDays(30)
																	select c;
	return answer;
}
```

