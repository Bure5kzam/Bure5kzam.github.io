---
layout: single
title: c++ std::placeholders
date: 2023-09-15 19:08
category:
author: Bure5kzam
tags: []
summary:
---
placeholder 객체는 바인딩 시 함수 객체를 생성할 때 인자 자리에 전달하는 객체이다.

std::bind 표현식에서 사용될 때, placeholder 객체는 생성된 함수 객체에 저장된다.

이후 함수 객체가 바운딩 되지않은 인자들과 호출될 때 각 placeholder 객체들은 대응되는 N번째 인자들로 대체된다.

placeholder 객체는 DefaultConstructible 또는 CopyConstructible 유형이며, 이들의 복사/이동 생성자는 예외를 발생시키지 않는다.

```c++
#include <iostream>
#include <string>

void goodbye(const std::string& s)
{
    std::cout << "Goodbye " << s << '\n';
}

class Object
{
public:
    void hello(const std::string& s)
    {
        std::cout << "Hello " << s << '\n';
    }
};

int main()
{
    using namespace std::placeholders;
    // main 내에서 placeholders 네임스페이스 사용 선언

    using ExampleFunction = std::function<void(const std::string&)>;
    // 인자로 string&을 받고 반환값이 없는 자료형 선언

    Object instance;
    std::string str("World");

    ExampleFunction f = std::bind(&Object::hello, &instance, _1);
    f(str);
    // Example 1 :  멤버 함수 객체를 instance에 바인딩.
    // 멤버 함수 hello의 인자 string&을 대신할 placeholder 객체를 넘겨줌
    // f는 instance.hello(str)와 동일하게 됨.

    f = std::bind(&goodbye, std::placeholders::_1);
    f(str);
    // Example 2 : 함수 객체를 바인딩
    // 마찬가지로 placeholdre 객체 _1.
    // equivalent to goodbye(str)

    auto lambda = [](std::string pre, char o, int rep, std::string post)
    {
        std::cout << pre;
        while (rep-- > 0)
            std::cout << o;
        std::cout << post << '\n';
    };

    // binding the lambda:
    std::function<void(std::string, char, int, std::string)> g =
        std::bind(&decltype(lambda)::operator(), &lambda, _1, _2, _3, _4);
    g("G", 'o', 'o'-'g', "gol");
    // Example 3 : lambda 객체를 바인딩
}
```
