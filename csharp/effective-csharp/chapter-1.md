# 이펙티브 C#

## 챕터 1 - C# 언어 요소

### 아이템 1 - 지역변수를 선언할 때는 var 를 사용하는 것이 낫다

- 코드를 읽기 위해서 명시적으로 드러내야 하는 경우가 아니라면, 지역변수는 var를 사용하는 것이 좋다. C# 에서는 특정 변수를 var 로 선언하면 동적 타이핑이 선언되는 것이 아니라 할당 연산자의 오른쪽 타입을 확인하여 왼쪽 변수의 타입을 결정하게 됩니다.
- 컴파일러가 올바른 타입을 유추 할 수 있다는 것은 개발자가 할 수 있는 실수를 줄여줄 수 있다는 것이 됩니다.
    
    ```csharp
    IEnumerable<string> q = 
    		from c in db.Customers
    		select c.ContactName;
    
    var q2 = q.Where(s => s.StartWith(start));
    ```
    
    1. 첫번째 LINQ 쿼리 문장이 수장되면 DB 로 쿼리 문장을 전달하여 데이터를 획득한다.
    2. 획득한 데이터를 기반으로 두번째 문장이 실행된다.
    
    ```csharp
    var q =
    	from c in db.Customers
    	select c.ContactName;
    
    var q2 = q.Where(s => s.StartWith(start));
    ```
    
    1. 첫번째 LINQ 쿼리 문장이 수행되면 DB로 쿼리 문장을 전달하지 않는다.
    2. 두번째 LINQ 쿼리 문장이 수행하면 1. 의 쿼리 문장에 where 절을 추가하여 완성된 SQL 쿼리 구문을 작성하게 된다.
- 변수를 읽을 때 타입명을 모두 명시하는 것보다는 var를 사용하면 변수의 의미 파악에 더 집중 할 수 있다. 단, 이때는 타입명을 유추할 수 있는 변수의 이름이 사용되어야 합니다.
    
    ```csharp
    // 반환 타입을 짐작하기 어려움
    var result = someObject.DoSomeWork(anotherParameter);
    
    // 의미가 좀 더 명확함 (변수 이름을 통한 무언가의 product 타입)
    var HighestSellingProduct = someObject.DoSomeWork(anotherParameter)
    ```
    
- 내장 타입의 경우 var를 함께 사용하면 타입 추론으로 인한 문제가 발생 할 수 있다. 이때는 명확하게 명시해주어야 합니다.

```csharp
// GetMagicNumber() 의 타입을 추론하여 f 의 타입이 설정됨
var f = GetMagicNumber();
// 계산이 필요한 상수들은 f와 동일한 타입으로 변환 후 계산함
var total = 100 * f / 6;

Console.WriteLine($"Declared Type: {total.GetType().Name}, Value: {total}");
```

### 아이템 2 - const 보다는 readonly 가 좋다

- 컴파일 상수(const) 와 런타임 상수(readonly) 를 구분하여 사용해야 합니다.
- 두 상수의 차이는
    1. 컴파일 상수는 컴파일 시점에 정해진 값으로 정의된다. 정의에 따라 정적 상수이므로 모든 인스턴스가 동일한 값을 가지게 된다.
    2. 런타임 상수는 상수의 값이 런타임에 평가된다. readonly 변수에 대한 참조 코드를 생성한다.
    멤버 초기화 구문이나 생성자를 통해 초기화가 가능하다.
- readonly 대신 const를 사용했을 때 이점은 성능이 빠르지만, 성능 개선 효과가 크지 않고, 유연성이 떨어진다. 변경되지 않는 값을 사용할 때를 제외하면 const 보다는 readonly 를 사용하는 것이 좋습니다.

### 아이템 3 - 캐스트보다는 is, as 가 좋다

- 사용자의 의도를 명확하게 표현할 수 있는 is 와 as 연산자를 사용하는게 좋습니다.
is 와 as 연산자는 거의 항상 예상대로 동작하며 대상 객체를 올바르게 형변환할 수 있을 경우에만 성공합니다.
- C# 에서 형변환을 수행하는 방법에는 as 연산자를 사용하는 방법과 컴파일러 캐스트 연산자 구문을 사용하는 두가지 방법이 있습니다.
- as 연산자는 형변환을 수행하는 경우 캐스팅 연산자보다 안전하고 런타임에 더 효율적으로 동작합니다. 객체를 특정 타입으로 명시적 캐스팅 하지만 캐스팅이 불가능하면 null을 반환합니다.
    
    ```csharp
    object o = Factory.GetObject();
    MyType t = o as MyType;
    if (t != null)
    {	// MyType 타입의 t 객체 사용 }
    else
    { // 오류 보고 }
    ```
    
    1. 작성하기도 좋고, 읽기도 편하다.
    2. try/catch 문이 없어 성능도 좋다.
