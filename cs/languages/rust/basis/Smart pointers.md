Smart pointers - это мощный механизм во многих языках программирования для безопасного управления памятью.

Умные указатели представляют из себя структуры данных, работа которых схожа с работой указателей, но в то же время они обладают дополнительными метаданными и возможностями, по сравнению с обычными указателями и ссылками.

Rust с его концепцией владения и заимствования имеет дополнительное различие между ссылками и умными указателями.
- в то время, как ссылки только заимствуют данные, умные указатели часто _владеют_ данными, на которые указывают.

---

## `Box<T>`

Наиболее простой умный указатель - это _box_, тип которого записывается как `Box<T>`.

***Свойства:***
- используется для выделения области памяти на куче (heap).
	- на стеке хранится адрес выделенной области памяти на куче

- зачастую необходим при работе с [[Dynamically sized types]]
	- в частности при работе с trait-объектами необходимо их хранить в виде `Box<dyn Trait_name>`

- Единственное владение
	- Rust гарантирует, что только `Box<T>` владеет данными в любой момент времени
	- с помощью move-semantics `Box<T>` передает владение данными, а предыдущий владелец теряет к ней доступ
	- Когда `Box<T>` выходит из области видимости, выделенная память автоматически освобождается.

- Неизменяемость по умолчанию
	- попытка изменить данные по адресу выделенной памяти приведёт к противоречию концепции единственного владения.


### Случаи использования $Box<T>$


1. ***Хранение данных на куче***

``` Rust
let b = Box::new(5); // allocate memory on heap for i32 
```


2. ***Хранение рекурсивных типов***
	-  Рекурсивный тип - это тип, компонентой которого является сам этот тип
	- так как размер рекурсивного типа неизвестен на этапе компиляции, то необходимо хранить его в виде `Box<Rec_type>`

``` Rust
enum List {
    Cons(i32, Box<List>),
    Nil,
}

fn main() { let list = Cons(1, Box::new(Cons(2, Box::new(Cons(3, Box::new(Nil)))))); }
```


3. ***Передача владения на массивные данные***
	- при передачи массивных данных между функциями, удобно использовать `Box<T>`, так как в этом случае не будет происходить копирования передаваемых данных

``` Rust
fn process_large_data(data: Box<[i32; 1_000_000]>) {
    // something...
}

let large_data = Box::new([0; 1_000_000]);
process_large_data(large_data);
```


4. ***Хранение и взаимодействие с DST***
	- размер динамических типов данных неизвестен на этапе компиляции, а значит, что необходимо их "обернуть".

``` Rust
trait Draw {
    fn draw(&self);
}

struct Button;

impl Draw for Button {
    fn draw(&self) {
        println!("Drawing a button");
    }
}

let button: Box<dyn Draw> = Box::new(Button);
button.draw();
```


---

## `RC<T>` ([Reference counting](https://doc.rust-lang.org/alloc/rc/struct.Rc.html))

`Rc<T>` - это умный указатель, который предоставляет множественное владение объектом, благодаря счётчику ссылок.


- Счётчик ссылок внутри `Rc<T>` позволяет контролировать число владельцев данного объекта. 
	- при появлении нового владельца -> счётчик ссылок инкрементируется
	- при отсутствии владельцев объект очищается из кучи, а счётчик обнуляется.

- используется только в однопоточном сценарии

- является аналогом std::shared_ptr<> в C++

- Rc<> не входит в список автоматического импорта прелюдии и его необходимо отдельно подключать : `use std::rc::Rc;`


``` Rust
enum List {
    Cons(i32, Rc<List>),
    Nil,
}

use crate::List::{Cons, Nil};
use std::rc::Rc;

fn main() {
    enum List {
    Cons(i32, Rc<List>),
    Nil,
}

use crate::List::{Cons, Nil};
use std::rc::Rc;

fn main() {
    let a = Rc::new(Cons(5, Rc::new(Cons(10, Rc::new(Nil)))));
    println!("count after creating a = {}", Rc::strong_count(&a));
    
    let b = Cons(3, Rc::clone(&a));
    println!("count after creating b = {}", Rc::strong_count(&a));
    
    {
        let c = Cons(4, Rc::clone(&a));
        println!("count after creating c = {}", Rc::strong_count(&a));
    }
    
    println!("count after c goes out of scope = {}",Rc::strong_count(&a));
}
```

данный код наглядно показывает работу множественного наследования и состояние счётчика ссылок:

``` Rust
// OUTPUT

count after creating a = 1  
count after creating b = 2  
count after creating c = 3  
count after c goes out of scope = 2
```

---

