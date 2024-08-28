***Dynamically sized types (DST)*** - это важная концепция в языке Rust, которая позволяет работать с типами данных , размер которых неизвестен на этапе компиляции кода, либо не может быть известен вовсе.

---

## Dynamically sized types

DST - это типы данных, размер которых не известен на этапе компиляции

к DST принадлежат такие типы, как:
- slices ()
- trait-объекты



***Особенности***:

- Невозможность прямого использования:
	- DST не могут быть сохранены или переданы в функцию напрямую.
		- ***DST всегда используются через ссылки или smart pointers***

- Метаданные
	- DST требуют дополнительные метаданные для хранения информации о размере. 
		- метаданные для срезов - длина среза
		- метаданные для trait-объектов - ссылка на vtable




---

## Slices

``` Rust
let array = [1, 2, 3, 4, 5];
let slice: &[i32] = &array[1..3]; // Slice [2, 3]

let string = "Hello, world!";
let string_slice: &str = &string[7..]; // Slice "world!"
```

***Почему срезы являются DST?***
- Срезы не имеют статического размера, так как длина среза может меняться в зависимости от того, какой размер массива или строки они представляют.
	- поэтому slices всегда определяются через ссылку 
	  `&[T]`


--- 

## Trait-objects

Trait-объекты — это DST, которые позволяют работать с типами, реализующими определенный trait, без знания конкретного типа во время компиляции. 
- Trait-объекты создаются с использованием ссылок или умных указателей, таких как `&dyn Trait` или 
  `Box<dyn Trait>`.

``` Rust
trait Draw {
    fn draw(&self);
}

struct Circle;

impl Draw for Circle {
    fn draw(&self) {
        println!("Drawing a circle");
    }
}

struct Square;

impl Draw for Square {
    fn draw(&self) {
        println!("Drawing a square");
    }
}

fn main() {
    let shapes: Vec<Box<dyn Draw>> = vec![
        Box::new(Circle),
        Box::new(Square),
    ];

    for shape in shapes {
        shape.draw();
    }
}
```

***Почему trait-объекты являются DSTs:***

- Trait-объекты не имеют статического размера, так как они могут представлять любой тип, реализующий данный trait.
	- поэтому trait-объекты используются через ссылки и умные указатели, которые содержат метаданные - указатель на vtable (vptr).
		- `let circle: Box<dyn Draw> = Box::new(Circle);` 
			- // (circle is a ptr to trait-object)


---

## Использование DST вместе с generics

DST могут быть предоставлены в качестве аргументов типа для параметров generics, имеющих специальную 
`?Sized` привязку.

- Это позволяет использовать DSTs в generic-типах и функциях.

``` Rust
fn print_slice<T: ?Sized>(slice: &[T]) {
    for item in slice {
        println!("{}", item);
    }
}

fn main() {
    let array = [1, 2, 3, 4, 5];
    print_slice(&array[1..3]); // Output: 2, 3
}
```


---

