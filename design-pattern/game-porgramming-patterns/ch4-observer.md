# CH4 관찰자

## Part II 디자인 패턴 다시 보기

### CHAPTER 4 관찰자

- 객체 사이에 일 대  다의 의존 관계를 정해두어, 어떤 객체의 상태가 변할 때 그 객체의 의존성을 가진 다른 객체들이 그 변화를 통지 받고 자동으로 업데이트될 수 있게 만듭니다.
- 무엇이 되었든, 객체의 상태가 변경되었을 때 변경을 다른 객체들에게 알려주는 구조
- 관찰자 / 공급자
    - 관찰자
        - 공급자의 상태 변화를 감지하고 처리하는 객체
        - 공급자의 상태 변화에 대한 알림을 받고 적절한 작업을 수행하는 역할을 한다
    - 공급자 (대상 Subject)
        - 상태 변화가 발생하면 관찰자들(!)에게 상태 변화를 알리는 역할
        - 상태를 알리기 위해 관찰자 목록을 가지고 있다
    
    ```cpp
    // 관찰자
    class Observer {
    public:
     virtual ~Observer() {}
     virtual void onNotify(const Entity& ent, Event evt) = 0;
    }
    
    class Achievements : public Observer {
    public:
     virtual void onNotify(const Entity& ent, Event evt) {
       // 알림이 왔음
     }
    }
    
    // 공급자 (대상)
    class Subject {
    private:
     Observer* observers_[MAX_OBSERVERS];
     int numObservers_;
    public:
     void AddObserver(Observer* ob) {
       // 배열에 추가한다.
     }
     void removeObserver(Observer* ob) {
       // 배열에서 제거한다.
     }
    protected:
     virtual void notify(const Entity& ent, Event evt) {
         for(int i = 0; i < numObservers_; i++)
           observers_[i]->onNotify(entity, event);
     }
    };
    
    // 적용
    class Physics : public Subject {
    public:
      void updateEntity(Entity& entity);
    };
    ```
    
- 이런 방식의 관찰자 패턴은 동기적이라는 문제가 있음. 관찰자가 하나라도 느리면 대상이 블록될 수 있다.
- 관찰자는 멀티스레드, 락과 함께 사용할 때는 정말 조심해야 한다.
- 이벤트 큐를 이용해 비동기적으로 상호작용하는게 더 좋을 수도 있다.
- 대상이 죽었을 때 관찰자에게 사망 알림을 보내는 방법.
관찰자를 제거할때는 소멸자에서 대상의 removeObserver() 만 호출하면 된다.
- 그 외 - Entity 객체를 대상으로 할 경우