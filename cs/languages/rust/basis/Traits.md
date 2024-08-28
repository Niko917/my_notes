**Трейт (Trait):** - это абстрактный интерфейс, который определяет набор методов, которые могут быть реализованы типами, которые реализуют данный трейт.

Трейты частично реализуют ***полиморфизм***,за счёт того, что их реализацию можно определить для произвольного типа.


Trait-объекты - это способ реализации абстрактных типов данных

- использование trait-объектов позволяет группировать разные типы данных, организованные по каким-либо признакам и свойствам.

- ***Инкапсуляция*** с помощью Trait-объектов позволяет скрыть детали реализации от пользователя.

- ***Взаимозаменяемость Trait-объектов*** - это когда один и тот же код может работать с различными типами данных, реализующими определенный Trait.

- Трейты являются альтернативой интерфейсам в других языках, но обладают большей гибкостью.

---

## Implementation

- Трейты объявляются с помощью ключевого слова `trait`.

- Внутри трейта можно объявлять методы и ассоциированные типы

- Трейты могут иметь определение методов по умолчанию
	- тип, для которого будет реализован трейт по умолчанию будет иметь метод, определённый по умолчанию.


``` Rust

trait Summary {
    fn summarize(&self) -> String;

    fn summarize_author(&self) -> String {
        String::from("Unknown author")
    }
}

struct Article {
    title: String,
    author: String,
    content: String,
}

impl Summary for Article {
    fn summarize(&self) -> String {
        format!("Article: {}, by {}", self.title, self.author)
    }

    fn summarize_author(&self) -> String {
        format!("Author: {}", self.author)
    }
}
```


---

## Trait bounds

Trait bounds позволяет гарантировать, что generic-тип будет обладать определенными методами.

Ограничение по трейту используется для указания того, что тип должен реализовывать определенный набор поведений.

``` Rust
fn notify<T: Summary>(item: &T) {
    println!("Breaking news: {}", item.summarize());
}
```

*В данном случае аргумент функции T будет ограничен и может быть только одним из типов, реализующих 
трейт `Summary`.* 


при этом ограничения можно увеличивать:
``` Rust
fn notify<T: Summary + Display>(item: &T) {
    println!("Breaking news: {}, {}", item.summarize(), item);
}
```

В этом случае функция `notify` принимает ссылку на объект типа $T$, который реализует трейты `Summary` и `Display`.


$\triangle$ если же ограничений много, то лучше использовать ключевое слово ***where***

``` Rust
fn notify<T>(item: &T)
where
    T: Summary + Display + Something_else,
{
    println!("Breaking news: {}, {}", item.summarize(), item);
}
```

---

## Generic Traits

Обобщенные трейты позволяют определять трейты, которые могут работать с различными типами данных. Это достигается с помощью generic-параметров.

``` Rust 
trait Container<T> {
    fn new() -> Self;
    fn add(&mut self, item: T);
    fn contains(&self, item: &T) -> bool;
}

struct MyContainer<T> {
    items: Vec<T>,
}

impl<T> Container<T> for MyContainer<T> {
    fn new() -> Self {
        MyContainer { items: Vec::new() }
    }

    fn add(&mut self, item: T) {
        self.items.push(item);
    }

    fn contains(&self, item: &T) -> bool {
        self.items.contains(item)
    }
}
```


---

## Supertraits

Супертрейты - это концепция языка Rust, которая позволяет явно указывать, что трейт, который мы определяем, требует, чтобы типы, реализующие этот трейт, также реализовывали другой трейт. 

Это позволяет трейту зависеть от функциональности другого трейта.

Example:

``` Rust
use std::fmt;

trait PrettyPrint: fmt::Display + fmt::Debug {
    fn pretty_print(&self) {
        println!("Display: {}", self);
        println!("Debug: {:?}", self);
    }
}
```

В этом примере $PrettyPrint$ требует, чтобы типы, реализующие его, также реализовывали трейты $Display$ и $Debug$.

---

## trait 'Sized'

Sized - это trait, который используется для представления типов, размер которых известен на этапе компиляции.

по умолчанию все примитивные типы являются реализующими данный трейт, исключением являются [[Dynamically sized types && Exotically sized types]].

``` Rust
fn print_sized<T: Sized>(value: T) {
    println!("Value: {:?}", value);
}

fn main() {
    let x = 42;
    print_sized(x); // OK, i32 in Sized

    // let slice = &[1, 2, 3];
    // print_sized(slice); // ERROR, &[i32] is not in Sized
}
```


---

## Deref && Drop traits

---
### [Deref trait](https://doc.rust-lang.org/std/ops/trait.Deref.html)

