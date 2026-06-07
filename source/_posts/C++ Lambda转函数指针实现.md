---
title: C++ Lambda转函数指针实现
date: 2026-06-06 23:15:01
categories:
    - CMake
tags: 
    - CMake
    - C++
---

C++ Lambda转函数指针实现

<!-- more -->

### 使用场景

C++ Lambda转函数指针，可以在不传递参数给函数指针的情况下，直接通过labmda捕获参数实现参数传递。

用来兼容不带void*上下文状态的接口，来实现参数传递。

### 代码实现

lambda2fp.h

```cpp
#include <type_traits>
#include <utility>

namespace detail {
    
template<typename Lambda, typename Ret, typename... Args>
Ret (*lambda2fp(Lambda&& lambda, Ret(*)(Args...)))(Args...) {
    using LambdaDecayed = std::decay_t<Lambda>;
    static LambdaDecayed static_lambda = std::forward<Lambda>(lambda);
    static Ret (*func_ptr)(Args...) = [](Args... args) -> Ret {
        if constexpr (std::is_void_v<Ret>) {
            static_lambda(args...);
        } else {
            return static_lambda(args...);
        }
    };
    return func_ptr;
}

}

#define WRAP_LAMBDA(lambda, signature) \
    detail::lambda2fp(lambda, (signature)nullptr)
```

### 使用示例

将lambda函数转换为不带返回的函数指针

```cpp
auto callback = WRAP_LAMBDA([invoker](){ invoker->print(); }, void(*)());
```

### 注意事项

由于其内部是通过静态变量实现，通过一个类的多次示例化，以第一次调用为准

如多个类调用callback打印类内的一个变量值，只会打印第一次调用类的变量值