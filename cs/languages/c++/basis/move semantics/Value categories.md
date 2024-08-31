Категории выражений (value categories) — это классификация выражений, которая определяет, как эти выражения могут быть использованы и какие операции с ними могут быть выполнены.

Термин value в данном контексте не означает конкретное значение, а скорее описывает общую природу и поведение выражений.

Каждое выражение в языке C++ характеризуется двумя параметрами:
- типом выражения (type)
	- Тип выражения определяет множество возможных значений, которые может принимать выражение, а также операции, которые могут быть выполнены с этим выражением.
- категорией выражения (value category)
	- Категория значения определяет семантику выражения


Любое выражение определённо принадлежит к одной из трёх основных категорий, которые являются подмножествами более общий категорий `gvalue` и `rvalue`.

![Value categories](pics/Diagram.svg)



![lvalues-rvalues](pics/lvalues-rvalues.png)

---
### prvalue (pure rvalue)

`prvalue` - это категория выражения, вычисление которого:
- определяет временное значение, которое не является адресуемым
- инициализирует объект
- является перемещаемым, т.к. представляет временное значение
- определяет значение операнда встроенного оператора
- не имеет идентификатора

Примерами `prvalue` являются:

- Литералы (за исключением строковых)
``` cpp
42; // prvalue
true; // prvalue
nullptr; // prvalue
```


- вызовы функций, возвращающие нессылочные типы
``` cpp
int foo();
foo(); // prvalue

int a{}, b {} // both lvalues
a + b; \\ prvalue

a++, a && b, a % b, a == b, !a, a>= b; 
// all logical and arithmetical expressions are prvalue
```


- адреса любых `lvalue` выражений всегда являются `prvalue`
``` cpp
int a{}; // lvalue
&a; // prvalue
```


- Анонимные функции, независимо от их захвата, являются `prvalue`.
``` cpp
auto lambda = [](int x) -> int { return x + 1; };
// [](int x) -> int { return x + 1; }; is prvalue
``` 


- Аргументы функций, которые неявно преобразуются
``` cpp
void printString(const std::string& str) {
    std::cout << str << std::endl;
}

printString("Hello, World!"); 
// "Hello, World!" is prvalue
```


- Выражение `requires`
``` cpp
requires (T i) { typename T::type; }; // prvalue
```

- Специализация концепта
``` cpp
std::equality_compatible<int>; // prvalue
```
---
### lvalue (locator value)

`lvalue` является подмножеством `gvalue` и представляет категорию выражений, которые определяют объекты, который может быть адресуемым.

``As a consequence, in ANSI-C, the meaning of the 'l' changed to locator value. An lvalue is now an object that has a specified location in the program (so that you can take the address, for example). In the same way, an rvalue can now be considered just a readable value.``

`lvalue` не является перемещаемым напрямую, но может быть преобразован с помощью `std::move()`.


Примерами `lvalue` являются:

- любые идентификаторы объектов, шаблонные параметры
``` cpp
int a; // 'a' is lvalue
int arr[10] // arr[10] is lvalue
int a[4]{} // also lvalue
int* p; // *p is lvalue

struct S { int x; }
S my_struct; // my_struct.x is lvalue

int& func(); // func() is a lvalue
```


- строковые литералы
``` cpp
"Meow!" // lvalue
```


- использование префиксных операторов
``` cpp
++a; // lvalue
--a; // also lvalue
```


- любые ссылки на функции являются `lvalue`
``` cpp
void myFunction(int x) {
    std::cout << "x = " << x << std::endl;
}

void(&funcRef)(int) = myFunction; 
// funcRef is an lvalue

auto& ar = std::move(myFunction); 
// OK: 'ar' is lvalue of type void(&)(int)
```
---

### xvalue (eXpiring value)

`xvalue` является подможеством как `rvalue`, так и `gvalue` и определяет категорию выражений, которые представляют объекты, ресурсы которых могут быть перемещены из-за их близости к концу жизни.

- от `xvalue` выражения нельзя взять адрес
- время жизни `xvalue` ограничено областью действия выражения

Примерами `xvalue` являются:

- результат вызова функции, которая возвращает `rvalue reference` относится к категории `xvalue`
``` cpp
int&& foo();
foo(); // xvalue

bool b { true }; // lvalue
std::move(b); // xvalue

static_cast<bool&&>(b); // xvalue
```


- обращение к любому нестатическому полю, относящемуся к `rvalue`, является `xvalue`. (member of object expression)
``` cpp
struct foo {
	int a;
}

foo f; // lvalue
std::move(f).a; // xvalue
foo{}.a; // xvalue
```

---
## Reference collapsing

Свёртка ссылок (reference collapsing) - это механизм, который упрощает и унифицирует работу с ссылками при использовании шаблонов.

Правила свёртки:

- `T&`  `&` $\rightsquigarrow$ `T&`
- `T&`  `&&` $\rightsquigarrow$ `T&`
- `T&&`  `&` $\rightsquigarrow$ `T&`
- `T&&`  `&&` $\rightsquigarrow$ `T&&`


Эти правила можно сформулировать следующим образом: 

- если хотя бы одна из ссылок является lvalue-ссылкой (`T&`), то результатом будет lvalue-ссылка.

- Если обе ссылки являются rvalue-ссылками (`T&&`), результатом будет rvalue-ссылка.

``` cpp
typedef int&  lref;
typedef int&& rref;
int n;
 
lref&  r1 = n; // type of r1 is int&
lref&& r2 = n; // type of r2 is int&
rref&  r3 = n; // type of r3 is int&
rref&& r4 = 1; // type of r4 is int&&
```

``` cpp
template<typename T>
void bar(T&& arg) {
    baz(std::forward<T>(arg));
}

void baz(int&) {
    std::cout << "lvalue\n";
}

void baz(int&&) {
    std::cout << "rvalue\n";
}

int main() {
    int x = 42;
    bar(x); // T = int&, arg = int&
    bar(42); // T = int, arg = int&&
}
```