*трейт Deref позволяет типам, реализующим данный трейт перегружать* *оператор разыменования `*`*

``` Rust
pub trait Deref {
    type Target: ?Sized;

    // Required method
    fn deref(&self) -> &Self::Target;
}
```

- `type Target` — это ассоциированный тип, который указывает на тип, к которому мы хотим получить доступ при разыменовании.


Типы из стандартной библиотеки Rust, реализующие данный трейт:
- `Box<T>`
- `Rc<T>`
- `Arc<T>`
- slices (`&[T]` && `&str`) 
- `Vec<T>`
- `RefCell<T>`
- `Cow<T>`
- ...


``` Rust
use std::ops::Deref;

struct MyPointer<T> {
    value: T,
}

impl<T> Deref for MyPointer<T> {
    type Target = T;

    fn deref(&self) -> &T {
        &self.value
    }
}

fn main() {
    let x = MyPointer { value: "Hello, world!" };
    println!("{}", *x); // Output: "Hello, world!"
}
```

- В неизменяемых контекстах, `*x` 
  (где `T` не является ссылкой) эквивалентно `*Deref::deref(&x)`.

---
### Automatic dereferencing

Если указанный выше тип реализует трейт `Deref`, то для него актуально автоматическое разыменование (automatic dereferencing).

Это означает, что Rust будет автоматически вызывать метод `deref` для получения ссылки на целевой тип, когда это необходимо.

Если же разработчику нужно нетривиальное разыменование для пользовательского типа,который не представлен в стандартной библиотеке ,то он сам прописывает условие через ассоциированный тип и функцию `deref`.

Вот как это работает:

1. **Ассоциированный тип `Target`**:
    
    - определяется ассоциированный тип `Target` в реализации трейта `Deref`.
	    - `Target` указывает, к какому типу необходимо получить доступ при разыменовании.
        
2. **Функция `deref`**:
    
    - Определяется функция `deref`, которая возвращает ссылку на ассоциированный тип `Target`.
	    - Этот метод определяет, как именно происходит разыменование.


### [Drop trait](https://doc.rust-lang.org/std/ops/trait.Drop.html)

Трейт Drop представлен в языке Rust для очистки памяти, занимаемой объектом, когда он выходит из области видимости.

``` Rust
pub trait Drop {
    // Required method
    fn drop(&mut self);
}
```

Вот как это работает на практике:

1. Когда объект выходит из области видимости, Rust сначала проверяет, реализует ли тип данного объекта трейт `Drop`.

2. Если трейт `Drop` реализован, вызывается метод `drop(&mut self)`.

3. После вызова `drop`, Rust генерирует "drop glue" для объекта, который затем рекурсивно вызывает деструкторы для всех полей этого объекта.

4. Этот процесс продолжается до тех пор, пока не будут вызваны деструкторы для всех полей, гарантируя, что все ресурсы будут освобождены.


"Drop glue" — это механизм, который гарантирует, что все поля типа будут корректно удалены, когда экземпляр типа выходит из области видимости.

"Drop glue" не требует явного определения программистом, так как он генерируется компилятором Rust.

``` Rust
struct MyStruct {
    data: Vec<i32>,
}

impl Drop for MyStruct {
    fn drop(&mut self) {
        println!("Dropping MyStruct with data: {:?}", self.data);
    }
}

fn main() {
    let my_struct = MyStruct { data: vec![1, 2, 3] };
}
```

В этом примере, когда `my_struct` выходит из области видимости, метод `drop` для `MyStruct` будет вызван автоматически, и выведется сообщение о том, что `MyStruct` был удален. 

- Компилятор Rust автоматически добавит код для вызова `drop`
   для поля `data`, чтобы вектор `Vec<i32>` также был корректно удален.

---

## [Ord trait](https://doc.rust-lang.org/std/cmp/trait.Ord.html) && [Eq trait](https://doc.rust-lang.org/std/cmp/trait.Eq.html)

В Rust, `Ord`, `Eq`, `PartialOrd`, и `PartialEq` — это трейты, которые определяют различные виды упорядоченности между объектами.

Для примитивных типов и типов из стандартной библиотеки Rust, таких как `i32`, `f64`, `String`, `Vec`, и других, трейты `Eq`, `PartialEq`, `Ord`, и `PartialOrd` уже определены.

=> определение данных трейтов необходимо только для пользовательских типов.

### [PartialEq trait](https://doc.rust-lang.org/std/cmp/trait.PartialEq.html)

`PartialEq` - Это трейт, который позволяет сравнивать экземпляры типа на равенство ($==$) и неравенство ($!=$).


