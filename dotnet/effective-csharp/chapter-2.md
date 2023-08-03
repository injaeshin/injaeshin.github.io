# EC# - 챕터 2

## 챕터 2 .NET 리소스 관리

### 아이템 11: .NET 리소스 관리에 대한 이해

- .net 환경의 메모리 관리와 가비지 수집기의 동작 방식 이해
- 가비지 수집기(GC)는 관리되는 메모리를 관리하며 메모리 관리 문제를 자동화합니다.
그럼에도 개발자가 올바르게 해제 작업을 수행하면 더 효과적으로 수집합니다.
비관리 리소스는 GC가 관리하지 않고, 개발자가 직접 관리해야 합니다. 또한, 이벤트 핸들러, 델리게이트 등을 잘못 사용하면 불필요한 메모리가 오래 남아있는 경우가 발생합니다.
- GC는 자동으로 관리 힙 메모리를 관리하기 위해 마크/콤팩트(Mark/Compact) 알고리즘을 사용합니다. 응용 프로그램의 최상위 객체(root)가 개별 객체까지의 참조 여부를 확인하도록 설계되어 있습니다.(Reference Tracking) 참조를 확인하지 못한 객체는 가비지로 간주하여 수집합니다.
수집이 완료되면 남아 있는 객체들을 힙의 시작 지점을 기준으로 옮겨서 가용 메모리를 단일화 하여 큰 메모리 공간으로 만드는 작업을 합니다.
- 관리 힙이 아닌 비 관리 리소스는 개발자가 관리해야 합니다.
비 관리 리소스를 관리 할 수 있도록 finalizer와 IDisposable 인터페이스를 제공합니다.
finalizer(종료자) 는 객체가 소멸될 때 비 관리 리소스를 해제 할 수 있도록 반드시 실행 되지만, 실행 시점을 정확히 계산할 수 없는 문제가 있습니다.
- 코드 상으로 객체가 사용 완료되어, 미 사용되어도 GC가 실행되고, 객체가 수집되는 시점까지 finalizer가 실행되지 않습니다.
- 이로 인해 C++ 의 코딩 스타일을 C#에 사용하면 정상적으로 동작하지 않을 수 있습니다.
- .net 환경에서 비 관리 리소스를 해제하는 가장 좋은 방법은 IDisposable 인터페이스와 Dispose 패턴을 활용하는 것이다.

### 아이템 12: 할당 구문보다 멤버 초기화 구문이 좋다

- 클래스에서 멤버 변수의 값 초기화는 멤버 초기화 구문(member initializer)을 사용하는 것이 좋다.
    
    ```csharp
    public class MyClass
    {
    		// 선언하는 동기에 초기화
    		private List<string> labels = new List<string>();
    }
    ```
    
    1. 몇 개의 생성자를 추가하든 상관없이 올바르게 초기화 할 수 있다.
    2. 컴파일러는 모든 생성자의 시작 부분에 멤버 초기화 구문을 포함하기 때문에 멤버 초기화 구문이 항상 포함된다. 따라서, 생성자 본문에서 멤버 변수를 별도로 초기화 할 필요가 없다.
    3. 기본 생성자도 멤버 초기화 구문 사용시 시작 부분에 멤버 초기화 구문이 포함된다.
- 상속의 경우도, 베이스 클래스 생성자가 호출되기 전 멤버 변수 초기화가 이루어진다. - 멤버 변수의 초기화 순서는 선언 순서로 수행된다. -
- 하지만, 다음의 경우는 멤버 초기화 구문을 사용하지 않는 것이 좋다.
    - 객체를 0이나 null로 초기화 하는 경우 - 시스템 초기화 루틴에서 코드를 실행하기 전 모든 값을 0으로 설정한다. (메모리 블록을 0으로 설정함)
    - 동일한 객체를 반복하여 초기화 하는 경우 - 멤버 초기화와 생성자에서 초기화 되는 반복 초기화가 실행된다면, 2개의 객체가 생성되어 그 중 하나는 가비지가 된다.
        
        ```csharp
        // 중복 초기화 예시
        public class MyClass
        {
        		private List<string> labels = new List<string>();
        		MyClass() { }
        		MyClass(int size)
        		{
        			labels = new List<string>(size);
        		}
        }
        
        // 컴파일러가 생성하는 실제 동작 코드
        public class MyClass
        {
        		private List<string> labels;
        		MyClass()
        	  {
        			labels = new List<string>();
        	  }
        		MyClass(int size)
        		{
        			labels = new List<string>();
        			labels = new List<string>(size);
        		}
        }
        ```
        
    - 예외 처리가 반드시 필요한 경우 - 클래스 내부에서 복구를 시도할 수 없는 경우에는 생성자 내부에서 초기화 코드를 처리하고 예제 처리 코드를 구현해야 한다.

