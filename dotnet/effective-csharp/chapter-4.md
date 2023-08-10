# EC# - 챕터 4

# 4장 LINQ 활용

### 아이템 29: 컬렉션을 반환하기보다 이터레이터를 반환하는 것이 낫다

- 용어
    - 컬렉션(collections)
        - 데이터 요소들을 저장하고 관리하는 구조
        - 컬렉션을 생성하면 해당 데이터가 메모리에 할당됨
        - List<T> / Dictionary<TKey, TValue> / HashSet<T> / Queue<T> …
    - 시퀀스
        - 데이터 요소들의 순차적인 나열, 필요한 경우에만 생성하거나 계산함(지연 평가)
        - 미리 모든 데이터를 메모리에 적재하지 않음. 반복하거나 처리할 때 필요한 만큼의 데이터만 메모리에 유지함
        - 데이터 처리를 간결하게 하고, LINQ와 함께 사용하여 데이터 조작 및 쿼리를 편리하게 수행 할 수 있음
    - 이터레이터
        - 특정 메서드가 속성을 사용하여 컬렉션의 요소를 순차적으로 반환하는 기능
        - 컬렉션의 데이터를 반복적으로 처리하거나 순회할 때 유용함
        - 미리 로드하지 않고도 필요한 데이터를 필요할 때 마다 로드하여 메모리를 효율적으로 사용 가능
- LINQ로 시퀀스를 반환하는 메서드를 작성해야 한다면 컬렉션을 반환하기보다는 이터레이터를 반환하는것이 작업을 좀 더 수월하게 수행할 수 있다.
- 이터레이터를 반환하면 필요할 때 생성 전략을 사용할 수 있다.
이터레이터 메서드를 시퀀스를 생성하는 방법을 알고 있는 객체를 생성한다.
그리고 이 객체는 실제 시퀀스에 대한 접근이 이루어지는 경우에만 사용된다.
- IEnumerable<T> 와 같은 인터페이스를 반환하도록 메서드를 작성하는 편이 좋다.
필요할 때마다 하나씩 항목을 생성할 수 있고, ToList()나 ToArray() 를 이용하여 저장된 컬렉션을 생성할 수도 있다.

### 아이템 30: 루프보다 쿼리 구문이 낫다.

- 쿼리 구문을 사용하는 명령형 방식에서 선언적인 방식으로 전환할 수 있다.
질의의 내용을 구성할 수 있고, 개별 항목의 실행 시기를 연기할 수 있다.
- 사용자의 의도를 더 명확하게 드러낼 수 있는 부가적인 장점도 있다.
    
    ```csharp
    private static IEnumerable<Tuple<int, int>> ProduceIndices2()
    {
    	for (var x = 0; x < 100; x++)
    		for (var y = 0; y < 100; y++)
    			if (x + y < 100)
    				yield return Tuple.Create(x, y);
    }
    
    private static IEnumerable<Tuple<int, int>> QueryIndices2()
    {
    	return from x in Enumerable.Range(0, 100)
    					from y in Enumerable.Range(0, 100)
    					where x + y < 100
    					select Tuple.Create(x, y);
    }
    
    private static IEnumerable<Tuple<int, int>> MethodIndices3()
    {
    	return Enumerable.Range(0, 100).
    			SelectMany(x => Enumerable.RAnge(0, 100),
    			(x, y) => Tuple.Create(x, y)).
    			Where(pt => pt.Item1 + pt.Item2 < 100).
    			OrderByDecending(pt => pt.Item1 * pt.ITem1 + pt.Item2 * pt.Item2);
    }
    ```
    
- 성능 개선이 필요하면 LINQ의 병렬 확장을 사용하는것도 고려할 수 있다. (AsParallel())

### 아이템 31: 시퀀스에 사용할 수 있는 조합 가능한 API를 작성하라

- 시퀀스를 다루는 메서드는 C# 이터레이터를 활용하여 작성할 수 있으며 결과가 필요한 시점에 맞춰 메서드가 수행되도록 할 수 있다.
 yield return 문을 사용하면 시퀀스를 반환하는 메서드를 쉽게 만들 수 있기도 하다.
