Лексический анализ - это этап компиляции, на котором происходит преобразование препроцессированного исходного кода в поток токенов (`token stream`)

часть ошибок компиляции может быть предотвращена уже на этапе лексического анализа.

например ошибки связанные с неправильным написанием ключевых слов языка, некорректность констант и.т.д...

также на этом этапе начинают формироваться [таблицы символов](obsidian://open?vault=notes&file=cs%2Flangs%2FC%2B%2B%2Fbasis%2Fcompile%20time%2Fsymbol%20tables)

---
## Tokens

Грамматика языка определяется в терминах токенов - фрагментов исходного кода, которые не могут быть осмысленно разделены

Таким образом, **токен** - это строительный блок, **элементарная единица исходного кода**.

Токены можно распределить на несколько типов:

- `Keywords` 
- `Operators`
- `Separators`
- `Literals`
- `Identifiers`

например:

- `while` - keyword token
- `main` - Identifier token
- `-` - minus operator token
- `->` - dereference (arrow) operator token
- `'0'` - literal token


- Исходный код преобразуется в поток токенов с помощью *Лексического анализатора* (Лексера).

``` c++
int main() {
    int a = 5;
    return 0;
}
```

с помощью специальных флагов можно указать компилятору вывести поток токенов данного исходного файла

``` bash
clang -fsyntax-only -Xlang -dump-tokens main.cpp
```

``` bash
int 'int'        [StartOfLine]  Loc=<main.cpp:1:1>
identifier 'main'        [LeadingSpace]  Loc=<main.cpp:1:5>
l_paren '('             Loc=<main.cpp:1:9>
r_paren ')'             Loc=<main.cpp:1:10>
l_brace '{'        [LeadingSpace]  Loc=<main.cpp:1:12>
int 'int'        [StartOfLine] [LeadingSpace]  Loc=<main.cpp:2:5>
identifier 'a'        [LeadingSpace]  Loc=<main.cpp:2:9>
equal '='        [LeadingSpace]  Loc=<main.cpp:2:11>
numeric_constant '5'        [LeadingSpace]  Loc=<main.cpp:2:13>
semi ';'             Loc=<main.cpp:2:14>
return 'return'        [StartOfLine] [LeadingSpace]  Loc=<main.cpp:3:5>
numeric_constant '0'        [LeadingSpace]  Loc=<main.cpp:3:12>
semi ';'             Loc=<main.cpp:3:13>
r_brace '}'        [StartOfLine]  Loc=<main.cpp:4:1>
eof ''             Loc=<main.cpp:4:2>
```