### 아이템 13: 정적 클래스 멤버를 올바르게 초기화하라

- 

### 아이템 14: 초기화 코드가 중복되는 것을 최소화하라

- 

### 아이템 15: 불필요한 객체를 만들지 말라

- 가비지 수집기(GC)는 자동으로 메모리 관리를 진행하지만 너무 많은 객체를 생산하면 심각한 성능 문제를 일으킬 수도 있다.
- 모든 참조 타입의 객체는 동적으로 메모리를 할당한다. (그것이 지역변수라도) 할당된 객체는 상위 객체가 삭제되면 가비지가 된다.
- 자주 호출되는 메서드 내에서 참조 타입의 객체를 매번 생성하는 경우라면 지역변수를 멤버 변수로 변경하는것이 좋다.
    
    ```csharp
    // MyFont 를 반복적으로 생성하는 문제
    protected override void OnPaint(PaintEventArgs e)
    {
    		using (Font MyFont = new Font("Arial", 10.0f))
    		{
    			e.Graphics.DrawString(DateTime.Now.ToString(),
    				MyFont, Brushes.Black, new PointF(0, 0));
    		}
    
    		base.OnPaint(e);
    }
    
    // MyFont 를 멤버 변수로 미리 생성하여 재사용
    {
    		e.Graphics.DrawString(DateTime.Now.ToString(),
    			myFont, Brushes.Black, new PointF(0, 0));
    
    		base.OnPaint(e);
    }
    ```
    
    1. Font 타입과 같이 IDisposable 인터페이스를 구현한 타입의 객체를 지역변수로 사용하면 이 클래스도 반드시 IDisposable을 구현해야 한다.
- 유사한 객체를 반복적으로 할당하는 것을 피하는 다른 방법으로, 정적 속성을 사용할 수 있다.
반복적으로 필요한 단일 객체가 호출되었을 때 생성하고, 동일한 요청이 있을 때 마다 이 객체를 돌려준다.
    
    ```csharp
    private static Brush blackBrush;
    public static Brush Black
    {
    		get
    		{
    			if (blackBrush == null)
    					blackBrush = new SolidBrush(Color.Black);
    			return blackBrush;
    		}
    }
    ```
    
    1. 경우에 따라서는 생성된 객체가 메모리상에 필요 이상으로 오랫동안 남아 있을 수 있다는 점이다. - 비 관리 리소스를 삭제할 수 없다는 문제도 있다. -
- 변경 불가능한(immutable) 타입의 반복 수정을 조심해야 한다. (String, DateTime, TimeSpan, Decimal, Uri …)
    
    ```csharp
    // msg 에 추가시마다 새로운 string 객체가 생성된다.
    string msg = "Hello, ";
    msg += thisUser.Name;
    msg += ". Today is ";
    msg += DateTime.Now.ToString();
    
    //
    string msg = string.Format("Hello, {0}. Today is {1}", thisUser.Name, DateTime.Now.ToString());
    string msg = string.Format($"Hello, {thisUser.Name}. Today is {DateTime.Now.ToString()}");
    
    //
    StringBuilder msg = new StringBuilder("Hello, ");
    msg.Append(thisUser.Name);
    msg.Append(". Today is ");
    msg.Append(DateTime.Now.ToString());
    
    string finalMsg = msg.ToString();
    ```
    
    1. string은 생성 후 변경 불가능한 타입이며, 추가, 수정마다 새로운 객체가 생성된다.
    2. 문자열 보간을 이용하거나, StringBuilder 를 이용하여 객체 생성을 최소화해야 한다.

### 아이템 16: 생성자 내에서는 절대로 가상 함수를 호출하지 말라

- 객체가 완전히 생성되기 전에 가상 함수를 호출하는 것은 예기치 않은 동작을 유발할 수 있다.
- 객체의 파생 클래스가 기반 클래스의 가상 함수를 오버라딩하여 다른 동작을 수행하는 경우에 특히 문제가 될 수 있다.

### 아이템 17: 표준 Dispose 패턴을 구현하라

- .NET Framework 내부에서 비관리 리소스를 정리하기 위한 표준 패턴으로 Dispose 패턴을 이용하고 있다. 이 패턴을 이용하면 IDisposable 인터페이스를 통해 리소스를 삭제할 수 있는 기능을 제공할 수 있다. 또한, 비관리 리소스를 정리하기 위한 다른 방법으로 finalizer를 사용할 수 있다.
    - Dispose - 비관리 리소스 정리를 위한 효과적인 패턴 (추천)
    - finalizer - 객체가 삭제되는 시점에서 자동 호출되는 함수