## `Arc<T>` ([Atomic reference counting](https://doc.rust-lang.org/std/sync/struct.Arc.html))

`Arc<T>` - это потокобезопасный вариант `Rc<T>`. 

Он использует атомарные операции для управления счётчиком ссылок, что позволяет безопасно использовать его в многопоточном программировании.   

***Свойства:***
- **Множественное владение в многопоточном окружении**: `Arc<T>` обеспечивает множественное владение объектом типа `T`, которое выделено на куче. Вызов [`clone`](https://doc.rust-lang.org/std/clone/trait.Clone.html#tymethod.clone "method std::clone::Clone::clone") на `Arc` создает новый указатель `Arc`, хранящийся на стеке, адрес которого указывает на ту же область памяти на куче, что и исходный `Arc`, увеличивая при этом счетчик ссылок.
	- Когда последний указатель `Arc` на данную область памяти уничтожается, объект, хранящееся в этой области (часто называемое "внутренним значением"), также удаляется.

![Arc|550](Arc.png)

- **Атомарный счетчик ссылок**: `Arc<T>` использует атомарные операции для управления счетчиком ссылок, что обеспечивает безопасность в многопоточном окружении.

- **Неизменяемость**: также как и `Rc<T>`, `Arc<T>` обеспечивает неизменяемость данных. 
	- Для изменчивости в многопоточном окружении можно использовать `Mutex<T>` или `RwLock<T>` внутри `Arc<T>`.

``` Rust
use std::sync::Arc;
use std::thread;

fn expensive_computation() -> Arc<Vec<i32>> {
    Arc::new(vec![1, 2, 3, 4, 5])
}

fn main() {
    let cache = expensive_computation();

    let mut handles = vec![];

    for i in 0..5 {
        let cache_clone = Arc::clone(&cache);
        let handle = thread::spawn(move || {
            println!("Thread {} sees cache: {:?}", i, cache_clone);
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }
}
```

В этом примере, `cache` совместно используется пятью потоками. `Arc::clone` увеличивает счетчик ссылок атомарно, позволяя каждому потоку иметь доступ к кэшированным данным.

---

## Interior Mutability Pattern

Interior Mutability Pattern - это pattern проектирования в Rust, который позволяет изменять данные, даже при наличии неизменяемых ссылок на эти данные.
- Для изменения данных данный pattern использует unsafe код внутри структуры данных для обхода стандартных правил Rust, регулирующих изменяемость и заимствование

- Мы можем использовать типы, в которых применяется Interior mutability pattern, только если мы можем гарантировать, что правила заимствования будут соблюдаться во время выполнения, несмотря на то, что компилятор не сможет этого гарантировать. 
	- В этом случае `unsafe` код оборачивается безопасным API, и внешне тип остаётся неизменяемым.


$\triangle$ Данный pattern проектирования реализуется в Rust с помощью умного указателя RefCell<>.


---
## `RefCell<T>`

`RefCell<T>` - это умный указатель, который реализует Interior Mutability pattern, а именно предоставляет внутреннюю изменяемость.

В отличии от обычных ссылок и других умных указателей в языке Rust, `RefCell<T>`  позволяет изменять данные, находящиеся по адресу данного указателя. 

Внутренняя изменяемость `RefCell<T>` достигается за счёт проверки правил заимствования в процессе runtime, а не в процессе компиляции.


***Правила заимствования:***

- В любой момент времени может существовать только одна изменяемая ссылка (&mut T), либо любое количество неизменяемых ссылок (&T).

- Заимствования (ссылки) должны быть действительны в течении всего времени их использования.
	- => Ссылка не может пережить владельца объекта 


`RefCell<T>` не позволяет полностью обойти правила заимствования:
- средство проверки правил заимствования в компиляторе позволяет эту внутреннюю изменяемость, однако правила заимствования проверяются во время выполнения. 
- В случае нарушения правил, вместо ошибки компиляции вызовется `panic!`.


``` Rust
use std::cell::RefCell;

fn main() {
    let value = RefCell::new(42);

    {
        let mut value_borrowed = value.borrow_mut();
        *value_borrowed += 1;
    }

    {
        let value_borrowed = value.borrow();
        println!("Value: {}", value_borrowed);
    }
}
```


***Основные методы `RefCell<T>`:***

- `borrow()` - возвращает неизменяемую ссылку (`Ref<T>`) на объект.
- `borrow_mut()` - возвращает изменяемую ссылку (`RefMut<T>`) на объект.


Ещё один наглядный пример работы RefCell:

``` Rust
use std::cell::RefCell;

fn main() {
    let cell = RefCell::new(42);

    // taking Ref<T>
    let value = cell.borrow();
    println!("The value is: {}", *value); // Output: The value is: 42

    // taking RefMut<T>
    {
        let mut mut_value = cell.borrow_mut();
        *mut_value = 100;
    }

    let updated_value = cell.borrow();
    println!("The updated value is: {}", *updated_value);
    // Output: The updated value is: 100
}
```


---

## ## [Reference cycles](https://www.youtube.com/watch?v=rzYS7dwGrhA)

Циклические ссылки - это ситуация, когда 2 или более объектов ссылаются друг на друга.

Данная проблема может привести к утечкам памяти, ведь данные объекты никогда не будут уничтожены после выхода из их локального lifetime.


***Пример циклической ссылки:***


``` Rust
use std::rc::Rc;
use std::cell::RefCell;

struct Node {
	value: i32,
	next: RefCell<Option<Rc<Node>>>,
}

fn main() {
	let a = Rc::new(Node{
		value: 1,
		next: RefCell::new(None),
	});

	let b = Rc::new(Node{
		value: 2,
		next: RefCell::new(Some(a.clone())),
	})

	if let Some(ref mut next) = *a.next.borrow_mut() {
		*next = b.clone();
	}
}
```


В этом примере `a` и `b` ссылаются друг на друга через `Rc`, `RefCell`.

Данный случай порождает циклическую ссылку и объекты `a` и `b` не будут удалены даже при выходе из функции `main`. 


### [std::rc::Weak](https://doc.rust-lang.org/std/rc/struct.Weak.html)

Для решения проблемы циклических ссылок в Rust можно использовать `Weak` из модуля `std::rc`.


`std::rc::Weak` позволяет временно ссылаться на объект, при этом не инкрементируя счётчик ссылок.


``` Rust
pub struct Weak<T, A = Global>
where A: Allocator, T: ?Sized,{
/* private fields */
}
```



- `std::rc::Weak` представляет из себя "слабую" версию умного указателя `Rc<T>`. 

- данная структура данных хранит **невладеющую ссылку** на объект.

- возможно преобразовать `Weak<T>` до `Rc<T>` c помощью метода `upgrade()`.
	- это позволяет получить доступ к объекту, на который ссылался `Weak<T>`. 

	- данный метод возвращает `Option<Rc<T>>`>`
		- если объект, на который указывает `Weak<T>` ещё существует, то метод вернёт `Some(Rc<T>)`, иначе - вернёт `None`.
		
		- в случае, если `upgrade` вернёт `Some(Rc<T>)` "сильный" счётчик ссылок увеличится на единицу


``` Rust
use std::rc::{Rc, Weak};
use std::cell::RefCell;

struct Node {
    value: i32,
    next: Option<Rc<RefCell<Node>>>,
    prev: Option<Weak<RefCell<Node>>>,
}

impl Node {
    fn new(value: i32) -> Rc<RefCell<Node>> {
        Rc::new(RefCell::new(Node {
            value,
            next: None,
            prev: None,
        }))
    }
}

fn main() {
    // creating two nodes
    let node1 = Node::new(1);
    let node2 = Node::new(2);

    // linking nodes to doubly linked list
    node1.borrow_mut().next = Some(node2.clone());
    node2.borrow_mut().prev = Some(Rc::downgrade(&node1));

    // Now node1 && node2 are linked
    // node1 -> node2
    // node2 -> node1 (through the Weak)

    // cheching, that node2 can get access to the node1 through the Weak ref
    if let Some(prev_node) = node2.borrow().prev.as_ref().and_then(|weak| weak.upgrade()) {
        println!("Node2's previous node value: {}", prev_node.borrow().value);
    } 
    
    else {
        println!("Node2's previous node is gone.");
    }

    // deleting "strong ref" to node1
    drop(node1);

    // Now node1 is deleted, and Weak ref into node2 can't get upgraded anymore
    if let Some(prev_node) = node2.borrow().prev.as_ref().and_then(|weak| weak.upgrade()) {
        println!("Node2's previous node value: {}", prev_node.borrow().value);
    } 
    else {
        println!("Node2's previous node is gone.");
    };
}
```



**разумные случаи использования `std::rc::Weak`:**

- Иерархические структуры данных
	- При работе с иерархическими структурами данных, такими как деревья или графы, `Weak<T>` может использоваться для создания ссылок от дочерних узлов к родительским узлам.
		- Это позволяет избежать циклических зависимостей, которые могут возникнуть, если дочерние узлы будут хранить сильные ссылки на родительские узлы.

- Временное хранение ссылок
	- если есть необходимость хранить ссылки на объекты, но при этом важно, чтобы эти ссылки не влияли на время жизни объектов, то для этого можно использовать `Weak<T>`




