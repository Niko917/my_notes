## `Self` type

$\triangle$ `Self` - особый тип данных, который представляет ссылку на тип текущего объекта. (функциональный аналог this в C++)


```Rust
struct Node {
    elem: i32,
    // `Self` is a `Node` here.
    next: Option<Box<Self>>,
}
```


```Rust
struct Foo(i32);

impl Foo {
    fn new() -> Self {
        Self(0)
    }
}

assert_eq!(Foo::new().0, Foo(0).0);
```

 
```Rust
trait Example {
    fn example() -> Self;
}

struct Foo(i32);

impl Example for Foo {
    fn example() -> Self {
        Self(42)
    }
}

assert_eq!(Foo::example().0, Foo(42).0);
```


---

## `self` type

$\triangle$ `self` - это параметр, который представляет экземпляр данного конкретного типа.


зачастую используются 3 различных вида:

- `self`
- `&self`
- `&mut self`


на примерах разберём все 3 варианта использования:

1. `self`. В данном случае `self` позволяет обратится к текущему экземпляру типа

```Rust
fn foo() {}
fn bar() {
    self::foo()
}
```



2. `&self`. Методы в реализации классов в языке Rust требуют явной передачи (`self / &self / &mut self`) в качестве первого параметра. 

   
```Rust
struct MyStruct {
    value: i32,
}

impl MyStruct {
    // method that just read info
    fn read(&self) -> i32 {
        self.value
    }
}
```

В данном случае указывается в качестве параметра `&self`, чтобы явно указать, что это является методом, а не ассоциированной функцией.

---

## Associated functions vs methods 

$\triangle$ Ассоциативные функции - это функции, которое связаны с типом (структурой, перечислением), но не требуют наличие экземпляра типа для вызова.

Ассоциативные функции не имеет доступа к данным экземпляра типа, ведь она не работает с конкретным объектом.

example:

```Rust
struct MyStruct {
    value: i32,
}

impl MyStruct {
    // Associated function
    fn new(value: i32) -> MyStruct {
        MyStruct { value }
    }
}

fn main() {
    let my_instance = MyStruct::new(5);
}
```

- В этом примере `new` — это ассоциированная функция, которая создает новый экземпляр `MyStruct`. 
- Она не требует наличия уже существующего экземпляра `MyStruct`.



$\triangle$ Метод - это функция, которая связана с конкретным экземпляром типа и имеет доступ к данным этого экземпляра. 

- ***первым аргументом в метод всегда передаётся &self / &mut self.***


```Rust
struct MyStruct {
    value: i32,
}

impl MyStruct {
    // Method
    fn read(&self) -> i32 {
        self.value
    }
}

fn main() {
    let my_instance = MyStruct { value: 5 };
    let value = my_instance.read();
    println!("Read value: {}", value);
}
```


=> ***Основные отличия:***

- ***Требование экземпляра***
	- Ассоциированная функция не требует экземпляра типа для вызова
	- Метод требует экземпляр типа для вызова

- ***Доступ к данным***
	- ассоциированная функция не имеет доступа к данным экземпляра, так как она с ним не работает
	- Метод имеет доступ к данным конкретного экземпляра, так как он работает с ним.

- ***Синтаксис вызова***
	- Ассоциированная функция вызывается через тип: ```My_struct::new(5);```
	- Метод вызывается напрямую через экземпляр:
	  ```my_instance.read();```
