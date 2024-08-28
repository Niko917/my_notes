
ключевое слово ***enum*** позволяет создать тип данных, который представляет из себя набор возможных вариантов

```Rust
enum Direction {
    North,
    South,
    East,
    West,
}
```


также каждое поле перечисления можно инициализировать явно и указывать тип
```Rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```


для перечисления можно создавать методы с помощью ключевого слова ***impl***
```Rust
impl Message {
    fn call(&self) {
        match self {
            Message::Quit => println!("Quit"),
            Message::Move { x, y } => println!("Move to x: {}, y: {}", x, y),
            Message::Write(text) => println!("Write: {}", text),
            Message::ChangeColor(r, g, b) => println!("Change color to R: {}, G: {}, B: {}", r, g, b),
        }
    }
}
```


теперь можно вызывать метод call() для любого поля данного перечисления: 
```Rust
let msg = Message::Write(String::from("Hello, world!"));
msg.call();
```


---

