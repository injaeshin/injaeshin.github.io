# BaaaaaaaarkingDog - 1

- [https://blog.encrypted.gg/category/강좌/실전 알고리즘?page=2](https://blog.encrypted.gg/category/%EA%B0%95%EC%A2%8C/%EC%8B%A4%EC%A0%84%20%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98?page=2)
- https://github.com/encrypted-def/basic-algo-lecture/blob/master/workbook.md


### 기본 (문자열)

- 8958 - OX 퀴즈
    - 연속되는 같은 문자에 따라 점수가 증가함
    연속이 끊기면 점수 초기화 (OO : 1 + 2, OXO, 1+1)
    
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    
    int main()
    {
        ios::sync_with_stdio(0);
        cin.tie(0);
        
        int n = 0;
        cin >> n;
        
        string s;
        for (int i = 0; i < n; i++) {
            cin >> s;
            
            int sum = 0;
            int score = 0;
            for(const char c : s) {
                if (c == 'O') {
                    score++;
                    sum += score;
                } else {
                    score = 0;
                }
            }
            
            cout << sum << "\n";
        }
        
        return 0;
    }
    ```
    
- 2920 - 음계
    - 입력받는 숫자가 내림차순, 오름차순, 무작위 인가를 찾는 문제
    현재 숫자와 다음 숫자의 계산이 무엇인가에 따라서 내림차순, 오름차순, 무작위인가를 판별함
    
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    
    int main()
    {
        ios::sync_with_stdio(0);
        cin.tie(0);
        
        int arr[8]{};
        for (int i = 0; i < 8; i++) {
            cin >> arr[i];
        }
        
        int ans = 0;
        for (int i = 0; i < 7; i++) {
            int a = arr[i] - arr[i+1];
            if (a == -1) {
                ans = 0;
            }
            else if (a == 1) {
                ans = 1;
            }
            else {
                ans = 2;
                break;
            }
        }
        
        if (ans == 0)
            cout << "ascending";
        else if (ans == 1)
            cout << "descending";
        else
            cout << "mixed";
        
        return 0;
    }
    ```
    
- 2953 - 나는 요리사다
    - 총 5명이 받은 점수 (최고 5점) 를 합산하여 1등과 최고 점수를 찾는 문제
    
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    
    int main()
    {
        ios::sync_with_stdio(0);
        cin.tie(0);
        
        int score[5]{};
    
        int mx = 0;
        int idx = 0;
        int a, b, c, d;
        for(int i = 0; i < 5; i++) {
            cin >> a >> b >> c >> d;
            score[i] = a + b + c + d;
            
            if (score[i] > mx) {
                mx = score[i];
                idx = i;
            }        
        }
        
        cout << idx + 1 << " " << mx;
        
        return 0;
    }
    ```
    
- 10809 - 알파벳 찾기

### 0x03 배열

- 10808 - 알파벳 개수
    - 문제 - 알파벳 고문자로만 이루어진 단어 S가 주어진다. 각 알파벳이 단어에 몇 개가 포함되어 있는지 구하는 프로그램을 작성하시오.
    - 조건 - 단어의 길이는 100 아래. 알파벳은 소문자만 있음
    - 풀이 - 
    1. 알파벳은 26개 글자 ( a - z ).
    2. 26개의 배열을 만듬.
    3. ascii code 의 a는 65, 제시하는 알파벳에서 65를 빼면 배열에 맞는 숫자가 나옴
    ( a - 65 = 0 = arr[0]), 배열의 값을 1씩 증가.
    - 코드
    
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    
    int main()
    {
        ios::sync_with_stdio(0);
        cin.tie(0);
        
        int ans[26] = {0, };
        std::string s;
        cin >> s;
        for (char c : s) {
            ans[c - 'a']++;
        }
        
        for (int i = 0; i < 26; i++) {
            cout << ans[i] << " ";
        }
        
        return 0;
    }
    ```
    
- 2577 - 숫자의 개수
    
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    
    int main()
    {
        ios::sync_with_stdio(0);
        cin.tie(0);
        
        int num[3]  = {0, };
        int ans[10] = {0, };
        
        for (int i = 0; i < 3; i++) {
            cin >> num[i];
        }
        
        for (char c : to_string(num[0] * num[1] * num[2])) {
            ans[c - '0']++;
        }
        
        for (int i = 0; i < 10; i++) {
            cout << ans[i] << "\n";
        }
        
        return 0;
    }
    ```
    
- 1475 - 방 번호
    - 풀이 -
    6은 9로 9는 6으로 대체할 수 있다.
    (arr[6] + arr[9] + 1) / 2 = 6 or 9의 수를 확인 가능함
    
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    
    int main()
    {
        ios::sync_with_stdio(0);
        cin.tie(0);
        
        int ans[10] = {0, };
        
        int n = 0;
        cin >> n;
        
        while (n > 0) {
            ans[n % 10]++;
            n /= 10;
        }
        
        int ret = 0;
        for (int i = 0; i < 10; i++) {
            if (i == 6 || i == 9) {
                continue;
            }
            
            ret = max(ret, ans[i]);
        }
        
        ret = max(ret, (ans[6] + ans[9] + 1) / 2);
        cout << ret;
        
        return 0;
    }
    ```
    
- 3273 - 두 수의 합
    - 투 포인터(two pointer) 알고리즘을 사용
    
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    
    int main()
    {
        ios::sync_with_stdio(0);
        cin.tie(0);
    
        int arr[100004] = {0, };
        
        int n;
        cin >> n;
        for (int i = 0; i < n; i++) {
            cin >> arr[i];
        }
        
        int x;
        cin >> x;
        
        sort(arr, arr+n);
        
        int ans = 0;
        int s = 0;
        int e = n - 1;
        while(s < e)
        {
            if (arr[s] + arr[e] == x) {
                ans++;
                s++;
                e--;
            }
            else if (arr[s] + arr[e] > x)
                e--;
            else
                s++;
        }
        
        cout << ans;
        
        return 0;
    }
    ```
    
- 10807 - 개수 세기
    - N은 1 - 100. 
    V는 -100 ~ 100. V의 입력 수 찾기
    - n만큼의 배열 100 + 100 으로 생성
    v 를 배열 인덱스로 만들어 카운드 증가
    단, v가 -100 까지 가능하다고 하니, v + 100 으로 하여 증가하여 인덱스 계산함
    ex. v : -100 + 100 = 0, v : -5 + 100 = 95, v : 10 + 100 = 110
    찾으려는 v에 +100 을 더하여 인덱스를 계산 후 카운트 출력함
    
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    
    int main()
    {
        ios::sync_with_stdio(0);
        cin.tie(0);
    
        int arr[204] = {0, };
        
        int n;
        cin >> n;
        
        int m;
        for (int i = 0; i < n; i++) {
            cin >> m;
            arr[m+100]++;
        }
        
        int v;
        cin >> v;
        cout << arr[v+100];
        
        return 0;
    }
    ```
    
- 13300 - 방 배정
    - 풀이
        - 학년, 성별이 입력되면 arr[학년][성별] + 1 로 더함
        - arr[학년][성별] 을 m 으로 나누고 값을 더함
        arr[학년][성별] 을 m 으로 나눈 나머지가 있다면 + 1을 함
    
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    
    int main()
    {
        ios::sync_with_stdio(0);
        cin.tie(0);
        
        int arr[8][2]{};
        
        int n = 0, m = 0;
        cin >> n >> m;
        
        int a = 0;
        int s = 0, y = 0;
        for (int i = 0; i < n; i++) {
            cin >> s >> y;
            arr[y][s]++;
        }
        
        int ans = 0;
        for (int i = 1; i <= 6; i++) {
            ans += arr[i][0] / m;
            ans += (arr[i][0] % m) ? 1 : 0;
    
            ans += arr[i][1] / m;
            ans += (arr[i][1] % m) ? 1 : 0;
        }
        
        cout << ans;
        
        return 0;
    }
    ```
    
- 11328 - Strfry
    - 풀이
        - strfry 는 입력받은 문자열을 무작위로 재 배열한다.
        - 첫번째 문자열이 재 배열되면 두번째 문자열이 나올 수 있는지 판단함
        가능하면 Possible, 불가능하면 Impossible
        - 문자열의 최대 길이는 1000 이다.
        문자는 영어, 소문자로 이루어졌다.
        - 배열[26] 생성 후 첫전째 문자열의 문자 수 만큼 배열에 +1
        두번째 문자열의 문자 수 만큼 -1
        배열의 값이 0 이면 가능, 0이 아니라면 불가능
        - 테스트 케이스를 확인할 때마다 배열 초기화
    - 코드
    
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    
    int main()
    {
        ios::sync_with_stdio(0);
        cin.tie(0);
        
        int arr[26]{};
        
        int n = 0;
        cin >> n;
        
        string a, b;
        for (int i = 0; i < n; i++) {
            cin >> a >> b;
            
            fill(arr, arr+26, 0);
            
            for(char c : a) {
                arr[c - 'a']++;
            }
            
            for(char c : b) {
                arr[c - 'a']--;
            }
            
            bool succ = true;
            for (int j = 0; j < 26; j++) {
                if (arr[j] != 0) {
                    succ = false;
                }
            }
            
            cout << ((succ) ? "Possible" : "Impossible") << "\n";
        }
    
        return 0;
    }
    ```
    
- 1919 - 애너그램 만들기
    - 영어 단어의 순서를 뒤바꾸어 같아질 수 있을 때 제거해야 하는 문자의 수를 찾는 문제
    aabbcc xxyybb 가 있다면, aaccxxyy를 제거하면 bb만 남아 있음 이때 정답은 8
    - 풀이
        - [실패] ~~arr[26] 선언, 문자의 인덱스 카운트 증가
        가장 높은 인덱스 확인 후 그 외 나머지 인덱스의 수를 반환~~
        - 첫번째 단어만큼 더하고, 두번째 단어만큼 차감함
        0이 아닌 모든 수를 절대값으로 더함
        
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;
        
        int main()
        {
            ios::sync_with_stdio(0);
            cin.tie(0);
            
            int arr[26]{};
            
            string a, b;
            cin >> a >> b;
            
            for (char c1 : a) {
                arr[c1 - 'a']++;
            }
            
            for (char c2 : b) {
                arr[c2 - 'a']--;
            }
            
            int ans = 0;
            for (int i = 0; i < 26; i++) {
                if (arr[i] != 0)
                    ans += abs(arr[i]);
            }
            
            cout << ans;
        
            return 0;
        }
        ```
        

### 0x04 연결 리스트

- 1406 - 에디터
    
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    
    int main()
    {
        ios::sync_with_stdio(0);
        cin.tie(0);
        
        /*
        L 커서를 왼쪽으로 이동함
        D 오른쪽으로 이동함
        B 왼쪽에 있는 문자를 삭제함
        P 문자를 왼쪽에 추가함
        
        1. 길이가 N인 초기 문자열 입력함 (max 100,000)
        2. 입력 명령어 개수 m (max 500,000)
        
        */
        
        list<char> lt;
        
        string s;
        cin >> s;
        for (char c : s)
            lt.push_back(c);
        
        auto it = lt.end();
        
        int n = 0;
        cin >> n;
        
        while(n--) {
            char cmd;
            cin >> cmd;
            if (cmd == 'L') {
                if (it != lt.begin())
                    it--;
            } else if (cmd == 'D') {
                if (it != lt.end())
                    it++;
            } else if (cmd == 'B') {
                if (it != lt.begin())
                    it = lt.erase(--it);
            } else if (cmd == 'P') {
                char c;
                cin >> c;
                lt.insert(it, c);
            }
        }
        
        for (char c : lt)
            cout << c;
    
        return 0;
    }
    ```
    
- 5397 - 키로거
    - 풀이 - 문자열 L이 주어진다. (1 ~ 1,000,000)
    백스페이스를 입력했다면 - 앞의 글자가 있다면 그 글자를 지운다.
    <, > 는 움직일 수 있다면 커서를 1 이동한다.
    
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    
    int main()
    {
        ios::sync_with_stdio(0);
        cin.tie(0);
    
        int n = 0;
        cin >> n;
    
        for (int i = 0; i < n; i++) {
            list<char> lt;
            auto p = lt.begin();
            
            string s;
            cin >> s;
            for(auto c : s) {
                if (c == '<') {
                    if (p != lt.begin())
                        p--;
                } else if (c == '>') {
                    if (p != lt.end())
                        p++;
                } else if (c == '-') {
                    if (p != lt.begin()) {
                    	p--;
                        p = lt.erase(p);
                    }
                } else {
                    lt.insert(p, c);
                }
            }
            
            for(auto c : lt) {
                cout << c;
            }
            cout << '\n';
        }
        
        return 0;
    }
    ```
    
- 1158 - 요세푸스 문제
    - 1부터 N까지 리스트가 있음. K번째 숫자를 제거함. 이후 +K 번째 숫자를 제거함. 리스트의 숫자를 모두 제거한 하면 제거된 순서대로 출력
    
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    
    int main()
    {
        ios::sync_with_stdio(0);
        cin.tie(0);
    
        int n = 0, k = 0;
        cin >> n >> k;
        
        queue<int> q;
        for (int i = 1; i <= n; i++) {
            q.push(i);
        }
        
        cout << "<";
        while(!q.empty()) {
            for (int i = 0; i < k - 1; i++) {
                q.push(q.front());
                q.pop();
            }
            
            cout << q.front();
            q.pop();
            
            if (q.size())
                cout << ", ";
        }
        
        cout << ">";
    }
    ```