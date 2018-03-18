# 가변인자 템플릿 (Variadic Template)

## 가변인자 템플릿이란

가변인자 템플릿이란, 이름 그대로 인자의 타입과 갯수가 가변적인 템플릿을 말한다.  
과거 C++의 경우에는 템플릿을 선언할 때 인자의 수를 반드시 명시해야 했었기 때문에,  
가변인자 기능을 제공하기 위해서는 각각의 인자 수에 맞는 모든 템플릿을 정의해야만 했다.

그러나 C++ 11이 나오면서 언어 자체적으로 가변인자 템플릿을 지원하게 되었다.

```C++
#include <iostream>
using namespace std;

int f(int a) { return a + 1; }

template <typename ... ARGS>
void goo(ARGS ... args)
{
    cout << "goo" << endl;
}

void hoo(int a, int b, int c)
{
    cout << "hoo " << a << b << c << endl;
}
```

typename ... 으로 선언된 ARGS는 Parameter Pack이라 부르며, 여러개의 인자 집합을 의미한다.  
여기서 주의할 점은 인자들의 타입을 선언하는 경우에는 typename ... ARGS 의 순서지만,  
함수에서 사용하는 경우에는 ARGS ... args의 순서라는 것이다.

```C++
template <typename ... ARGS>
void foo(ARGS ... args)
{
    // 1. sizeof...() 키워드
    cout << sizeof...(ARGS) << endl;
    cout << sizeof...(args) << endl;

    // 2. parameter pack을 다른 함수로 보내는 코드 (...이 이름 뒤에 따라 붙음)
    goo(args...);

    // 3. pack expansion - parameter pack에 있는 내용을 풀어서 전달
    hoo(args...);
    hoo(f(args)...); //< hoo(f(1), f(2), f(3))
}


int main(int argc, char* argv[])
{
    foo(1, 2, 3);
    return 0;
}
```

sizeof... 라는 키워드는 타입 인자의 갯수. 즉, Parameter Pack 내부의 객체 수를 의미한다.

## Parameter Pack 내부 값 사용하기

가변인자 템플릿의 모든 인자가 가변인자인 경우에는 인자를 꺼낼 수 없다.  
따라서 가변인자인 Parameter Pack 내부의 값에 접근하고 싶다면, 다음과 같은 형태로 함수를 구성해야 한다.

```C++
#include <iostream>
using namespace std;
 
template <typename T, typename ... ARGS>
void Goo (T a, ARGS ... args)
{
    static int n = 0;
    ++n;

    cout << n << ": " << typeid(T).name() << " : " << a << endl;

    // 꺼낼 때는 'recursive'한 방법으로 꺼내야 한다.
    // recursive한 방식으로 동작하므로 작은 코드에 대해서만 사용해야한다.
    Goo(args...);
}

// 재귀의 종료를 위해 인자없는 Goo 함수가 필요하다.
void Goo ()
{
    cout << "Goo 종료" << endl;
}

int main(int argc, char* argv[])
{
    Goo(1, 3.4, "aa", 5);
    return 0;
}
```

이처럼 재귀적으로 함수를 호출하다보면, 마지막에 인자가 없는 함수가 호출되므로  
이때 사용될 함수도 오버로딩 해주어야 한다.