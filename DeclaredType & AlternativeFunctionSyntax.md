# Declared Type

C++ 11에서는 auto라는 새로운 키워드가 추가되었다.  
auto는 값에 알맞는 타입으로 알아서 적용되는 키워드이다.

이와 비슷한 decltype이라는 키워드도 추가되었는데,  
이는 declared type의 약자로, 값을 이용해 그 타입을 알아내는 역할을 한다.

다음 예제를 보자.

```C++
#include <iostream>

int main()
{
    std::cout << sizeof(decltype(1)) << std::endl;      // 4 (int)
    std::cout << sizeof(decltype(1.5)) << std::endl;    // 8 (double)
    std::cout << sizeof(decltype('c')) << std::endl;    // 1 (char)

    return 0;
}
```

# Alternative Function Syntax

## C++ 11

decltype 키워드는 위와 같이 타입의 사이즈를 알아내는 데에도 사용 할 수 있지만,  
C++ 11에서 새롭게 추가된 함수 문법을 사용하면 더욱 다채롭게 활용할 수 있다.

아래 예제는, 함수를 정의하는 과정에서 auto와 함께 사용되는 모습이다.

```C++
#include <iostream>
#include <string>

using namespace std;


template <typename U, typename V>
auto add_11(U u, V v) -> decltype(u + v)
{
    return u + v;
}


int main()
{
    cout << add_11(1, 2) << endl;       // 3
    cout << add_11(1, 2.5) << endl;     // 3.5
    cout << add_11(string("Hello,"), string(" World!")) << endl;    // Hello, World!

    return 0;
}
```

기존 함수의 리턴타입이 있을 자리에 auto라는 키워드를 사용하면서,  
뒤에 ->decltype(*sth*) 형식을 붙여주면, 컴파일 타임에 *sth*의 자료형을
리턴타입으로 사용하게 된다.  
이와 같은 방법은 람다함수에 많이 사용된다.

## C++ 14

```C++
template <typename U, typename V>
auto add_14(U u, V v)
{
    return u + v;
}
```
이러한 함수 정의 방법은 C++ 14로 오면서 더욱 발전하게 되는데,  
->decltype(*sth*)을 생략해도 동작하도록 변경되었다.
