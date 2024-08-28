[Шаблоны](https://ru.cppreference.com/w/cpp/language/templates) - это механизм, позволяющий использовать одни и те же функции или классы для обработки данных разных типов.

``` C++
template <typename T>
```

--- 
## Introduction

Что компилятор делает, увидев ключевое слово template и следующее за ним определение функции?

Правильно, почти ничего или очень мало.

Дело в том, что сам по себе шаблон не вызывает генерацию компилятором какого-либо кода.
Да и не может он этого сделать, поскольку еще не знает, с каким типом данных будет работать данная функция.

Генерации кода не происходит до тех пор, пока функция не будет реально вызвана в ходе выполнения программы.

Компилятор также генерирует вызов реализованной функции и вставляет его туда, где находится *вызываемая* функция.


``` C++
#include <iostream>

template <typename T>
class stack {
	private:
		T st[MAX]; // array of any type
		int top;
	public:
		stack () {top = -1;}
		void push (T variable) {st[++top] = variable;}
		T pop () {return st[top--];}
}

int main () {
	stack<float> s_1;
	s_1.push(1111.1F);
	s_1.push(2222.2F);
	s_1.push(3333.3F);
	std::cout << "1: " << s_1.pop() << endl;
	std::cout << "2: " << s_1.pop() << endl;
	std::cout << "3: " << s_1.pop() << endl;
	
	stack<long> s2;
	s2.push(123123123L);
	s2.push(234234234L);
	s2.push(345345345L);
	cout << "1: " << s2.pop() << endl;
	cout << "2: " << s2.pop() << endl;
	cout << "3: " << s2.pop() << endl;
	return 0;
}
```

---
## Functions overloading


Перегрузка функций - это процесс создания нескольких функций с одним и тем же именем, но с разными параметрами. 

- Это позволяет вызывать одну и ту же функцию с разными типами данных, и компилятор выберет подходящую версию функции на основе типов аргументов.

``` c++
#include <iostream>
#include <string>

// General template function
template <typename T>
void print(T value) {
    std::cout << "Generic: " << value << std::endl;
}

// Overload for type int
void print(int value) {
    std::cout << "Specialized for int: " << value << std::endl;
}

// Overload for type std::string
void print(const std::string& value) {
    std::cout << "Specialized for std::string: " << value << std::endl;
}

int main() {
    print(42);            // Call the overloaded function for int
    print("Hello");       // Call the overloaded function for const char* (converted to std::string)
    print(std::string("World")); // Call the overloaded function for std::string
    print(3.14);          // Call the general template function

    return 0;
}
```

--- 
## Template classes

Использование шаблонов позволяет определить шаблонный класс, который можно инстанцировать с различными типами данных. 

Это позволяет писать обобщенный код, который может работать с любыми типами.

``` C++
template <typename T>
class Box {
private:
    T content;
public:
    void setContent(const T& newContent) {
        content = newContent;
    }

    T getContent() const {
        return content;
    }
};

int main() {
    Box<int> intBox;
    intBox.setContent(123);

    Box<std::string> stringBox;
    stringBox.setContent("Hello World");

    std::cout << intBox.getContent() << std::endl; // 123
    std::cout << stringBox.getContent() << std::endl; // Hello World

    return 0;
}

```

---

## Template specialization

Специализация шаблонов (template specialization) — это возможность определить конкретную реализацию шаблона для определенного типа данных или набора типов данных.

---
### Full specialization

Полностью специализированные шаблоны определяют реализацию шаблона для конкретного типа данных.

Это означает, что шаблон будет работать только для указанного типа.

``` c++
template <typename T>
class MyClass {
public:
    void print(T value) {
        std::cout << "Generic: " << value << std::endl;
    }
};

// Specialization for int
template <>
class MyClass<int> {
public:
    void print(int value) {
        std::cout << "Specialized for int: " << value << std::endl;
    }
};
```

---

### Partial specialization

Частичная специализация позволяет определить реализацию шаблона для подмножества типов данных.

``` c++
template <typename T>
class MyClass {
public:
    void print(T value) {
        std::cout << "Generic: " << value << std::endl;
    }
};

// Partial specialization for pointers
template <typename T>
class MyClass<T*> {
public:
    void print(T* value) {
        std::cout << "Specialized for pointer: " << *value << std::endl;
    }
};
```

---

## Variadic templates