``` Rust
pub trait PartialEq<Rhs = Self>
where
    Rhs: ?Sized, {
    
    // Required method
    fn eq(&self, other: &Rhs) -> bool;

    // Provided method
    fn ne(&self, other: &Rhs) -> bool { ... }
}
```

$\triangle$ Этот трейт позволяет проводить сравнения с использованием оператора равенства для типов, которые не имеют отношения полной эквивалентности.

- Например, для действительных чисел NaN != NaN, поэтому типы с плавающей запятой реализуют `PartialEq`, но не `Eq`. 

- Формально говоря, когда Rhs == Self, этот признак соответствует отношению частичной эквивалентности.

$\triangle$ Реализации должны гарантировать, что `eq` и `ne` согласуются друг с другом:  
  
- a != b $<=>$, когда !(a == b).


***Связь с маркерным трейтом`Eq`:***

- `Eq` — это маркерный трейт, который указывает, что все значения типа могут быть сравнены на равенство.
    
- Если тип реализует `Eq`, он также должен реализовать `PartialEq`.
    
- `Eq` не требует дополнительных методов, так как он просто указывает, что `PartialEq` реализован корректно для всех значений типа.

$\triangle$ [How can i compare 2 different types?](https://doc.rust-lang.org/std/cmp/trait.PartialEq.html#how-can-i-compare-two-different-types)


### [Eq trait](https://doc.rust-lang.org/std/cmp/trait.Eq.html)

Маркерный трейт Eq определяет отношение эквивалентности.

=> Для любого объекта `a` выполняются свойства:

- свойство рефлективности : $a == a$ 
- свойство симметричности : если $a == b$, то $b == a$
- свойство транзитивности : если $a == b$, $b == c$, то $a == c$

***Связь с трейтом `PartialEq`:***

- Трейт `Eq` является подмножеством `PartialEq`, который указывает, что каждый объект данного типа полностью эквивалентен самому себе.
	- Это означает, что тип полностью поддерживает равенство, и нет таких объектов, при которых сравнение на равенство возвращало бы `false`.


``` Rust
#[derive(Debug, PartialEq, Eq)]
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let p1 = Point { x: 1, y: 2 };
    let p2 = Point { x: 1, y: 2 };
    let p3 = Point { x: 3, y: 4 };

    println!("p1 == p2: {}", p1 == p2); // true
    println!("p1 == p3: {}", p1 == p3); // false
}
```


### [PartialOrd](https://doc.rust-lang.org/std/cmp/trait.PartialOrd.html) trait

`PartialOrd` — это трейт в Rust, который определяет частичную упорядоченность между объектами данного типа.


``` Rust
pub trait PartialOrd<Rhs = Self>: PartialEq<Rhs>
where
    Rhs: ?Sized, {
    
    // Required method
    fn partial_cmp(&self, other: &Rhs) -> Option<Ordering>;

    // Provided methods
    fn lt(&self, other: &Rhs) -> bool { ... } // less than
    fn le(&self, other: &Rhs) -> bool { ... } // less or eq
    fn gt(&self, other: &Rhs) -> bool { ... } // greater than
    fn ge(&self, other: &Rhs) -> bool { ... } // greater or eq
}
```

$\triangle$ Методы `lt`, `le`, `gt` и `ge` этого трейта могут быть вызваны с помощью операторов `<`, `<=`, `>` и `>=` соответственно.

$\triangle$ Методы этого трейта должны быть совместимы друг с другом и с методами `PartialEq`.

$\triangle$ Должны выполняться следующие условия:
1. `a == b` $<=>$ `partial_cmp(a, b) == Some(Equal)`.
2. `a < b` $<=>$ `partial_cmp(a, b) == Some(Less)`
3. `a > b` $<=>$ `partial_cmp(a, b) == Some(Greater)`
4. `a <= b` $<=>$ `a < b || a == b`
5. `a >= b` $<=>$ `a > b || a == b`
6. `a != b` $<=>$ `!(a == b)`.


$\triangle$ Derivable:

``` Rust
#[derive(PartialEq, PartialOrd)]
enum E {
    Top,
    Bottom,
}

assert!(E::Top < E::Bottom);
```


``` Rust
use std::cmp::Ordering;

#[derive(Eq)]
struct Person {
    id: u32,
    name: String,
    height: u32,
}

impl PartialOrd for Person {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(other))
    }
}
```

### [Ordering](https://doc.rust-lang.org/std/cmp/enum.Ordering.html) trait

Ordering в Rust - это enum, который используется трейтами `PartialOrd` и `Ord` для описания порядка объектов. 

Он имеет три варианта:

- **`Less`**: объект считается меньше другого.
- **`Equal`**: объекты равны.
- **`Greater`**: объект считается больше другого.

``` Rust
use std::cmp::Ordering;

let result = 1.0.partial_cmp(&2.0);
assert_eq!(result, Some(Ordering::Less));

let result = 1.0.partial_cmp(&1.0);
assert_eq!(result, Some(Ordering::Equal));

let result = 2.0.partial_cmp(&1.0);
assert_eq!(result, Some(Ordering::Greater));
```

### [Ord trait](https://doc.rust-lang.org/std/cmp/trait.Ord.html)

Трейт `Ord` в Rust является фундаментальным для типов, которые образуют полную упорядоченность.

Полная упорядоченность — это бинарное отношение на множестве, где каждая пара элементов множества может быть сравнена друг с другом.

``` Rust
pub trait Ord: Eq + PartialOrd {
    // Required method
    fn cmp(&self, other: &Self) -> Ordering;

    // Provided methods
    fn max(self, other: Self) -> Self
       where Self: Sized { ... }
    fn min(self, other: Self) -> Self
       where Self: Sized { ... }
    fn clamp(self, min: Self, max: Self) -> Self
       where Self: Sized + PartialOrd { ... }
}
```

Реализации должны быть согласованы с реализацией PartialOrd и обеспечивать соответствие max, min и clamp требованиям cmp:
  
- partial_cmp(a, b) == Some(cmp(a, b)). 

- max(a, b) == max_by(a, b, cmp) (обеспечивается реализацией по умолчанию).  

- min(a, b) == min_by(a, b, cmp) (обеспечивается реализацией по умолчанию).  


$\triangle$ Из вышеизложенного и требований PartialOrd следует, что $\forall$ a, b, c:  
  
- a < b || a == b || a > b истинно;  
- < является транзитивным: a < b, b < c  =>  a < c.

---

## [Allocator](https://doc.rust-lang.org/std/alloc/trait.Allocator.html) trait

В Rust трейт `Allocator` является частью стандартной библиотеки и предназначен для абстрагирования процесса выделения и освобождения памяти.

данный trait определяет несколько основных методов, которые должны быть реализованы для любого аллокатора:

- **allocate**: Выделяет блок памяти заданного размера и выравнивания.
    
- **allocate_zeroed**: Выделяет блок памяти заданного размера и выравнивания, заполняя его нулями.
    
- **deallocate**: Освобождает ранее выделенный блок памяти.
    
- **grow**: Увеличивает размер ранее выделенного блока памяти.
    
- **grow_zeroed**: Увеличивает размер ранее выделенного блока памяти, заполняя новые байты нулями.
    
- **shrink**: Уменьшает размер ранее выделенного блока памяти.

``` Rust
pub unsafe trait Allocator {
    // Required methods
    fn allocate(&self, layout: Layout) -> Result<NonNull<[u8]>, AllocError>;
    
    unsafe fn deallocate(&self, ptr: NonNull<u8>, layout: Layout);

    // Provided methods
    fn allocate_zeroed(&self, layout: Layout,)
     -> Result<NonNull<[u8]>, AllocError> { ... }
    
    unsafe fn grow(
	    &self,
        ptr: NonNull<u8>,
        old_layout: Layout,
        new_layout: Layout,
    ) -> Result<NonNull<[u8]>, AllocError> { ... }
    
    unsafe fn grow_zeroed(
        &self,
        ptr: NonNull<u8>,
        old_layout: Layout,
        new_layout: Layout,
    ) -> Result<NonNull<[u8]>, AllocError> { ... }
    
    unsafe fn shrink(
        &self,
        ptr: NonNull<u8>,
        old_layout: Layout,
        new_layout: Layout,
    ) -> Result<NonNull<[u8]>, AllocError> { ... }
    
    fn by_ref(&self) -> &Self
       where Self: Sized { ... }
}
```


---
### `Layout` [struct](https://doc.rust-lang.org/std/alloc/struct.Layout.html)

`Layout` является структурой из модуля `alloc::alloc`, которая описывает конкретный блок памяти:

- size - размер блока памяти в байтах, необходимый для выделения
	- Размер, округленный до ближайшего значения, кратного align, не превышает isize::MAX
- align - выравнивание блока памяти в байтах 
  (по степени двойки)

данная структура необходима в качестве входных данных для аллокатора.

``` Rust
use std::alloc::{Layout, alloc};

unsafe {
    let layout = Layout::new::<u64>(); // Create a Layout for the u64 type
    if layout.size() > 0 {
        let ptr = alloc(layout); // Allocate memory with the specified size and alignment
        // Use the allocated memory
        // ...
        // Free the memory
    } else {
        // Handle the case of zero size
    }
}
```

