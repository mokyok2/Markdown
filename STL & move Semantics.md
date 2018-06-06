# STL과 move 연산

_이 글을 이해하기 위해서는 rvalue reference(우측값 참조)에 대한 이해가 선행되어야 합니다._

## 우측값 참조

C++의 STL은 move 연산을 지원한다.  
move 연산이란 대입 연산시에 값을 복사하는게 아니라 직접 옮겨버리는 것을 의미하는데,  
대입할 값을 대입 연산 이후에 더 이상 사용하지 않을 때,  
대입할 값과 대입될 값의 포인터만 서로 바꿔준다면, 복잡한 복사 연산 없이 같은 기능을 수행할 수 있다는 장점이 있다.  

사실 이러한 기능 자체만을 구현하기 원한다면, 우측값 참조라는 개념이 반드시 필요하지는 않을 수 있다.  
하지만, 기존의 좌측값 참조로 대입하던 방식의 코드에 오버로딩을 통해서 구현하기 위해서는  
인자를 구분할 수 있는 방법이 필요했다.

그래서 C++ 개발자들이 만든 개념이 우측값 참조이다.

## std::move

std::move() 함수는 인자로 들어오는 값을 우측값 참조로 타입을 변환해주는 기능이 전부다.  
따라서 우측값 참조로 오버로딩된 함수를 사용하고 싶다면 기존 좌측값에 std::move를 사용해주면 된다.

___std::move 함수가 move 연산을 수행하는 것이 아니다!___

다음 예시를 보자.

```C++
#include <iostream>
#include <vector>


class vec_cls
{
private:
    std::vector<int> m_vec;

public:
    void init()
    {
        m_vec.clear();
        m_vec.emplace_back(0);
    }

    void set(std::vector<int>& p_vec)
    {
        m_vec = p_vec;      // operator= (T& arg)
    }

    void set(std::vector<int>&& p_vec)
    {
        m_vec = std::move(p_vec);       // operator= (T&& arg)
    }
};
```

위의 set 함수는 std::vector의 operator= 함수를 래핑한 것이다.  
중요한 것은 operator= 함수가 set 함수처럼 오버로딩 되어있다는 것이다.

보다시피 우측값 참조로 오버로딩 되어있는 함수를 호출하기 위해서는  
std::move를 사용하여 우측값 참조로 타입변환을 해주어야 한다.

```C++
int main()
{
    vec_cls cls;

    std::vector<int> vec;
    vec.emplace_back(1);
    vec.emplace_back(2);
    vec.emplace_back(3);

    // ------------------------

    cls.init();
    cls.set(vec);

    std::cout << vec.size() << std::endl; // size : 3

    // ------------------------

    cls.init();
    cls.set(std::move(vec));

    std::cout << vec.size() << std::endl; // size : 0

    // ------------------------


    return 0;
}
```

사실 우측값 참조로 오버로딩한 함수 안에서 move 연산을 어떻게 구현할지는 개인의 자유다.  
STL에서는 인자로 들어온 값이 가지고 있는 포인터들을 대입될 값의 포인터로 복사하고,  
인자로 들어온 값은 쓰레기값의 어떤 포인터로 새로 초기화 해버린다.

위의 코드에서 move 연산 이후 vec.size()가 0이 된 이유가 바로 이 때문이다.