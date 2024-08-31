## match

***match*** - мощная альтернатива switch в других языках 

```Rust
enum Direction {
    East,
    West,
    North,
    South,
}

fn main() {
    let dire = Direction::South;
    match dire {
        Direction::East => println!("East"),
        Direction::South | Direction::North => {
            println!("South or North");
        },
        _ => println!("West"),
    };
}
```



---
## match as an expression

так как ***match*** это выражение => Rust позволяет определять  переменные значениями выражений

```Rust
fn main() {
    let boolean = true;

    let binary = match boolean {
        true => 1,
        false => 0,
    };

    assert_eq!(binary, 1);

    println!("Success!");
}
```

---


## matches!

***matches*** - это макрос, который позволяет проверить соответствует ли значение заданному  шаблону.

matches! возвращает ***true / false***

```Rust
enum Direction {
    North,
    South,
    East,
    West,
}

fn main() {
    let direction = Direction::North;

    if matches!(direction, Direction::North) {
        println!("Going North");
    }
}
```

---

## binding operator '@'

данный оператор (@) **предназначен для создания переменной, которая будет содержать значение, соответствующее шаблону**, чтобы затем использовать данную переменную в коде

```Rust
    enum Message {
        Hello { id: i32 },
    }

    let msg = Message::Hello { id: 5 };

    match msg {
        Message::Hello {
            id: id_variable @ 3..=7,
        } => println!("Found an id in range: {id_variable}"),
        Message::Hello { id: 10..=12 } => {
            println!("Found an id in another range")
        }
        Message::Hello { id } => println!("Found some other id: {id}"),
    }

```


=> Указывая id_variable @ перед диапазоном 3..=7 мы фиксируем любое значение, соответствующее данному диапазону, а также проверяем соответствие значения данному паттерну 
