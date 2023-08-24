# CH5 프로토타입

## Part II 디자인 패턴 다시 보기

### CHAPTER 5 프로토타입

- 특정 객체와 동일한 객체를 복사한다.
베이스 클래스에 Clone() 가상 함수를 추가하여 이를 상속받는 하위 클래스에서 Clone 을  구현하여 복사 로직을 추가하도록 한다.
- 몬스터를 생성하는 스포너를 예를 들면…
    
    ```cpp
    class Monster {
        // 기타 등등...
    }
    
    class Ghost : public Monster {};
    class Demon : public Monster {};
    class Sorcerer : public Monster {};
    
    class Spawner {
    public:
        virtual ~Spawner() {}
        virtual Monster* spawnMonster() = 0;
    }
    
    class GhostSpawner : public Spawner {
    public:
        virtual Monster* spawnMonster() {
            return new Ghost();
        }
    };
    
    class DemonSpawner : public Spawner {
    public:
        virtual Monster* spawnMonster() {
            return new Demon();
        }
    };
    ```
    
    - 이런 코드가 존재할 수 있다. 몬스터 추가마다 무언가 새로운 클래스들이 계속 추가된다.
- 위 코드를 프로토타입 패턴으로 처리하면
    
    ```cpp
    class Monster {
    public:
        virtual ~Monster() {}
        virtual Monster* clone() = 0;
    };
    
    class Ghost : public Monster {
    public:
        Ghost(int health, int speed) : health_(health), speed_(speed) { }
        virtual Monster* clone() { return new Ghost(health_, speed_); }
    private:
        int health_;
        int speed_;
    };
    
    class Spawner {
    public:
        Spawner(Monster* prototype) : prototype_(prototype) { }
        Monster* spawnMonster() {
            return prototype_->clone();
        }
    private:
        Monster* prototype_;
    };
    
    // doing
    Monster* ghostPrototype = new Ghost(15, 3);
    Spawner* ghostSpawner = new Spawner(ghostPrototype);
    ```
    
    - 객체 뿐만 아니라, 상태도 같이 복사 가능하다.
- 요즘은 개체 종류별로 클래스를 만들기보다는 컨포넌트나 타입객체로 모델링하는 것을 선호한다.
- 스폰 함수