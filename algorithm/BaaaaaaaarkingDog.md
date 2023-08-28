# [BaaaaaaaarkingDog](https://blog.encrypted.gg/)

- [https://blog.encrypted.gg/category/강좌/실전 알고리즘?page=2](https://blog.encrypted.gg/category/%EA%B0%95%EC%A2%8C/%EC%8B%A4%EC%A0%84%20%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98?page=2)
- https://github.com/encrypted-def/basic-algo-lecture/blob/master/workbook.md

[문자열 / 배열 / 연결 리스트](https://www.notion.so/d169152c50ab4195ac2f8f2759da5654?pvs=21)

### 0x05 스택

- 10828 - 스택
    
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    
    int main()
    {
        ios::sync_with_stdio(0);
        cin.tie(0);
    
        stack<int> stk;
        
        int n = 0;
        cin >> n;
        
        while(n--) {
            string s;
            cin >> s;
            
    
            if (s == "push") {
                int v;
                cin >> v;
                stk.push(v);
            }
            else if (s == "top") {
                if (stk.size() == 0)
                    cout << "-1" << "\n";
                else
                    cout << stk.top() << "\n";
            }
            else if (s == "size") {
                cout << stk.size() << "\n";
            }
            else if (s == "empty") {
                cout << ((stk.empty()) ? "1" : "0") << "\n";
            }
            else if (s == "pop") {
                if (stk.size() == 0) {
                    cout << "-1" << "\n";
                } 
                else {
                    cout << stk.top() << "\n";
                    stk.pop();
                }
            }
        }
        
        return 0;
    }
    ```
    
- 10774 - 제로
    - K : 1 ~ 100,000
    0이 아닌 경우 입력값을 저장함
    0인 경우 가장 최근 저장한 수를 삭제함
    전체 수의 합을 출력한다.
    
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    
    int main()
    {
        ios::sync_with_stdio(0);
        cin.tie(0);
        
        int K = 0;
        cin >> K;
        
        stack<int> stk;
        
        for (int i = 0; i < K; i++) {
            int n = 0;    
            cin >> n;
            
            if (n == 0) {
                stk.pop();
            } else {
                stk.push(n);
            }
        }
        
        int ans = 0;
        while (!stk.empty()) {
            ans += stk.top();
            stk.pop();
        }
    
        cout << ans;
        
        return 0;
    }
    ```
    
- 1874 - 스택 수열
    1. 지문을 이해했는가? - 설명 가능한가?
        1. 이해를 못해서 도움 영상 시청함 ( https://www.youtube.com/watch?v=byCxMbgzEVM )
        2. 예시 1번은 해결했는데, 예시 2번인 NO 상황을 이해 못함
        3. 이해를 못해서 정답을 확인함
    2. 문제를 해결하기 위해서 무엇을 해야 하는가?
        1. n 만큼 반복하여 입력 받음
            1. v 의 입력보다 작거나 같도록 스택에 cnt++ 를 추가함 - “+”
            2. cnt 가 v와 다르다면 “NO” 출력 후 종료
            3. 현재 스택의 top 은 v 와 같음. pop - “-”
        2. “NO” 가 아니라면 “+”, “-” 를 차례로 출력함
    - 코드
        
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;
        
        int main() {
          ios::sync_with_stdio(0);
          cin.tie(0);
        
          int n = 0;
          cin >> n;
        
          int cnt = 1;
          stack<int> stk;
          string ans;
          
          while(n--) {
            int v = 0;
            cin >> v;
        
            while(cnt <= v) {
                stk.push(cnt++);
                ans += "+\n";
            }
            
            if (stk.top() != v) {
                cout << "NO";
                return 0;
            }
            
            stk.pop();
            ans += "-\n";
          }
          
          cout << ans;
          return 0;
        }
        ```
        
    1. 무엇을 배웠는가?
        1. 문제의 의미도 이해를 못하고 설명을 찾아보면서 30% 이해했다.
        ”NO”가 나와야 하는 조건의 코드를 작성하지 못하고 정답을 찾아보고,
        코드 작성자가 대단하다고 생각함
        2. 코드는 이해했지만, 어떤 문제인지 잘 모르겠음..

- 2493 - 탑
    - 지문을 이해했는가? - 설명이 가능한가?
        - 입력받은 숫자보다 큰 숫자의 인덱스는 무엇인가?
    - 문제를 해결하기 위해서 무엇을 해야 하는가?
        - 스택의 처음 첫 데이터는 비교 대상이 없으므로, 0으로 출력해야 함
        max + 값으로 스택에 미리 저장함 (인덱스는 0)
        - 입력받은 숫자와 인덱스를 같이 스택에 보관하는 방법이 필요함 (pair)
        - 숫자 하나씩 입력 받으면서 스택에 저장되어 있는 정수를 검사하면서 계산해야 함
            - 숫자 입력 받음 : v
            - 스택 top 의 숫자는 v보다 작은가 - pop for while
            현재 스택의 숫자 인덱스 출력.
    - 무엇을 배웠는가?
        - pair  로 인한 인덱스를 미리 넣는 방법을 배웠음… 응용을 못하고 있었음…
        - 최초 스택의 데이터가 없을때를 꼬려하여 empty 조건만 생각했는데, 그리고 앞의 데이터가 없을때는 고려하여 0은 언제 나와야 하는가를 고민했었음.
        미리 최대 수치의 0을 넣어주면 모든 고민이 끝난다는 것을 배웠음. 절망…
    - 코드
        
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;
        
        int main() {
          ios::sync_with_stdio(0);
          cin.tie(0);
        
          int n = 0;
          cin >> n;
          
          stack<pair<int, int>> s;
          
          s.push({0, 100000004});
          for (int i = 1; i <= n; i++) {
            int v = 0;
            cin >> v;
            
            while (s.top().second < v) {
              s.pop();
            }
            
            cout << s.top().first << " ";
            s.push({i, v});
          }
          
          return 0;
        }
        ```
        
- 6198 - 옥상 정원 꾸미기
    
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    
    int main() {
        ios::sync_with_stdio(0);
        cin.tie(0);
        
        int n = 0;
        cin >> n;
        
        stack<int> s;
        long long a = 0;
        while(n--) {
            int h = 0;
            cin >> h;        
            while (!s.empty() && s.top() <= h) {
                s.pop();
            }
            
            a += s.size();
            s.push(h);
        }
        
        cout << a;    
        return 0;
    }
    ```
    
- 17298 - 오큰수
    - 문제를 이해 하였는가?
        - A[i] 의 오른쪽에 있으면서 A[i]보다 큰 수를 찾는 문제. A[i]보다 큰 수가 없다면 -1.
    - 
    
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    
    int main(){
      ios::sync_with_stdio(0);
      cin.tie(0);
      
      int c[1000004] {};
      int a[1000004] {};
      stack<int> s;
      
      int n = 0;
      cin >> n;
      for (int i = 0; i < n; i++) {
        cin >> c[i];
      }
      
      for (int i = n - 1; i >= 0; i--) {
        while (!s.empty() && s.top() <= c[i]) {
          s.pop();
        }
        
        a[i] = (s.empty()) ? -1 : s.top();
        s.push(c[i]);
      }
      
      for( int i = 0; i < n; i++) {
        cout << a[i] << " ";
      }
      
      return 0;
    }
    ```
    
- 3015 - 오아시스 재결합
- 6549 - 히스토그램에서 가장 큰 직사각