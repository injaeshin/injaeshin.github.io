# EC# - 챕터 5

## **챕터 5 예외 처리**

### **아이템 45: 메서드가 실패했음을 알리기 위해서 예외를 이용하라**

- 메서드가 요청 작업을 제대로 수행하지 못한다면, 예외를 발생시켜 실패하였음을 알리는 것이 좋다. 오류 코드를 이용하면 사용자가 쉽게 무시 할 수 있고, 오류 코드를 활용하여 정상 흐름을 방해하면서 핵심 논리를 흐리게 할 수 있다.
- 예외를 사용하면 문제 상태를 확인할 수 있는 정보들을 많이 가지고 있고, 오류가 발생했음을 무시한다면 데이터가 손상된 상태로 진행되는 문제를 방지 할 수 있다.
- 하지만, 모든 오류 상황을 예외로 다룰 필요는 없다. 함수의 기능을 최소화 하여 목적에 맞도록 처리하면 반환값으로 상황을 전달 할 수 있고, 오류 발생 확인을 위한 정보 확인이 더 명확해진다.
    
    ```csharp
    // 파일이 존재 하는지 확인. 존재하지 않더라도 함수는 수행을 완료한 것.
    // 어떤 결과가 나와도 프로그램의 흐름을 이어 갈 수 있음
    File.Exists()
    
    // 파일이 존재하고, 파일을 읽을 수 있고, 프로세스가 권한이 있는지 확인 후 성공
    // 실패하면 프로그램의 흐름을 이어갈 수 없음
    File.Open()
    ```
    
- 예외 발생 메서드를 작성할때는 예외가 발생할 수 있는 문제들의 사전 조건 검사 메서드를 함께 작성하고, 미리 실행시키는 패턴을 이용하면 예외 발생으로 인한 성능 저하 문제를 피할 수 있음.
- 오류가 발생하면 항상 예외를 발생시키도록 코드를 작성하는것이 좋다. 하지만, 일반적인 흐름 제어 매커니즘으로 예외를 사용해서는 안된다.

### **아이템 46: 리소스 정리를 위해 using과 try/finally를 활용하라**

- 관리되지 않는 리소스를 정리하기 위해서는 using 구문 , try/finally 블록을 사용하는 방법이 있다. 그래야 Dispose() 메서드를 호출하게 된다. 혹시나 Dispose() 메서드 호출을 잊어버린 경우를 대비해서라도 finalizer 를 방어적으로 작성해야 한다. 이 경우에는 프로그램 수행 성능에 좋은 방향은 아니다.
    
    ```csharp
    //
    var myConnection = new SqlConnection(connString);
    var mySqlCommand = new SQlCommand(commandString, myConnection);
    myConnection.Open();
    mySqlCommand.ExecuteNonQuery();
    
    mySqlCommand.Dispose();
    myConnection.Dispose();
    
    // 
    using (SqlConnection myConnection = new SqlConnection(connString))
    {
    	using (SqlCommand mySqlCommand = new SqlCommand(commandString, myConnection)
    	{
    		myConnection.Open();
    		mySqlCommand.ExecuteNonQuery();
    	}
    }
    ```
    
    - 첫번째 예제 코드는 정상적으로 동작할 경우 문제가 없이 실행된다. 하지만, 객체 생성 후 Dispose() 호출 전에 오류가 발생한다면, 정상적으로 정리를 하지 못하는 문제가 있다.
    - 두번째 예제 코드는 문제가 발생하더라도, 해당 블록을 빠져나갈때 리소스를 정리하려고 한다.
- C# 컴파일러는 using 문을 발견하면 해당 객체를 감싸는 try/finally 블록을 자동으로 생성해준다.
단, IDisposable 인터페이스를 지원하지 않는 타입에 대해서는 using 문을 사용할 수 없다.
이 경우 as 를 이용하면 타입이 IDisposable 를 실제 구현하였는지와 상관없이 더 안전하게 코드를 작성할 수 있다.
    
    ```csharp
    //
    object obj = Factory.CreateResource();
    using (obj as IDisposable)
    	Console.WriteLine(obj.ToString());
    
    //
    var conn = new SqlConnection(connString);
    var cmd = new SqlCommand(cmdString, conn);
    
    using (conn as IDisposable)
    using (cmd as IDisposable)
    {
    	conn.Open();
    	cmd.ExecuteNonQuery();
    }
    ```
    
    - 첫번째 예제 코드는 obj 객체가 IDisposable 을 상속받아 구현했다면, 블록을 나갈때 Dispose() 를 자동 실행하도록 한다. 상속받지 않았다면 Dispose() 를 실행하지 않는다.
    - 하지만, 두번째 코드처럼 작성하게 된다면 cmd 생성자를 호출하는 동안 예외가 발생하게 된다면 Dispose 를 호출하지 못한다.
- IDisposable 인터페이스를 구현한 객체를 사용할 경우 using 문을 이용하면 어떤 상황에서도 해당 객체가 할당한 리소스를 정상적으로 해제할 수 있다.
Dispose()를 호출해야 하는 객체가 2개 이상인 경우 using 블록을 중복해서 쓰거나 자체적으로 try/finally 블록을 구현하는 것이 좋다.
- 간혹 Dispose()와 Close() 2개의 메서드를 제공하는 경우가 있다면 Dispose()를 호출하는 것이 좋다. Close()는 연결을 닫고 해제하는 기능을 포함하고 있지만, Dispose는 리소스 해제하는 작업 외에도 가비지 수집기에게 finalizer를 호출할 필요가 없다는 코드를 추가하는게 일반적이기 때문이다.

### **아이템 47: 사용자 지정 예외 클래스를 완벽하게 작성하라**