- Dispose 를 사용하기 위해서는 부모 클래스는
    - IDisposable 인터페이스를 구현해야 한다.
    - 비관리 리소스가 포함되어 있다면, finalizer (종료자) 를 추가해야 한다.
    - Dispose와 finalizer는 리소스 정리 작업을 위임 할 수 있어야 한다. 파생 클래스가 고유의 리소스 정리 작업이 필요한 경우 재정의할 수 있도록 하기 위함이다.
- 파생 클래스에서는
    - 고유의 리소스 정리 작업을 수행해야 한다면 부모 클래스에서 정의한 가상 메서드를 재정의한다.
    - 멤버 필드로 관리하는 비관리 리소스가 포함되어 있다면 finalizer를 추가한다.
    - 베이스 클래스에서 정의하고 있는 가상 함수를 반드시 재 호출해야 한다.
- 비관리 리소스를 포함하는 클래스에서는 Dispose 메서드가 항상 올바르게 호출된다는 보장을 할 수 없으므로, 반드시 finalizer 를 구현해야 한다.
- Dispose 에서 사용 완료된 객체를 해제하고 난 후에는 finalizer 에서 다시 해제하지 않도록 해야 한다. finalize와 Dispose() 메서드는 일반적으로 동일한 역할을 수행하므로 중복된 코드가 여러번 호출될 수 있음을 고려하자.

```csharp
public interface IDisposable
{
		void Dispose();
}
```

- IDisposable.Dispose() 메서드는
    - 모든 비관리 리소스를 정리한다.
    - 모든 관리 리소스를 정리한다.
    - 객체가 이미 정리되었음을 나타내기 위한 상태 플래그 설정이 필요하다. (재 요청시 ObjectDisposedExpcetion 예외를 발생시킨다.
    - finalizer 호출 회피를 위해 GC.SuppressFinalize(this) 를 호출한다.
- 이미 정의된 베이스 클래스를 상속하여 파생 클래스를 정의하는 경우, 파생 클래스의 Dispose 실행되면서 베이스 클래스의 Dispose 도 실행 될 수 있도록 호출하는 코드가 작성되어야 한다.

```csharp
using System;

namespace Sandbox
{
    public class MyResourceHog : IDisposable
    {
        private bool alreadyDisposed = false;

        public void Dispose()
        {
            Console.WriteLine("Dispose in base");
            Dispose(true);
            GC.SuppressFinalize(this);
        }

        protected virtual void Dispose(bool isDisposing)
        {
            Console.WriteLine("virtual Dispose in base");

            if (alreadyDisposed)
                return;

            if (isDisposing)
            {
                // 관리 리소스를 정리한다.
            }

            // 비관리 리소스를 정리한다.

            alreadyDisposed = true;
        }

        public void ExampleMethod()
        {
            if (alreadyDisposed)
                throw new ObjectDisposedException("MyResourceHog",
                        "Called Example MEthod on Disposed object");

            // 구현...
        }
    }

    public class DerivedResourceHog : MyResourceHog
    {
        private bool disposed = false;

        protected override void Dispose(bool isDisposing)
        {
            Console.WriteLine("Dispose in derived");

            if (disposed)
                return;

            if (isDisposing)
            {
                // 관리 리소스 정리
            }

            // 비관리 리소스 정리

            // 베이스 클래스가 자신의 리소스를 정리할 수 있도록 해주어야 한다.
            // 베이스 클래스는 GC.SuppressFinalize() 를 호출해야 한다.
            base.Dispose(isDisposing);

            disposed = true;
        }
    }

    public class Program
    {
        public static void Main()
        {
            using (var resource = new DerivedResourceHog())
            {
                Console.WriteLine("Hello");
            }

            return;
        }
    }
}
```

<aside>
💡 결과

Hello
Dispose in base
Dispose in derived
virtual Dispose in base

</aside>

1. using 의 구문이 종료되는 시점에 맞춰서 베이스 클래스의 Dispose 가 호출됨
2. 객체는 파생클래스 타입으로 생성되었으므로, virtual Dispose 함수는 override 된 함수로 호출됨
3. 파생 클래스의 dispose 함수에 base.dispose(isDisposing) 호출로 베이스 클래스 Dispose(bool)이 호출됨
4. GC.SuppressFinalize(this)을 실행하여 finalizer 호출을 회피하도록 한다.