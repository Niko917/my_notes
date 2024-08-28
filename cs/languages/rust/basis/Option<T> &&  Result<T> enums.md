
## [Option< T >](https://doc.rust-lang.org/std/option/index.html)



каждый параметр $Option<T>$ является либо $Some(T)$ и содержит значение, либо является $None$ и не содержит его

```Rust
enum Option<T> {
    Some(T),
    None,
}
```


### cases:

***1.***  pattern matching ^8c8e1b

```Rust
fn divide(numerator: f64, denominator: f64) -> Option<f64> {
    if denominator == 0.0 {
        None
    } else {
        Some(numerator / denominator)
    }
}

// The return value of the function is an option
let result = divide(2.0, 3.0);

// Pattern match to retrieve the value
match result {
    // The division was valid
    Some(x) => println!("Result: {x}"),
    // The division was invalid
    None    => println!("Cannot divide by 0"),
}
```


***2.*** "Null" pointers

так как в языке Rust из-за контроля безопасности не существует null-pointers => указатель в Rust всегда должен указывать на действительную область памяти.

Несмотря на это, в языке Rust есть <mark style="background: #FF5582A6;">optional pointer</mark> . 
Это обёртка умного указателя $Box<T>$ в Option. 

Таким образом $Optional<Box<T>>$ является комбинацией 2-х типов: 
- $Optional<T>$
- $Box<T>$ 


```Rust
let optional = None;
check_optional(optional);

let optional = Some(Box::new(9000));
check_optional(optional);

fn check_optional(optional: Option<Box<i32>>) {
    match optional {
        Some(p) => println!("has value {p}"),
        None => println!("has no value"),
    }
}
```

Это позволяет указать, что объект, который мы обернули в Option может быть не действителен.

также перед обращением и использованием типа $T$, на который указывает $Box<T>$, необходимо проверить его валидность с помощью pattern matching.

```Rust

fn get_point() -> Option<Box<Point>> {
    // some condition that return 'None'
    if some_condition {
        None
    } else {
        Some(Box::new(Point { x: 10, y: 20 }))
    }
}

fn main() {
    let optional_point = get_point();

    match optional_point {
        Some(p) => println!("Point: ({}, {})", p.x, p.y),
        None => println!("There is no Point"),
    }
}
```


к тому же тип $Option<T>$ позволяет использовать множество [полезных  методов](https://doc.rust-lang.org/std/option/enum.Option.html#variants).

---

### [`&Option<T>` vs `Option<&T>`](https://www.youtube.com/watch?v=6c7pZYP_iIE)


---

## '?' operator 

? - оператор для [[match && pattern matching]] при работе с перечислениями Result и Option

Рассмотрим пример
```Rust
fn add_last_numbers(stack: &mut Vec<i32>) -> Option<i32> {
    let a = stack.pop();
    let b = stack.pop();

    match (a, b) {
        (Some(x), Some(y)) => Some(x + y),
        _ => None,
    }
}
```

данный код можно написать проще с помощью оператора '?' :

```Rust
fn add_last_numbers(stack: &mut Vec<i32>) -> Option<i32> {
	return Some(stack.pop()? + stack.pop()?);
}
```

В этом упрощенном варианте, если `stack.pop()` возвращает `None`, то `None` будет автоматически возвращено из функции `add_last_numbers`. 

Если же `stack.pop()` возвращает $Some(x)$, то оператор '?' вернёт $x$ 


---

## [Result< T >](https://doc.rust-lang.org/std/result/enum.Result.html)


В разных языках программирования используются такие объекты как ***nullptr***, ***nil***, ***undefined***. 

Однако в Rust это заменяется с помощью ***Result generic enum***.

Данное перечисление предназначено для работы с потенциально опасными операциями и объектами.

```Rust
// T and E are generics. T can contain any type of value, E can be any error.
enum Result<T,E> {
	Ok(T), // contains the success value
	Err(E), // contains the error value
}
```


---
## Methods (Result<T,E>)

1.  ***is_ok() && is_err()***
	- is_ok() : возвращает **true** в случае, если результат является Ok(T)
	- is_err() : возвращает **true** в случае, если результат является Err(E)


2. ***unwrap()  / unwrap_or(default: T) / unwrap_or_else(f: F)***
	- unwrap() : **возвращает параметр T** под оберткой Ok(T), если он есть, ***иначе паникует***
	
	- unwrap_or(default: Alt) : ***возвращает параметр T внутри Ok(T)***, если он есть, ***иначе возвращает generic-параметр по умолчанию (Alt)***
	
	- unwrap_or_else(f: F) : ***возвращает параметр T внутри Ok(T)***, если он есть, ***иначе вызывается функция f и возвращается её результат*** 


4. ***map(f: F) / map_err(f: F)***
	- map(f: F) : ***применяет функцию f к параметру T внутри Ok(T)***, если он есть, ***и возвращает новый Result***
	
	- map_err(f: F) : ***применяет функцию f к параметру E*** внутри Err(E), если он есть, ***и возвращает новый*** Result

---

## Examples (Result<T,E>)

1. 
```Rust
fn divide(a: i32, b: i32) -> Result<i32, &'static str> {
    if b == 0 {
        Err("Division by zero")
    } else {
        Ok(a / b)
    }
}

fn main() {
    let result = divide(10, 0);
    
    if result.is_ok() {
        println!("Result: {}", result.unwrap());
    } else {
        println!("Error: {}", result.unwrap_or_else(|_| "Default error message"));
    }
    
    let mapped_result = result.map(|v| v * 2);
    match mapped_result {
        Ok(value) => println!("Mapped result: {}", value),
        Err(e) => println!("Mapped error: {}", e),
    }
    
    match result {
        Ok(value) => println!("Result: {}", value),
        Err(e) => println!("Error: {}", e),
    }
}
```


2. 
```Rust
fn main() {
    let result = divide(10, 0);
    
    if result.is_ok() {
        println!("Result: {}", result.unwrap());
    } else {
        println!("Error: {}", result.unwrap_or_else(|_| "Default error message"));
    }
    
    let mapped_result = result.map(|v| v * 2);
    match mapped_result {
        Ok(value) => println!("Mapped result: {}", value),
        Err(e) => println!("Mapped error: {}", e),
    }
}
```


3. 
```Rust 
fn main() {
    let result = divide(10, 2)
        .map(|v| v * 2)
        .and_then(|v| divide(v, 2));
    
    match result {
        Ok(value) => println!("Final result: {}", value),
        Err(e) => println!("Final error: {}", e),
    }
}
```


4. 
```Rust
fn read_file() -> Result<String, std::io::Error> {
    std::fs::read_to_string("file.txt")
}

fn process_file() -> Result<(), std::io::Error> {
    let content = read_file()?;
    println!("File content: {}", content);
    Ok(())
}

fn main() {
    if let Err(e) = process_file() {
        println!("Error processing file: {}", e);
    }
}
```

***в случае перечисления Result<T,E> оператор '?' достаёт параметр T из Ok(T) и возвращает его***, в ином случае оператор вернёт ошибку Err(E)


---