- 모든 오류를 예외로 표현할 필요는 없다. 하지만 즉각적으로 처리되거나 보고되지 않았을 때 문제가 심각한 경우에는 예외를 발생시키는 것을 선호한다.
ex. 데이터베이스 내에 데이터 정합성 문제가 발생한 경우.
- 서로 다른 예외 클래스를 활용하여 예외를 발생시키는 유일한 이유는 코드 작성 개발자가 각각을 구분하여 서로 다른 작업을 수행할 수 있도록 해주기 위함이다. 복구 가능성을 염두에 두고 추가적인 정보를 담도록 예외 클래스를 작성하는 것이 좋다.
- 다른 작업이나 복구 메커니즘으로 이어질 가능성이 있는 오류가 발생한 경우라면 새로운 예외 클래스를 만들자.
    - 예외 객체를 생성할때는 오류 상황을 세부적으로 나타내는 고유한 정보를 포함시키는 것이 좋다. 앞서 발생한 예외가 있다면 InnerException 속성에 저장하는 것이 좋다. 기존 예외에 더하여 추가적인 정보를 제공해줄 수 있다.
    
    ```csharp
    try
    {
    	// 외부 라이브러리에서 예외 발생하는 경우
    	return ThirdPartyLibrary.ImportantRoutine();
    }
    catch (ThirdPartyException e)
    {
    	var msg = $"Problem with {e.ToString()} using library";
    	throw new DoingSomeWorkException(msg, e);
    }
    ```
    

### **아이템 48: 강력한 예외 보증을 준수하는 것이 좋다**

- 응용 프로그램 전체를 하나로 보는것이 아니라, 각 메소드별로 어떤 예외 보증을 기준으로 삼고 처리하는 방식을 구현하는 것이 좋다.
- 예외 보증이이란,
    - 기본 보증(basic guarantee): 특정 함수 내에서 발생한 예외가 이 함수를 빠져나오더라도 어떤 리소스도 누수되지 않으며, 모든 객체의 상태가 유효한 상태를 유지함을 의미한다.
    .NET CLR은 기본 보증을 준수한다. 관리 환경은 메모리 전반을 관리하므로 IDisposable을 구현한 리소스를 소유한 상태에서 예외를 유발하는 경우를 제외한다면 리소스가 유출될 가능성이 없다.
    - 강력한 보증(strong guarantee): 기본 보증 + 예외 발생 시에도 프로그램의 상태가 변경되지 않음을 추가로 보증하는 것을 의미한다.
    프로그램이 사용하는 데이터 요소는 변경 불가능한 값 타입에 저장하는 것이 좋다.
    LINQ 는 기본적으로 강력한 예외 보증 요건을 준수하므로 LINQ 쿼리를 사용하는것도 도움이 된다.
    - 예외 없음 보증(no-throw guarantee): 특정 메서드가 예외를 전혀 발생시키지 않으며, 항상 완벽하게 수행될 뿐 아니라, 예외가 이 메서드 밖으로 전파되지 않을 것을 보장한다.
    finalizer 와 Dispose 메서드와 예외 필터인 when 내에서는 절대로 예외를 발생시켜서는 안된다.
    finalizer 에서 예외가 발생하면 리소스를 온전히 정리하지 못한 채 프로그램이 종료된다. Dispose 에서 예외가 발생하면 기존 예외를 무시하고 새로운 예외를 발생시킨다.
    예외 필터인 when 절 내에서 예외가 발생하면 기존 예외를 무시하고 새로운 예외를 발생시킨다.
    delegate의 대상이 되는 메서드 안에서도 예외를 발생시켜서는 안된다. 멀티 캐스트 델리게이트의 경우 대상이 되는 메서드 중 하나가 예외를 일으키면 다른 대상 메서드가 호출되지 않는 문제가 있다.

### **아이템 49 + 50: catch 후 예외를 다시 발생시키는 것보다 예외 필터가 낫다 + 예외 필터의 다른 활용 예를 살펴보라**

- catch 문 내에서 예외를 발생하는 것보다 특정 상황을 고려하여 예외 필터를 사용하는 경우가 좋을 수 있다.
- 예외 필터는 catch 문 이후 when 키워드를 이용하여 구성하게 되는데 catch 문에 지정한 예외 타입에 대해서만 필터가 수행된다. 
예외 필터를 추가하면 예외 처리가 반드시 필요한 경우에 한해서만 catch 문을 수행하도록 코드를 구조화 할 수 있으며, 오류에 대한 세부 정보를 읽지 않고 그대로 지킬 수 있다.
- .NET CLR은 코드상에 try/catch 문이 있더라도 catch 문 내부로 진입할 가능성이 없는 경우 최적화를 수행하여 성능을 개선한다.
    
    ```csharp
    public static void TreeOfErrors()
            {
                var retryCount = 0;
                while (true)
                {
                    try
                    {
                        MakeException();
                    }
                    catch (Exception e) when (LogException(e))
                    { }
                    catch (TimeoutException) when (retryCount++ < 5) &&
                                              (!System.Diagnostics.Debugger.IsAttached))
                    {
                        Console.WriteLine("Timeout error: trying again");
    
                        Task.Delay(1000 * retryCount).Wait();
                    }
                }
            }
    
            private static void MakeException()
            {
                throw new TimeoutException();
            }
    
            private static bool LogException(Exception e)
            {
                var oldColor = Console.ForegroundColor;
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine(e.ToString());
                Console.ForegroundColor = oldColor;
    
                return false;
            }
    ```
    
    - 예외 필터를 잘 활용하면 예외가 발생했을 때 어떤 일이 벌어지고 있는지를 자세히 살펴볼 수 있다. 어느 부분에서 문제가 발생 했는지를 활인할 수만 있다면 디버깅은 훨씬 수월해진다.