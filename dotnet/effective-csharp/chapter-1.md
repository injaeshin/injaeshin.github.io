# 이펙티브 c#

## 챕터 1 - C# 언어 요소

### 아이템 1 - 지역변수를 선언할 때는 var 를 사용하는 것이 낫다.

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

### 아이템 2 - const 보다는 readonly 가 좋다.

- 컴파일 상수(const) 와 런타임 상수(readonly) 를 구분하여 사용해야 합니다.
- 두 상수의 차이는
    1. 컴파일 상수는 컴파일 시점에 정해진 값으로 정의된다. 정의에 따라 정적 상수이므로 모든 인스턴스가 동일한 값을 가지게 된다.
    2. 런타임 상수는 상수의 값이 런타임에 평가된다. readonly 변수에 대한 참조 코드를 생성한다.
    멤버 초기화 구문이나 생성자를 통해 초기화가 가능하다.
- readonly 대신 const를 사용했을 때 이점은 성능이 빠르지만, 성능 개선 효과가 크지 않고, 유연성이 떨어진다. 변경되지 않는 값을 사용할 때를 제외하면 const 보다는 readonly 를 사용하는 것이 좋습니다.
