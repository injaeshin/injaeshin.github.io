# BaaaaaaaarkingDog

- [https://blog.encrypted.gg/category/강좌/실전 알고리즘?page=2](https://blog.encrypted.gg/category/%EA%B0%95%EC%A2%8C/%EC%8B%A4%EC%A0%84%20%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98?page=2)
- https://github.com/encrypted-def/basic-algo-lecture/blob/master/workbook.md


    

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
    
    
- 2493 - 탑
- 6198 - 옥상 정원 꾸미기
- 17298 - 오큰수
- 3015 - 오아시스 재결합
- 6549 - 히스토그램에서 가장 큰 직사각