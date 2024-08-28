Модуль - способ организации и структурирования кода, который позволяет группировать связанные по какому-либо признаку части кода, а также управлять видимостью и повторно использовать код.

по концепции понятие модуля очень схоже с понятием namespace в C++.


$\triangle$ каждый модуль создаёт изолированную область видимости.


example:

```Rust
mod my_module {
	pub fn say_hello() {
		println!("Hello!");
	}
}

fn main() {
	my_module::say_hello();
}
```


модули также могут быть вложенными:

```Rust
mod outer_module {
    pub mod inner_module {
        pub fn say_hello() {
            println!("Hello from inner_module!");
        }
    }
}

fn main() {
    outer_module::inner_module::say_hello();
}
```

---

## mod prelude 

$\triangle$ `Prelude` - это набор основных элементов языка, которые автоматически импортируются в каждый модуль.

например из стандартной библиотека Rust автоматически импортируются такие структуры данных как Vec, Option, Result, ...

$\triangle$ Также можно создать собственный модуль `prelude`, в который добавить набор объектов, которые используются в нескольких крейтах.



example:

```Rust
pub mod prelude {
	pub use super::my_module::say_hello();
	pub PI = 3.14;
}

fn main() {
	use prelude::*
	// '*' means that we extract all from this module
	say_hello();
}
```