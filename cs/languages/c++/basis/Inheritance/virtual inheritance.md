## basics

Виртуальное наследование - это механизм языка, который позволяет избежать проблемы множественного наследования.

о каких проблемах идёт речь?

### Diamond problem

рассмотрим пример:

``` cpp
class Base {
public:
    int value;
    Base() : value(0) {}
};

class Derived1 : public Base {
    // ...
};

class Derived2 : public Base {
    // ...
};

class FinalDerived : public Derived1, public Derived2 {
    // ...
};
```

В этом случае объект `FinalDerived` будет содержать два экземпляра класса `Base` — один через `Derived1` и другой через `Derived2`. 

Это может привести к неоднозначности при доступе к членам `Base` из `FinalDerived`.

---
### how virtual inheritance solve it?

Виртуальное наследование позволяет создать только один экземпляр базового класса в иерархии наследования.

Для этого используется ключевое слово `virtual`:

``` cpp
class Base {
public:
    int value;
    Base() : value(0) {}
};

class Derived1 : virtual public Base {
    // ...
};

class Derived2 : virtual public Base {
    // ...
};

class FinalDerived : public Derived1, public Derived2 {
    // ...
};
```

Теперь `FinalDerived` будет содержать только один экземпляр `Base`.

---
### Initialisation of base class

Для корректной инициализации базового класса в `FinalDerived`, конструктор `Base` должен быть вызван явно:

``` cpp
class FinalDerived : public Derived1, public Derived2 {
public:
    FinalDerived(int v) : Base(v), Derived1(v), Derived2(v) {}
};
```


Здесь ключевой момент — явный вызов конструктора `Base` в списке инициализации конструктора `FinalDerived`.

Это гарантирует, что `Base` будет инициализирован только один раз, и именно с тем значением, которое вы передаете в конструктор `FinalDerived`.

---

## [Behaviour of virtual function in constructors and destructors.](https://isocpp.org/wiki/faq/multiple-inheritance#mi-vi-ctor-order)

### virtual functions in constructors

- Когда вызывается конструктор, объект создается поэтапно, начиная с базового класса и переходя к самому производному классу.

- В процессе создания объекта он еще не полностью сформирован, и тип объекта считается типом конструктора, который в данный момент выполняется.

рассмотрим это на следующем примере:

``` cpp
class Base {
public:
    Base() {
        foo(); // calls Base::foo()
    }
    virtual void foo() {
        std::cout << "Base::foo()" << std::endl;
    }
};

class Derived : public Base {
public:
    Derived() {
        foo(); // calls Derived::foo()
    }
    void foo() override {
        std::cout << "Derived::foo()" << std::endl;
    }
};

int main() {
    Derived d;
    return 0;
}
```

Output:

``` cpp
Base::foo()
Derived::foo()
```

---

### virtual functions in destructors

- Когда вызывается деструктор, объект разрушается в обратном порядке создания, начиная с самого производного класса и переходя к базовому классу. 

- В процессе разрушения объект считается типом деструктора, который в данный момент выполняется.

Также рассмотрим это на следующем примере:

``` cpp
class Base {
public:
    ~Base() {
        foo(); // calls Base::foo()
    }
    virtual void foo() {
        std::cout << "Base::foo()" << std::endl;
    }
};

class Derived : public Base {
public:
    ~Derived() {
        foo(); // calls Derived::foo()
    }
    void foo() override {
        std::cout << "Derived::foo()" << std::endl;
    }
};

int main() {
    Base* b = new Derived();
    delete b;
    return 0;
}
```

Output:

``` cpp
Derived::foo()
Base::foo()
```

---

## Pure virtual functions

*чисто виртуальная функция* - это основной механизм для создания абстрактного класса.

Чисто виртуальная функция объявляется в базовом классе с использованием синтаксиса `= 0` в конце объявления функции:

``` cpp
class Base {
public:
    virtual void pureVirtualFunction() = 0;
};
```

***Свойства:***

- Чисто виртуальные функции не могут иметь определение в базовом классе. 
	- Они служат только для определения интерфейса.

- Чисто виртуальные функции обязательны для переопределения в производных классах

- Класс, содержащий хотя бы одну чисто виртуальную функцию, является абстрактным и не может быть инстанцирован напрямую.

``` cpp
#include <iostream>

class Animal {
public:
    virtual void makeSound() = 0; // pure virtual func
};

class Dog : public Animal {
public:
    void makeSound() override {
        std::cout << "Woof!" << std::endl;
    }
};

class Cat : public Animal {
public:
    void makeSound() override {
        std::cout << "Meow!" << std::endl;
    }
};

int main() {
    // Animal animal; // Error: you can't create an instance of abstract class

    Dog dog;
    Cat cat;

    Animal* animals[2];
    animals[0] = &dog;
    animals[1] = &cat;

    for (Animal* animal : animals) {
        animal->makeSound(); 
    }

    return 0;
}
```

---

## [Standart](https://isocpp.org/wiki/faq/multiple-inheritance#mi-delegate-to-sister)