- as 연산자는 객체를 다른 타입으로 형변환 하려면 지정한 타입이거나 혹은 지정한 타입을 상속한 타입이어야 합니다. 그 외의 경우는 모두 실패합니다.
사용자 정의 형변환 연산자는 객체의 런타임 타입이 아닌 컴파일 타입에 맞춰 수행됩니다.
- 값 타입의 객체를 null 이 될 수 없는 객체로의 변환은 불가능하지만, nullable 타입으로 형변환은 가능합니다.
    
    ```csharp
    object o = Factory.GetValue();
    int i = o as int; // 불가능
    
    var i = o as int ?; // nullable 타입으로는 가능
    if (i != null)
    	Console.WriteLine(i.Value);
    ```
    
- is 연산자는 런타임에 객체의 타입을 확인하는 용도로 사용되며, 형변환 전 검증을 위해 사용하면 더 방어적 코드를 작성할 수 있습니다.

### 아이템 4 - string.Format() 을 보간 문자열로 대체하라

- C# 6.0 에서 새로 도입된 문자열 보간을 사용하면 오류 예방과 가독성이 높아집니다.
    
    ```csharp
    Console.Writeline("This value of pi is {0}", Math.PI); // 기존 문자열 사용
    
    Console.Writeline($"This value of pi is {Math.PI}"); // 보간 문자열 사용
    ```
    
    1. 파라미터 순서를 수정하면서 벌어지는 오류를 방지할 수 있다. ({0}, {1}…)
    2. 문자열을 그대로 읽을 수 있어서 가독성이 높아진다.
- 파라미터로 값 타입을 그대로 전달하면 object 타입으로 변경되어 전달됩니다. 이로 인한 박싱이 수행됩니다. 이를 개선하기 위해서는 문자열로 변경하여 전달하여야 합니다.
    
    ```csharp
    Console.Writeline($"This value of pi is {Math.PI.ToString()}");
    ```
    
- 추가적으로 가능한 문법들
    
    ```csharp
    // 문자열 포매팅
    Console.Writeline($"This value of pi is {Math.PI.ToString("F2")}");
    // 문자열 포매팅 함축
    Console.Writeline($"This value of pi is {Math.PI:F2}");
    // 3항 연산자
    Console.Writeline($"This value of pi is {(round ? Math.PI.ToString() :
    																									Math.PI.ToString("F2")}");
    // null 조건 연산자
    Console.Writeline($"The customer's name is {c?.Name ?? "Name is missing"}");
    ```
    

### 아이템 5 - 문화권별로 다른 문자열을 생성하려면 FormattableString을 사용하라

### 아이템 6 - nameof() 연산자를 적극 활용하라

- nameof 키워드는 심볼 그 자체를 해당 심볼을 포함하는 문자열로 대체합니다.
- 심볼의 이름을 평가하며 (이름 값을 가지고 온다) 타입, 변수, 인터페이스, 네임스페이스 등 대부분에서 사용할 수 있습니다.
    
    ```csharp
    if (value != name)
    {
    		name = value;
    		PropertyChanged?.Invoke(this, new PropertyChangedEVentArgs(nameof(Name)));
    }
    
    if (thisCantBeNull == null)
    		throw new ArgumentNullException(nameof(thisCantBeNull),
    					"We told you this cant be null");
    ```
    
    1. nameof 연산자를 사용하면 속성의 이름을 변경할 경우 전달해야 하는 문자열도 변경된다.
    2. 특성 매개변수로 문자열을 전달해야 할 경우 nameof 연산자를 사용할 수 있다.
    3. Web API 의 route 를 지정할 때 유용하다.

### 아이템 7 - 델리게이트를 이용하여 콜백을 표현하라

- 상호 통신을 수행해야 할 경우 클래스간의 결합도를 낮추고 싶다면 인터페이스 보다는 델리게이트를 사용하는 것이 좋습니다.
- 런타임에 통지 대상을 설정하거나, 한개의 대상 또는 다수의 대상에게 통지를 보낼 수 있습니다.
- 자주 사용되는 델레게이트 정의
    - Preficate<T> - bool 을 반환
    - Func<> - 여러 매개변수를 받아 단일 결과를 반환
    - Action<> - 여러 매개변수를 받아 void를 반환
