---

---
--- 
## [auto](https://en.cppreference.com/w/cpp/language/auto)


Ключевое слово auto в C++ используется для автоматического определения типа объекта на основе типа её инициализирующего выражения. 

Время ее жизни - local lifetime.

**Переменная, с ключевым словом auto должна быть определена в момент объявления.

1. локальные переменные

``` C++
auto x = 42; // x -> int
auto pi = 3.14; // pi -> double
auto str = "Hello, World!"; // str -> const char*	
```

2. функции их их возвращаемое значение

``` C++
auto functionName(parameters) -> returnType {
    // body
}
```

3.  функции и их шаблоны

``` C++
auto add(int x, int y) {
    return x + y;
}
// return-value of add is automatically int

template<typename T, typename U>
auto multiply(T x, U y) -> decltype(x * y) {
    return x * y;
}

```

---
## [Decltype](https://en.cppreference.com/w/cpp/language/auto)

decltype - это ключевое слово, которое проверяет тип объявленного объекта.

с помощью decltype можно узнавать тип объекта, без необходимости создавать экземпляр этого типа.

Это особенно полезно в шаблонном программировании и метапрограммировании, где типы могут быть сложными и не всегда очевидными.

Decltype предоставляет информацию о типе на этапе компиляции, в отличии от typeid, который предоставляет информацию в процессе run-time


1. 
``` C++
int x = 5;
decltype(x) y = 10; 
```

2. decltype также полезен, при работе с шаблонными функциями для определении типа возвращаемого значения, которое зависит от типов аргументов:

``` C++
template<typename T, typename U>
auto add(T t, U u) -> decltype(t + u) {
    return t + u;
}
```