- 이터레이터 메서드는 단윌 시퀀스(IEnumerable<T>로 표현되는)를 입력으로 취하고 그 결과로도 단일의 시퀀스(다른 IEnumerable<T>)를 반환하는 메서드를 말한다.
- 전체 시퀀스를 한 번만 순회하면서 조합된 메서드 세트를 수행할 수 있어 런타임의 효율이 개선된다.
이터레이터 메서드를 사용하도록 라이브러리를 개선하면 다수의 CPU 코러에 서로 다른 작업을 할당할 수 있으므로 전체적인 성능이 더욱 개선된다.
- 한 번의 순회 과정을 통해 여러 작업이 단번에 수행될 수 있다.
    
    ```csharp
    public static IEnumerable<T> Unique<T>(IEnumerable<T> sequence)
    {
    	var uniqueVals = new HashSet<int>()
    	foreach (var num in nums)
    	{
    		if (!uniqueVals.Contains(num))
    		{
    			uniqueVals.Add(num);
    			yield return num;
    		}
    	}
    }
    
    public static IEnumerable<int> Square(IEnumerable<int> nums)
    {
    	foreach (Var num in nums)
    		tield return num * num;
    }
    
    foreach (var num in Square(Unique(nums))
    	WriteLine("Number returned from Unique: {0}", num);
    ```
    

### 아이템 32: Action, Predicate, Function과 순회 방식을 분리하라

- 용어
    - Predicate - 시퀀스 내의 항목이 조건에 부합하는지를 boolean 로 반환함
    - action 델리게이트 - 컬렉션 내 개별 요소에 대해 실제 수행할 작업을 전달함
- function(predicate 포함) 을 이용하면 시퀀스의 개별 요소에 대해 적용할 다양한 작업을 구현할 수 있다.
action 을 이용하면 시퀀스의 일부 요소를 가져와서 수행할 작업을 손쉽게 구현할 수 있다.
    
    ```csharp
    public static IEnumerable<T> Select<T>(IEnumerable<T> sequence, Func<T, T> method)
    {
    	foreach (T element in sequence)
    		yield return method(element);
    }
    
    foreach (int i in Select(myInts, value => value * value))
    	WriteLine(i);
    
    //
    public static IEnumerable<T out> Select<T in, T out>(IEnumerable<T in> sequence, Func<T in, T out> method)
    {
    	foreach (T element in sequence)
    		yield return method(element);
    }
    
    foreach (string s in Select(MyInts, value => value.ToString()))
    	WriteLine(s);
    ```
    

### 아이템 33: 필요한 시점에 필요한 요소를 생성하라

- 시퀀스를 사용하는 측에서 값을 요청하는 시점에 맞춰 필요한 값을 생성하는 것이 좋다.
- 요청시 전체를 모두 생성하여 반환하는 방법 보다는, 필요한 일부만 사용할 수 있도록 하는 것이 효율적이다.

### 아이템 34: 함수를 매개변수로 사용하여 결합도를 낮추라

- 함수를 매개변수로 사용하면 컴포넌트를 사용하는 측과 컴포넌트의 구현하는 측의 코드를 분리할 수 있다.
- 인터페이스를 정의하거나 베이스 클래스를 만들어야 하는 경우라면 함수를 매개변수로 취하는 제네릭 메서드를 구현하는 것이 대안이 될 수 있을지 검토해야 한다.
- 추가 작업이 들어가고, 코드의 명확성이 떨어진다. 따라서 코드를 분리해서 사용자들이 얻을 수 있는 잠재적 이득과 복잡도 증가라는 단점 사이에서 적절한 균형을 가져야 한다.

```csharp
public static IEnumerable<TResult> Zip<T1, T2, TResult>(
		IEnumerable<T1> first, IEnumerable<T2> second, Func<T1, T2, TResult> zipper)
{
	using (var firstSequence = fist.GetEnumerator())
	{
		using (var secondSequence = second.GetEnumerator())
		{
			while(firstSequence.MoveNext() && secondSequence.MoveNext())
			{
				yield return zipper(firstSequence.Current, secondSequence.Current);
			}
		}
	}
}

var result = zip(fist, second, (one, two) => string.Format("{0}, {1}", one, two));
```

### 아이템 35: 확장 메서드는 절대 오버로드하지 마라

