Семантика перемещения - это механизм передачи ресурса от одного объекта к другому, путём передачи владения, без дополнительных затрат на копирование.


Семантика перемещения основывается на двух ключевых концепциях:

1. Move Constructor
	- определяет создание объекта путём передачи владения ресурсом
		- передаёт владение ресурсом новому объекту, оставляя прошлого владельца в состоянии, пригодном для уничтожения.


2. Move assignment operator
	- определяет передачу владения ресурсом от одного объекта другому.


---
### moving fundamental types

Для примитивных типов перемещение не имеет практического отличия от копирования, так как примитивные типы не обладают свойством владения.

``` C++
int a = 5;
int b = a;  // Copying
int c = std::move(a);  // Moving (but effectively this is also copying)
```


При этом при перемещении ресурса между пользовательскими типами, последовательно вызывается move assignment operator для всех нестатических полей.

---
### [Move constructor](https://ru.cppreference.com/w/cpp/language/move_constructor)

``` C++
T::T(T&& other) noexcept
```

Перемещающий конструктор принимает параметр по rvalue-ссылке (`T&&`), что позволяет ему перехватывать временные объекты и объекты, которые явно перемещаются.

Он передает владение ресурсами от `other` к новому объекту, оставляя `other` в состоянии, пригодном для уничтожения или присваивания.

``` C++
// example
DynamicArray(DynamicArray&& other) noexcept : 
	size_(other.size_), data_(other.data_) {
        other.size_ = 0;
        other.data_ = nullptr;
        std::cout << "Move constructor called" << std::endl;
    }

int main() {
    DynamicArray a(10);
    DynamicArray b(std::move(a)); // getting rvalue-ref from std::move() and calling the move constructor
	
	a.push_back(42); // it's okay, we can re-use moved object
    return 0;
}
```

 (!***бывший владелец должен оставаться все ещё валиден***!)

---
### [Move assignment operator](https://ru.cppreference.com/w/cpp/language/move_operator)

``` C++
T& T::operator=(T&& other) noexcept
```

``` C++
// example
MyClass& operator=(MyClass&& other) noexcept {
        if (this != &other) {
            delete data;
            // moving resource
            data = other.data;
            other.data = nullptr;
        }
        return *this;
    }
```

``` C++
MyClass a;
MyClass b;

b = std::move(a);  // calling move assignment operator
```

---
## Rule of five

*Правило пяти* - это принцип, который указывает на необходимость явного определения пяти специальных методов класса, если хотя бы один из них нетривиален.

Эти пять методов включают:

- Деструктор (Destructor)
- Конструктор копирования (Copy constructor)
- Копирующий оператор присваивания (Copy assignment operator)
- Перемещающий конструктор (Move constructor)
- Перемещающий оператор присваивания (Move assignment operator)

---
## [std::move](https://en.cppreference.com/w/cpp/utility/move)

`std::move` - это функция из стандартной библиотеки, которая преобразует объект к rvalue-ссылке.

Данное преобразование предоставляет возможность передавать возвращаемое значение в качестве аргумента в конструктор перемещения и применять перемещающий оператор присваивания.

``` C++
#include <type_traits>
template <typename T>
constexpr std::remove_reference_t<T>&& move(T&& t) noexcept {
	return static_cast<typename std::remove_reference<T>::type&&>(t);
}
```

- Функция принимает параметр `T&&`, который является универсальной ссылкой (forwarding reference).
	- здесь `Т&&` является универсальной ссылкой, так как `T` это шаблонный параметр

- Функция возвращает `typename std::remove_reference<T>::type&&`. 
	- `std::remove_reference<T>` "убирает" ссылку у переданного шаблонного параметра с [помощью](https://ru.cppreference.com/w/cpp/types/remove_reference) `std::remove_reference_t::type`, и добавив `&&`, преобразует аргумент (`t`) в rvalue-reference.


Имена [ссылочных переменных rvalue](https://ru.cppreference.com/w/cpp/language/reference "cpp/language/reference") являются значениями [lvalue](https://ru.cppreference.com/w/cpp/language/value_category "cpp/language/value category") и должны быть преобразованы в значения [xvalue](https://ru.cppreference.com/w/cpp/language/value_category "cpp/language/value category") для привязки к перегрузкам функций, которые принимают ссылочные параметры rvalue, поэтому [конструкторы перемещения](https://ru.cppreference.com/w/cpp/language/move_constructor "cpp/language/move constructor") и [перемещающий оператор присваивания](https://ru.cppreference.com/w/cpp/language/move_operator "cpp/language/move operator") обычно используют std::move.

---
## std::forward

Функция `std::forward` применяется при прямой передаче (perfect forwarding)

Прямая передача - это механизм, который позволяет сохранить тип и категорию значения аргументов при их передаче в другую функцию.

``` C++
template<typename T>
constexpr T&& forward(std::remove_reference_t<T>& arg) noexcept {
    return static_cast<T&&>(arg);
}

template<typename T>
constexpr T&& forward(std::remove_reference_t<T>&& arg) noexcept {
    static_assert(!std::is_lvalue_reference_v<T>, "Cannot forward an rvalue as an lvalue.");
    return static_cast<T&&>(arg);
}
```

данная функция имеет две перегрузки: для вызова с lvalue в качестве агрумента и для вызова с rvalue


---



2.5.   Понятие прямой передачи. Пример, когда необходимо использовать функцию std::forward. [EMC §5.1]

2.6.   Понятие rvalue-ссылок. Особенности инициализации rvalue-ссылок, разрешенные и запрещенные присваивания между нессылочными объектами, lvalue-ссылками и rvalue-ссылками.

2.7.   Правила продления жизни объектов с помощью ссылок. Правила присваивания между константными и неконстантными lvalue- и rvalue-ссылками.

2.12.  Ссылочные квалификаторы (reference qualifiers). Пример использования. [EMC §3.6] Особенности вызова неконстантных методов у временных объектов.

2.13.  Return Value Optimization и условия ее возникновения. Целесообразное и нецелесообразное использование std::move в функциях, где возможна RVO (пример: сложение двух матриц). [EMC §5.3]