- 델리게이트는 어떤 예외도 잡지않으며, 예외가 발생하면 함수 호출과정이 중단됩니다.
- 마지막으로 호출된 대상 함수의 반환값이 델리게이트의 반환값으로 간주됩니다.
    
    ```csharp
    //
    Func<bool> cp = () => CheckWithUser();
    cp += () => CheckWithSystem(); // 최종 반환값. CheckWithUser 의 반환값을 무시함
    c.LengthyOperation(cp);
    
    //
    bool bContinue = true;
    foreach (ComplicatedClass cl in container)
    {
    		cl.DoLengthyOperation();
    		foreach (Func<bool> pr in pred.GetInvocationList())
    				bContinue &= pr();
    		if (!bContinue)
    				return;
    }
    ```
    
    1. 델리게이트 반환값은 멀티 캐스트에서 마지막에 호출된 함수의 반환값이 되며, 다른 반환값을 모두 무시된다. (위 예제에서 CheckWithUser의 반환값을 무시된다)
    2. 델리게이트에 포함된 호출 대상 콜백 함수를 직접 다뤄야 한다. (추가된 메서드의 리스트를 직접 호출하는 방식)

### 아이템 8 - 이벤트 호출 시에는 null 조건 연산자를 사용하라

- 이벤트 호출을 위해 사용되는 이벤트 핸들러가 결합되어 있지 않다면 NullReferenceException 예외가 발생한다. 이벤트 발생 전 이벤트 핸들러가 결합되었는지 확인이 필요하다.
    
    ```csharp
    //
    counter++;
    var handler = Updated;
    if (handler != null)
    		handler(this, counter);
    
    //
    counter++;
    Updated?.Invoke(this, counter);
    ```
    
    1. Updated 이벤트 핸들러를 handler 에게 얕은 복사(shallow copy)한다. (여러 개의 이벤트 핸들러가 포함된 리스트의 복사본) 만약 다른 스레드가 이벤트의 대한 구독을 취소해도 기존 객체의 내용은 수정되지만, 지역 변수의 내용은 변경되지 않는다.
    2. null 조건 연산자(?.) 를 사용하면 안전하게 이벤트를 호출할 수 있다. 이 연산자는 원자적(Atomic)으로 수행되기 때문에 안전하게 실행된다.
    원자적(Atomic) - 특정 작업이 한 번에 완전히 실행되거나 전혀 실행되지 않는다는 것.

### 아이템 9 - 박싱과 언박싱을 최소화하라

- 박싱 - 값 타입 객체를 참조 타입 객체로 변환하는 과정입니다. (스택 값 → 힙에 할당)
언박싱 - 참조 타입 객체로부터 값 타입 객체의 복사본을 가지고 오는 방법 (힙 → 스택)
- 박싱과 언박싱에서 임시 객체가 생성될 수 있어 성능에 영향을 끼친다.
    
    ```csharp
    //
    Console.WriteLine(@"A few numbers:{firstNumber}, {secondNumber}");
    //
    Console.WriteLine(@"A few numbers:{firstNumber.ToString()}, {secondNumber.ToString()}");
    ```
    
    1. 보간 문자열을 만드는 작업은 System.Object 객체에 대한 배열을 사용한다. 정수값은 값 타입이므로 값을 문자열로 변환하기 위한 함수를 호출하려면 반드시 박싱을 수행해야 한다.
    2. 값 타입의 객체를 전달하지 말고, 문자열 인스턴스를 전달하는 것이 좋다.
- 값 타입을 System.Object 타입이나 인터페이스 타입으로 변경하는 코드는 가급적 작성하지 말아야 합니다.
- .net 2.0 에 추가된 제네릭 컬렉션을 사용해야 합니다.

### 아이템 10 - 베이스 클래스가 업그레이드된 경우에만 new 한정자를 사용하라

- 베이스 클래스에서 virtual로 선언하지 않은 멤버를 재정의하려는 경우 new 한정자를 사용할 수 있습니다. new 한정자는 비가상 메서드를 가상 메서드로 만드는 것이 아니라 클래스의 명명 범위 내에 새로운 메서드를 추가하는 역할을 합니다.
- new 한정자를 사용해도 좋은 경우는 베이스 클래스에서 이미 사용하고 있는 메서드를 재정의하여 완전 새로운 베이스 클래스를 만들어야 하는 경우입니다.
```csharp
public class BaseWidget
{
		public void NormalizeAllValues()
		{
		}
}

public class MyWidget : BaseWidget
{
		// 메서드 재정의
		public new void NormalizeValues()
		{
				base.NormalizeValues();
		}
}
```