- 확장 메서드로 타입을 확장하는 용도의 사용은 적절하지 않다.
    - 애매 모호한 참조 때문에 컴파일 오류가 발생하게 된다.
- 특정 타입에 대해 동작하는 추가적인 메서드가 필요하다면, 오버로드가 아닌 이름을 변경하라.

### 아이템 36: 쿼리 효편식과 메서드 호출 구문이 어떻게 대응되는지 이해하라

- LINQ 는 쿼리 언어와 그 쿼리 언어를 일련의 메서드 집합으로 변환하는 2가지 핵심 구조를 기반으로 한다.
- 모든 쿼리 표현식은 하나 혹은 여러 개의 메서드로 매핑된다.
    
    ```csharp
    var numbers = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
    
    var smallNumbers = from n in numbers
    										where n < 5
    										select n;
    
    var smallNumbers = numbers.Where(n => n < 5);
    ```
    
    1. where 는 필터 역할을 수행한다.
    2. select 입력 시퀀스와 출력 시퀀스가 다른 경우 생략될 수 있다.
- !?…

### 아이템 37: 쿼리를 사용할 때는 즉시 평가보다 지연 평가가 낫다

- 용어
    - 지연 평가(lazy evaluation)
        - 데이터가 실제로 필요할 때까지 계산이 미루어지는 접근 방식
        - 불필요한 연산을 피하고 메모리를 절약할 수 있다.
    - 즉시 평가(eager evaluation)
        - 데이터 처리나 연산이 발생하는 즉시 해당 데이터나 결과를 계산하는 방식
        - 데이터를 미리 계산하여 나중에 사용할 수 있다.
- 쿼리 정의는 데이터나 시퀀스를 즉각적으로 얻어오는것이 아닌, 어떤 과정으로 작업을 수행할지에 대한 절차를 정의하는것이다.
일반적으로 이러한 방식이 더 효과적이며 실제로 쿼리의 결과를 이용하여 순회를 수행해야만 결과가 생성된다.
- 시퀀스로 부터 값을 즉각적으로 평가하여 그 결과를 얻고 싶다면 ToList(), ToArray() 메서드를 사용하면 된다.

### 아이템 38: 메서드보다 람다 표현식이 낫다

- 쿼리를 작성할 때 람다 표현식을 본문으로 하는 조그만 빌딩 블록을 조합하여 완전한 쿼리를 작성할 수 있다.
각각의 빌딩 블록은 IEnumerable<T> 와 IQueryable<T> 를 활용하면 장점을 극대화 할 수 있다. 또한, 이 방식을 이용하면 표현식 트리를 이용하여 쿼리를 수행하는 LINQ to SQL 에서도 올바르게 동작하는 재사용 가능한 빌딩 블록을 작성할 수 있다.

### 아이템 39: function과 action 내에서는 예외가 발생하지 않도록 하라

- 일련의 값을 순차적으로 처리하는 코드가 중간 어디쯤에서 예외를 일으키면 상태를 복구할 수 없는 문제에 봉착한다.
이는 시퀀스 내에 포함된 요소의 값을 직접 수정하기 때문에 발생한다.
- 시퀀스 내의 개별 요소들을 수정하기 이전에 오류 가능성을 사전 테스트 해볼 수 있다.
- 원본 시퀀스의 복사본을 마련해두고 복사본에 대해 알고리즘을 적용한 후 정상적으로 완료된 경우에만 원본 시퀀스를 대체하는 방법도 있다.

### 아이템 40: 지연 수행과 즉시 수행을 구분하라

- 선언적 코드는 해설적이며 무슨 작업을 해야 하는지를 정의한다.
명령형 코드는 어떻게 작업을 수행해야 하는지를 단계별로 세분화하여 기술한다.

```csharp
// DoStuff 의 매개변수를 생성하기 위해 Method1, Method2, Method3을 호출한다.
// 계산된 매개변수를 이용하여 DoStuff 를 호출한다.
var answer = DoStuff(Method1(), Method2(), Method3());

// Method1, Method2, Method3 을 매개변수로 DoStuff 를 호출한다.
// DoStuff 내에서 Method1, Method2, Method3 의 수행결과가 필요하다면 각 필요한 함수를 호출한다.
// Method1, Method2, Method3 은 어떤 순서로도 수행될 수 있으며 필요에 따라 여러번 수행되거나 수행되지 않을 수 있다.
var answer = DoStuff(() => Method1(), () => Method2(), () => Method3());

// 위 두가지를 혼합하여 사용할 수 있다
// 메서드의 효율을 높이기 위해서 캐시를 사용
var cache = Method1();
var answer = DoStuff(() => cache, () => Method2(), () => Method3());
```

