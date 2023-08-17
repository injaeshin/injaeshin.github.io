# 알고리즘 문제풀이

- [https://blog.encrypted.gg/category/강좌/실전 알고리즘?page=2](https://blog.encrypted.gg/category/%EA%B0%95%EC%A2%8C/%EC%8B%A4%EC%A0%84%20%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98?page=2)
- https://github.com/encrypted-def/basic-algo-lecture/blob/master/workbook.md

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
    
    
- 3273 - 두 수의 합
    
    
- 10807 - 개수 세기
    
    
- 13300 - 방 배정
    
    
- 11328 - Strfry
    
    
- 1919 - 애너그램 만들기