- 지연 수행과 즉시 수행 중 어떤 방식이 좋을지 결정하는 핵심 기준을 실제로 메서드가 무슨 작업을 하는가에 달려 있다.
- 입력 데이터가 그리 크지 않은 경우라면 데이터를 전달하는 것이 더 좋을 수 있다.
하지만 입력이나 출력 데이터가 매우 커서 전체 데이터를 저장하기가 어려운 경우라면 알고리즘 자체를 매개변수로 취하는 형태가 훨씬 낫다.

### 아이템 41: 값비싼 리소스를 캡처하지 말라

- 클로저는 갭처된 변수를 포함하는 객체를 생성한다.
- 만약 클로저 내에 IDisposable을 구현하고 있는 객체가 있다면 문제가 발생할 수 있다.
- 너무 무거운 객체가 포함되는 경우에도 성능상의 문제가 발생할 수 있다.
- 클로저에 의해 생성된 객체를 메서드가 반환하는 경우 클로저를 수행하기 위해 캡처됐던 모든 변수들이 그 안에 포함된다.
꼭 필요한 변수, 변수를 제대로 정리했는지 확인해야 한다.

### 아이템 42: IEnumerable<T> 데이터 소스와 IQueryable<T> 데이터 소스를 구분하라

- IEnumerable<T>
    - LINQ to Objects
    - Enumerable<T> 확장 메서드는 쿼리식 내의 람다 표현식과 함수 매개변수를 나타내기 위해 델리게이트를 사용한다.
    - Enumerable<T> 내의 모든 메서드는 로컬 머신에서 수행된다.
- IQueryable<T>
    - LINQ to SQL
    - Queryable<T> 표현식 트리를 분석한 후 분석된 로직을 provider에 적합한 형태로 변경한 다음 이를 실제 데이터가 있는 위치의 컴퓨터에서 수행한다.
- AsQueryable() 을 사용하면 IEnumerable<T> 를 IQeruyable<T> 로 변경할 수 있다.

### 아이템 43: 쿼리 결과의 의미를 명확히 강제하고, Single()과 First()를 사용하라

- Single() 은 정확히 1개의 요소만 반환한다.
SingleOrDefault() 는 1개의 요소만 반환하거나, 요소가 없으면 기본값을 반환한다.
- First()는 첫번째 요소만 반환한다.
FirstOrDefault() 는 첫번째 요소만 반환하거나, 첫번째 요소가 없으면 기본값을 반환한다.
- Single은 정확히 하나라는 의미이다. First 는 첫번째, Last 는 마지막 요소라는 의미이다.
- 특정 요소를 찾기 위해 그 외 다른 메서드를 사용해야 한다면 쿼리가 명확하지 않거나 혹은 다른 방법이 있는지 찾아보는 것이 좋다.

### 아이템 44: 바인딩된 변수는 수정하지 말라

- 클로저에서 캡처된 변수를 수정하였을 경우 람다 표현식의 참조 변수도 같이 수정되는 현상이 발생한다.
- 람다 표현식 내부에서 객체 인스턴스의 멤버 변수에 접근하는 코드가 있다면 컴파일러는 해당 변수를 사용하는 인스턴스 메서드를 생성한다.
하지만, 람다 표현식 내에 지역변수를 사용하는 코드가 포함되었거나, 메서드의 매개변수를 사용하는 코드가 포함되었을 경우 중첩 클래스를 생성한다.
- 참조된 변수를 외부에서 변경하거나, 람다 표현식 내에서 변경하게 되면 양쪽 모두 반영이 되도록 설계 되어 있다.
- 여러 개의 쿼리를 연이어 수행할 때 바인딩된 변수의 값을 수정하게 되면 지연 수행과 클로저 구현 특성 때문에 예상치 않은 문제가 발생할 수 있다.
- 클로저에 의해 캡처되어 바인딩된 변수는 수정하지 않는 것이 좋다.